# How to deploy your Flask app to AWS

I'm going to show how to:
- Set up a skeleton Flask app with Ngnix and uWSGI
- Create a Docker image for development and deployment
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

Finally I added a `requirements.txt` file. This will need to be modified as you add more Python packages to your project:
```
Flask==1.1.2
uWSGI==2.0.19.1
```

# Create a Docker image for development and production
