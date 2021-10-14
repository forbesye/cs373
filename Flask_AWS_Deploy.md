# How to deploy your Flask app to AWS

I'm going to show how to:
- Set up a skeleton Flask app with Ngnix and uWSGI
- Create a Docker image for development and production
- Deploy it all to AWS Elastic Beanstalk
- Configure TLS/SSL and domain name
- Set up automatic deployment on GitLab pipelines for develop and main branches

All of the code for the this test API can be seen here: https://gitlab.com/forbesye/test-api

I shut down the instance that I created because I didn't want to exceed the AWS free tier, which only allows for one single EC2 instance per month.

## Flask app with Ngnix and uWSGI

In your `back-end` folder, create the following files:

An `app.py` which is the base of your Flask app

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello_world():
    return '<img src="https://i.kym-cdn.com/photos/images/original/001/211/814/a1c.jpg" alt="cowboy" />'

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000, debug=True)
```

Note: You will obviously need more stuff, like SQLAlchemy and dotenv

Next we need to add [nginx](https://www.nginx.com/) and [uWSGI](https://uwsgi-docs.readthedocs.io/en/latest/). The two work in conjunction to serve as a load balancer, and allow your deployment app to have multiple instances of your Flask app running to increase throughput of requests.

Add a `nginx.conf` file. This is a default config I found online:

```
user www-data;
worker_processes auto;
pid /run/nginx.pid;

events {
    worker_connections 1024;
    use epoll;
    multi_accept on;
}

http {
    access_log /dev/stdout;
    error_log /dev/stdout;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    index   index.html index.htm;

    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  localhost;
        root         /var/www/html;

        location / {
            include uwsgi_params;
            uwsgi_pass unix:/tmp/uwsgi.socket;
            uwsgi_read_timeout 1h;
            uwsgi_send_timeout 1h;
            proxy_send_timeout 1h;
            proxy_read_timeout 1h;
        }
    }
}
```

Then add a `uwsgi.ini` file. Again, this is a default configuration:

```
[uwsgi]
module = app:app
uid = www-data
gid = www-data
master = true
processes = 5


socket = /tmp/uwsgi.socket
chmod-sock = 664
vacuum = true

die-on-term = true
```

Then add a `start.sh` file. This is run by your production Docker image to get everything started:
```bash
#!/usr/bin/env bash
service nginx start
uwsgi --ini uwsgi.ini
```

Finally add a `requirements.txt` file. This will need to be modified as you add more Python packages to your project:
```
Flask==1.1.2
uWSGI==2.0.19.1
```

## Create a Docker image for development and production

I'm going to create two Dockerfiles, `dev.Dockerfile` for development purposes, and `Dockerfile` for production. The main differences are that the development Dockerfile explicitly runs `app.py` on port 5000, while the production runs the `start.sh` file we created earlier on port 80.

dev.Dockerfile:
```dockerfile
FROM ubuntu:latest
ENV DEBIAN_FRONTEND=noninteractive

COPY ./badproxy /etc/apt/apt.conf.d/99fixbadproxy

RUN apt-get clean && sudo apt-get update
RUN apt-get install -y python3
RUN apt-get install -y python3-pip python3-dev build-essential vim
RUN apt-get install -y libmysqlclient-dev libpq-dev postgresql

COPY . usr/src/backend
COPY requirements.txt usr/src/backend/requirements.txt

WORKDIR /usr/src/backend

RUN pip3 install --upgrade pip
RUN pip3 install -r requirements.txt

EXPOSE 5000

CMD ["python3", "app.py"]
```

Dockerfile:
```dockerfile
FROM ubuntu:latest
ENV DEBIAN_FRONTEND=noninteractive

# https://forums.docker.com/t/hash-sum-mismatch-writing-more-data-as-expected/45940/3
# Uncomment this line and follow this if you have the same issue
COPY ./badproxy /etc/apt/apt.conf.d/99fixbadproxy

RUN apt-get update -y
RUN apt-get install -y python3
RUN apt-get install -y python3-pip python3-dev build-essential vim
RUN apt-get -y install nginx

COPY . usr/src/backend
COPY requirements.txt usr/src/backend/requirements.txt

WORKDIR /usr/src/backend

RUN pip3 install --upgrade pip
RUN pip3 install -r requirements.txt

EXPOSE 80

COPY nginx.conf /etc/nginx
RUN chmod +x ./start.sh
CMD ["./start.sh"]
```

**Note**: I was facing some issues with the apt-get calls when building the Docker image, so I had to follow that badproxy fix that you can see above. This might not be needed for you if you can build it correctly without it.

Now you can test the Docker images to see if they work. There are three important flags to remember: `-p`, `-t`, and `-f`, these are for port, tag, and file respectively.

To build and run the development Docker image, run the following. These commands are for Linux/WSL/MacOS:

`sudo docker build -t flask-docker-dev -f dev.Dockerfile .`

```docker run -it -v `pwd`:/usr/backend -w /usr/backend -p 5000:5000 flask-docker-dev```

This will be viewable at `localhost:5000`.

To build and run the production Docker image:

`sudo docker build -t flask-docker .`

```docker run -it -v `pwd`:/usr/backend -w /usr/backend -p 80:80 flask-docker```

This will be viewable at `localhost`.

![Screen Shot 2021-10-13 at 11 51 01 AM](https://user-images.githubusercontent.com/8890739/137178077-6f38bbcc-d4f8-43d8-9713-0109600457e8.png)

Now we're ready to deploy this to AWS Elastic Beanstalk!

# Deploying to AWS Elastic Beanstalk

First, you need to install the AWS Elastic Beanstalk CLI. [Here's the guide on how to do so](https://github.com/aws/aws-elastic-beanstalk-cli-setup)

Once you got that installed, you need to get your AWS access key and secret access key. [You can get them by following this guide](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-appendix-sign-up.html)

***DO NOT PUSH THE SECRET KEY TO YOUR REPO!!!***

The rest is pretty straightforward once you have the CLI installed. I followed [this guide](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/docker.html) to get it onto AWS. I'll summarize it below:

You need to make sure you're in your `back-end` folder before you run all of this! Also make sure your `Dockerfile` is in the folder, as the CLI will look for it when you're creating your EB instance.

First run `eb init -p docker your-application-name` and follow all prompts, pasting your AWS access key and secret access key when prompted. The application name is fairly arbitrary, so have it as whatever you want.

Next run `eb local run --port 5000`. This should build and run your production Docker image, and make it available at `localhost:5000`. Use this to make sure everything is running as expected.

Next run `eb create environment-name`. This will do all the heavy lifting and get your Flask app and Docker image deployed on AWS. You can go to the console for your environment by running `eb console` and the deployment instance by running `eb open`. I would make the environment name something along the lines of `website-api-prod`, in case you want a development API instance, as we will cover below.

After a couple of minutes, your EB console should look something like this, and should be accessible at the `something.elasticbeanstalk.com` link.

![aws-console](https://user-images.githubusercontent.com/8890739/137181134-ca60b608-ff46-4ff7-9918-e2a40a84a713.png)

EDIT: I realized that EB's autogenerated `.gitignore` by default doesn't let you include your `/.elasticbeanstalk/config.yml` file, which is needed for CI/CD pipelines. I modified the `.gitignore` in the `back-end` folder to look like this:

```
# Elastic Beanstalk Files
.elasticbeanstalk/*
!.elasticbeanstalk/*.global.yml
!.elasticbeanstalk/config.yml # Note the change to this line
```

I don't foresee including this as an issue, as there's no sensitive information in this config file.

## Configure TLS/SSL and domain name

Now we want the make our API accessible at `https://api.website.com`. To do this, we need to get a TLS/SSL certificate from the AWS Certificate Manager, and then to assign it to our EB instance. Then we need to add the `api` CNAME record to the DNS to link it to the EB instance.

First, go to [AWS Certificate Manager](https://aws.amazon.com/certificate-manager/) and request a new certificate.  Make sure that the URL that you provide for your certificate is `api.website.com`:

![domain](https://user-images.githubusercontent.com/8890739/137238193-88cb00b7-fecb-4449-bfd5-8b10a0764e58.png)

I had to do `test-api.texasvotes.me` because I already got a certificate for `api.texasvotes.me`. Also make sure you're on the same AWS region as you are for your EB instance. For example, in the two screenshots above, I'm in Oregon (us-west-2) for AWS Certificate Manager and Elastic Beanstalk. This is important for linking the certificate to the EB instance.

The process is pretty straightforward and is very similar to what you did to get a TLS/SSL certificate in phase 1 with AWS Amplify if you used that. You will recieve a CNAME record with a long string for the host name and destination to use for verification of your domain name. Once your TLS/SSL certificate is provided (this should take a few minutes max). Your screen should look like this:

![TLS](https://user-images.githubusercontent.com/8890739/137238594-c4929ae9-1bb3-4a2d-98dc-fba3fa4c6bc3.png)

Next, go to your Elastic Beanstalk console. I followed this tutorial [here](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/configuring-https-elb.html). For our EB instance, we have a classic load balancer, so follow the steps for that. The configuration after following the directions should look like this before submission:

![load](https://user-images.githubusercontent.com/8890739/137238853-565546df-58d9-494b-96ff-5dfca495ae62.png)

**Note:** Make sure you hit the orange "APPLY" button at the bottom of the page after adding. Otherwise your changes will not be saved! This happened to me!

Finally, add a CNAME record to your DNS records. Where `api` is the host name, and your `something.elasticbeanstalk.com` URL is the destination, like so:

![image](https://user-images.githubusercontent.com/8890739/137239098-f25b1279-6ea0-42dd-8e09-05605db57703.png)

After a couple of minutes, you should be able to access your API at `api.website.com`!

We also want to include important environment variables like the database log in string, so that they are acessible for the Flask app in the EB instance. This can be done by going to Configuration > Software > Environment Properties:

![env-var](https://user-images.githubusercontent.com/8890739/137242717-f71b6ce6-2b70-4da7-8093-0d7d6146f774.png)

## Set up automatic deployment on GitLab pipelines for develop and main branches

Now we want our API to automatically deploy every time we push to develop or main. 

This is where my group faced the issue of having to pay money for two EB instances (development and production) per month for the ~2 months that we needed them up. Since we wanted to have a development API that we could work on during the week after submission of phase 2 and 3.

I never found out a way to have a development and production API be on the same EB instance, and have two different subdomains be able to reach it and divert the requests in the correct manner. I think this may be possible to do with more knowlege of Nginx and uWSGI. So if anyone knows how to do so, please let me know. My group decided that the ~$15 a month ($6 per person for the two months we had the extra EB instance up) was worth it as it allowed us more time in development, and made sure we didn't touch production during grading. This isn't a requirement, but I would talk between your group and decide if it's worth it.

Regardless, we can set up our GitLab CI/CD to use the EB CLI to deploy our app when we conditionally push to the main and possibly develop branch. First, like I mentioned previously, make sure you include the `!.elasticbeanstalk/config.yml` to the `.gitignore` in the `back-end` folder that is automatically created with `eb init`. 

Next, add a `deploy` stage to your `.gitlab-ci.yml`, as well as a `deploy_production` job that conditionally runs on the `main` branch only. Mine looks like this:

```yml
image: node:latest

stages:
  - deploy

deploy_develop:
  image: python:3.6-stretch
  only:
    variables:
      - $CI_COMMIT_BRANCH == "develop"
  stage: deploy
  before_script:
    - pip install awsebcli --upgrade --user
    - git checkout develop
  script:
    - cd back-end
    - /root/.local/bin/eb deploy test-api-dev

deploy_production:
  image: python:3.6-stretch
  only:
    variables:
      - $CI_COMMIT_BRANCH == "main"
  stage: deploy
  before_script:
    - pip install awsebcli --upgrade --user
    - git checkout main
  script:
    - cd back-end
    - /root/.local/bin/eb deploy test-api-prod
```

The `deploy_develop` job is optional here. If you do decide to have a development API environment, make sure to create a new EB environment within the same application. As you can see, I had a second environment called `test-api-dev`.

| API Instance | Optional | EB Application Name | EB Environment Name |
|--------------|----------|---------------------|---------------------|
| Production   | No       | website-api         | website-api-prod    |
| Development  | Yes      | website-api         | website-api-dev     |

Then, you need to add the AWS Access Key and Secret Access Key to your GitLab CI/CD settings, so that the pipeline runners can authenticate when they run. Go to Settings > CI/CD > Variables to add these. Also check to protect and mask these variables when you add them. It should look like this:

![Screen Shot 2021-10-13 at 9 37 31 PM](https://user-images.githubusercontent.com/8890739/137244119-55b45cd0-388f-442f-a188-607e8bcf308f.png)

Then, push your code to your develop/main branch and check if it works! Here's what my pipeline output looked like after a successful deployment:

![Screen Shot 2021-10-13 at 9 42 06 PM](https://user-images.githubusercontent.com/8890739/137244274-82ab7d93-0e6a-44bb-b623-52e88ace7bba.png)
