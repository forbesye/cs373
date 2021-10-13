# How to deploy your Flask app to AWS

I'm going to show how to:
- Set up a skeleton Flask app with Ngnix and uWSGI
- Create a Docker image for development and production
- Deploy it all to AWS Elastic Beanstalk
- Configure TLS/SSL and domain name

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

Note: I was facing some issues with the apt-get calls when building the Docker image, so I had to follow that badproxy fix that you can see above. This might not be needed for you if you can build it correctly without it.

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
