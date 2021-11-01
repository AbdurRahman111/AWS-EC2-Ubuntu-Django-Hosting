1. craete AWS instace, download keypair and store it for ssh login
2. in security group : allow http port anywhere, http, and custom port 8000
3. launch instance
4. add public ip into domain AAA variable
5. install python and create virtual env:
	sudo apt-get update
	sudo apt-get install -y python3-venv
	python3 -m venv env

6. activate virtual env, install req.txt packages
	source env/bin/activate

	pip install django==3.0.2

7. install nginx:
	sudo apt-get install nginx
   
   change the security group

   install gunicorn:
	pip install gunicorn

8. bind gunicorn with wsgi
	gunicorn --bind 0.0.0.0:8000 project_name.wsgi:application

	allow the host in settings.py
	sudo nano project_name/settings.py

	gunicorn --bind 0.0.0.0:8000 project_name.wsgi:application

   try to run :
	ip:8000


sudo systemctl reload nginx

To make it permanent running state
	sudo apt-get install supervisor


cd /etc/supervisor/conf.d/
	sudo touch gunicorn.conf

sudo nano gunicorn.conf
	[program:gunicorn]
	directory=/home/ubuntu/project
	command=/home/ubuntu/env/bin/gunicorn --workers 3 --bind unix:/home/ubuntu/project/app.sock project_name.wsgi:application
	autostart = true
	autorestart=true
	stderr_logfile= /var/log/gunicorn/gunicorn.err.log
	stdout_logfile= /var/log/gunicorn/gunicorn.out.log

	[group:guni]
	programs:gunicorn



sudo mkdir /var/log/gunicorn

sudo supervisorctl reread

sudo supervisorctl update

sudo supervisorctl status

sudo systemctl reload nginx

cd /etc/nginx/sites-available/

sudo nano default

## make some line into comments
include proxy_params;
proxy_pass http://unix:/home/ubuntu/project_name/app.sock;


sudo systemctl reload nginx


show statics:
===============
sudo nano default

location /static/{
	autoindex on;
	alias /home/ubuntu/final_test/static/;
	}


sudo systemctl reload nginx


to make HTTPS request:
===========================
1. sudo apt-add-repository -r ppa:certbot/certbot
2. sudo apt-get install python3-certbot-nginx
3. sudo certbot --nginx -d yourDomain.com -d www.yourDomain.com 

sudo systemctl reload nginx


