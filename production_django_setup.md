# Complete djanto project setup

Install python requirements and initiate database

----------

### 1. Install python requirements
Login to your production server.
```
ssh root@<production-server-ip-address>
``` 
Install python requirements
```
cd /var/www/<your-project>
source env/bin/activate
cd <your-project>

pip install -r ./requirements.txt
```

### 2. Create django database
```
python manage.py migrate auth
python manage.py makemigrations
python manage.py migrate --noinput
python manage.py collectstatic
```

### 3. Create postgresql superuser
Replace `superuser` with the settings of your  admin
```
echo "from django.contrib.auth import get_user_model; User = get_user_model(); User.objects.create_superuser(<superuser_name>, '<superuser_email>', '<superuser_password>')" | python manage.py shell
```

Alternatively, the admin user ca be created interactively
```
python manage.py createsuperuser
```

### 4. Test application
In case you have UFW firewall protecting your server. To allow for testing, allow acces to the port we'll be using
```
sudo ufw allow 8000
```
In case you don't have firewall protection the command produces an error. Ignore it and proceed

Test our project by starting up the django development server 
```
python manage.py runserver 0.0.0.0:8000
```
In your web browser, visit your server's domain name or IP address followed by :8000:
```
http://server_domain_or_IP:8000
```
You should see the our Django home page

Append /admin to the end of the URL in the address bar to check for the administrative login:

When you are finished exploring, hit CTRL-C in the terminal window to shut down the development server.

To finish, deactivate the virtual environment
```
deactivate
```

