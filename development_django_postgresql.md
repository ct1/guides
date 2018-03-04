# Create a local django + postgresql project

An implementation guide to create a local django + postgresql project

----------

*NOTE* Replace `projname` in all commands with the name of your project

1. Create Dev directory (storage for all local projects)
    ```
    cd ~/
    mkdir Dev && cd Dev
    ```

2. Create virtual environment.
First install virtual environment for python 3
    ```
    sudo apt-get install -y python3-venv
    ```
    Go to Dev
    ```
    cd ~/Dev
    ```
    Replace `projname` with the name of your project
    ```
    mkdir <projname> && cd <projname>
    # instead of 'virtualenv -p python3 env' use:
    python3 -m venv env

    # activate on Mac/Linux
    source env/bin/activate

    # activate on Windows
    .\Scripts\activate
    ```

3. Install django & start project
    ```
    pip install --upgrade pip
    pip install django
    pip install djangorestframework djangorestframework-filters
    pip install django-rest-auth django-cors-headers
    pip install django-admin-view-permission
    pip install psycopg2-binary
    pip install djangorestframework-jwt
    ```
    Replace `projname` with the name of your project
    ```
    mkdir src && cd src
    django-admin.py startproject --template=~/Dev/templates/django_base/django_base <projname> .

    # Windows (optional)
    .\Scripts\django-admin.py startproject --template=~/Dev/templates/django_base/django_base <projname> .
    ```

4. Create individual settings file
    ```
    cd ..
    # currently working in 
    # ~/Dev/<projname> on mac/linux
    # \Users\YourName\Dev\<projname> on windows
    ```
    Replace `projname` with the name of your project
    ```
    mkdir src/etc
    sed -e "s/{1}/<projname>/" ../templates/settings.txt > src/etc/my_settings.ini
    cp ../templates/local.py src/django_proj/settings
    ```

5. Modify django database settings
    ```
    # With your preferred text editor open file src/django_proj/settings/dev.py, go to the database section
    and modify the parameters NAME, USER, PASSWORD. Make sure they match values in etc/my_settings.ini
    ```
    Replace `projname` with the name of your project
    ```
    DATABASES = {
        'default': {
            'ENGINE' : 'django.contrib.gis.db.backends.postgis',
            #'ENGINE': 'django.db.backends.postgresql_psycopg2',
            'NAME': '<projname>',
            'USER' : '<dbusername>',
            'PASSWORD' : '<dbusername_password>',
            'HOST': 'localhost',
            'PORT': '',
        }
    }
    ```

6. Create postgresql database

    *NOTE* Replace db_user (name, password) and proj_db values
    for your case, before executing db commands. Make sure they match values you've defined in django settings
    ```
    # currently working in 
    # ~/Dev/<projname> on mac/linux
    # \Users\YourName\Dev\<projname> on windows
    ```
    Replace `projname` with the settings of your database
    ```
    dropdb <projname>
    dropuser <projname>user

    # create user and database
    psql -c "CREATE USER <projname>user WITH PASSWORD '<projname>user_pswd'; "
    createdb --owner=<projname>user -E utf8 <projname>

    # create postgis extension to handle geometry data
    psql -d <projname> -c "CREATE EXTENSION postgis;"
    ```

7. Some other installations:
    ```
    # currently working in 
    # ~/Dev/<projname> on mac/linux
    # \Users\YourName\Dev\<projname> on windows

    cd src

    # libraries defined in template requirements.txt
    pip install -r requirements.txt
    ```

8. Run migration and create superuser
    ```
    python manage.py migrate auth
    python manage.py makemigrations
    python manage.py migrate --noinput

    # option 1
    python manage.py createsuperuser

    # option 2. Replace superuser (name, email, password) values and execute the command
    echo "from django.contrib.auth import get_user_model; User = get_user_model(); User.objects.create_superuser('superuser_name', 'superuser_email', 'superuser_password')" | python manage.py shell
    ```

