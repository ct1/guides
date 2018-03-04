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
deactivate
```

Alternatively, the admin user ca be created interactively
```
python manage.py createsuperuser
deactivate
```


