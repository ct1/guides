# Production setup using Nginx, gunicorn

Install and configure postgis, django, gunicorn, nginx, supervisor


----------

### 1. Install python requirements
Login to your production server.
```
ssh root@<production-server-ip-address>
``` 
Ensure updated apt-get
```
sudo apt-get updated
```
Install, python, postgresql, nginx
```
sudo apt-get install python3-pip python3-dev libpq-dev postgresql postgresql-contrib nginx
```

### 2. Configure postgres

```
sudo -u postgres psql
```

```
postgres=# CREATE DATABASE advisor;
postgres=# CREATE USER myprojectuser WITH PASSWORD 'password';
postgres=# ALTER ROLE myprojectuser SET client_encoding TO 'utf8';
postgres=# ALTER ROLE myprojectuser SET default_transaction_isolation TO 'read committed';
postgres=# ALTER ROLE myprojectuser SET timezone TO 'UTC';
postgres=# GRANT ALL PRIVILEGES ON DATABASE advisor TO myprojectuser;
postgres=# \q
```

### 3. Create a Python Virtual Environment for the Project

Get project specific python requirement’s into a virtual environment
```
sudo -H pip3 install --upgrade pip
sudo -H pip3 install virtualenv

mkdir ~/myproject && cd ~/myproject
virtualenv env

source env/bin/activate
```
```
(env) .... $ pip install django gunicorn psycopg2-binary
```
The basic software for a django-postgresql project is installed. Create the project
```
(env) .... $ django-admin.py startproject myproject ~/myproject
```

### 4. Adjust the Project Settings

Modify settings.py
```
(env) .... $ nano ~/myproject/myproject/settings.py
```

```
...
ALLOWED_HOSTS = [ 'example.com', '203.0.113.5']
...
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'myproject',
        'USER': 'myprojectuser',
        'PASSWORD': 'password',
        'HOST': 'localhost',
        'PORT': '',
    }
}
...

STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'static/')
...

```

### 5. Complete Initial Project Setup

```
(env) .... $ ~/myproject/manage.py makemigrations
(env) .... $ ~/myproject/manage.py migrate
```
Create an administrative user for the project
```
(env) .... $ ~/myproject/manage.py createsuperuser
```
Collect all of the static content into the directory location configured
```
(env) .... $ ~/myproject/manage.py collectstatic
```
Test our your project by starting up the Django development server
```
(env) .... $ ~/myproject/manage.py runserver 0.0.0.0:8000
```
In the browser
```
http://server_domain_or_IP:8000
```
Append /admin to the end of the URL in the address bar, you will be prompted for the administrative username and password you created. Check the css styles are correct and test superuser login

When finished, stop the development server hitting CTRL-C


### 6. Testing Gunicorn's Ability to Serve the Project

```
(env) .... $ cd ~/dev/advisor
(env) .... $ gunicorn --bind 0.0.0.0:8000 myproject.wsgi
```
For gunicorn, advisor.wsgi is the entry point to the Django application. Inside of this file, a function called application is used to communicate with the application.
Test again in your web browser
```
http://server_domain_or_IP:8000
```
Append /admin to the end of the and confirm that the css styles are not correct. This occurs because gunicorn is only an application server and does not serve static files

We have finished configuring the django application. Leave virtual environment
```
(env) .... $ deactivate
```

### 7. Configure Nginx to Proxy Pass to Gunicorn

Now we need to configure Nginx to pass traffic to the process
Create and open a new server block in Nginx's sites-available directory
```
sudo nano /etc/nginx/sites-available/advisor
```
Insert the following code
```
server {
    listen 80;
    server_name <server_domain_or_IP>;

    location = /favicon.ico { access_log off; log_not_found off; }

    location /static/ {
        root /home/debian/static/;
    }

    location / {
        include proxy_params;
        proxy_pass http://unix:/home/debian/advisor/advisor.sock;
    }
}
```
- Block starts by start by specifying it should listen on the normal port 80 and that it should respond to our server's domain name or IP address.
- Ignore any problems with finding a favicon
- Tell nginx where to find the static assets in myproject/static
- Create a location block to match all other requests. Include the standard
proxy_params and pass the traffic to the socket created by Gunicorn

Save and close the file. Enable the file by linking it to the sites-enabled directory
```
sudo ln -s /etc/nginx/sites-available/myproject /etc/nginx/sites-enabled
```
If required use the flag –force

Check the Nginx configuration for syntax errors
```
sudo nginx –t
```
If no errors are reported, restart Nginx
```
sudo systemctl restart nginx
```
You should now be able to go to your server's domain or IP address to view your application. Confirm in the admin page that the css styles are correctly served

### 8. Installation and Setup of supervisor

Every time our machine boots we have to start gunicorn. We will do this using supervisor
```
sudo apt-get install supervisor
```
Restart it
```
sudo service supervisor restart
```
Now we need to configure supervisor
Create and open a configuration file in supervisord’s conf.d directory
```
sudo nano /etc/supervisor/conf.d/advisor.conf
```
and configure our project
```
[program:advisor]
command=/home/debian/advisor/env/bin/gunicorn --workers 3 --bind unix:/home/debian/advisor/advisor.sock django_proj.wsgi
directory=/home/debian/advisor/src/
autostart=true
autorestart=true
stderr_logfile=/home/debian/advisor/logs/advisor.err.log
stdout_logfile=/home/debian/advisor/logs/advisor.out.log
```
We start by defining a program with the name advisor This name will be used for commands such as
```
sudo supervisorctl start advisor
```
The directory directive line indicates a path from which the command will be run
Autostart tells the script to start on system boot and autorestart tells it to restart when it exists for some reason.

Save the file and execute
```
sudo supervisorctl reread
sudo supervisorctl update
```
To verify that everything is working, type
```
ps ax | grep gunicorn
```
You should see several gunicorn processes running. Or, you can go to localhost:8000 and you will see your django app up and running.

Let's see the builtin supervisor web interface
Open up /etc/supervisor/supervisor.conf and place these lines at the beginning of the file
```
[inet_http_server]
port=0.0.0.0:9001
```
Save the file and reload supervisor
```
sudo supervisorctl reload
```
Open up your browser and go to 0.0.0.0:9001


