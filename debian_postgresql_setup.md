# Setup postgresql for django in Digital Ocean

An installation guide for postgresql and postgis for django

----------


### 1. Install postgresql and postgis
	Connect to your remote server connect with ssh. In terminal([PuTTY](http://www.putty.org/) if on Windows) enter:
    ```
     # format
     ssh root@<ip_address>

     #example:
     ssh root@104.131.146.34
    ```

	Go to the project folder and execute commands:
    ```
    cd <projname>
    sudo apt-get update
    sudo apt-get install postgresql-9.6 postgresql-client-9.6 postgresql-9.6-postgis-2.3 -f
    ```

	Check the installation
    ```
    ps -ef | grep postgre
    ```
    You should see something like this on the terminal:
    ```
    postgres 32164     1  0 21:58 ?        00:00:00 /usr/lib/postgresql/9.4/bin/postgres -D /var/lib/   postgresql/9.4/main -c config_file=/etc/postgresql/9.4/main/postgresql.conf
    postgres 32166 32164  0 21:58 ?        00:00:00 postgres: checkpointer process
    postgres 32167 32164  0 21:58 ?        00:00:00 postgres: writer process
    postgres 32168 32164  0 21:58 ?        00:00:00 postgres: wal writer process
    postgres 32169 32164  0 21:58 ?        00:00:00 postgres: autovacuum launcher process
    postgres 32170 32164  0 21:58 ?        00:00:00 postgres: stats collector process
    ``` 

	Log as postgres and check psql is working
    ```
    su - postgres
    psql
    ```
    You should see something like this on the terminal:
    ```
    psql (9.6.6)
    Type "help" for help.

    postgres=#
    ``` 
    Leave psql
    ```
    \q
    ```

### 2. Create database and user
	Replace `projname` with the settings of your database
    ```
    dropdb <projname>
    dropuser <projname>user

    # create user and database
    psql -c "CREATE USER <userid> WITH PASSWORD 'password';"
    createdb --owner=<userid> -E utf8 <projname>

    # create postgis extension to handle geometry data
    psql -d <projname> -c "CREATE EXTENSION postgis;"

    # exit postgres
    exit
    ```

### 3. Create superuser
	Replace `superuser` with the settings of your  admin
    ```
    echo "from django.contrib.auth import get_user_model; User = get_user_model(); User.objects.create_superuser(<superuser_name>, '<superuser_email>', '<superuser_password>')" | python manage.py shell
    ```

    Alternatively, the admin user ca be created interactively
    ```
    python manage.py createsuperuser
    ```



NOTE - Data in creation is executed after the setup of django project
* python manage.py makemigrations
* python manage.py migrate
* python manage.py collectstatic
