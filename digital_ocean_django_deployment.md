# Digital Ocean & Django Deployment Guide

A installation guide for getting Django setup on Digital Ocean

----------


### 1. Digital Ocean Setup

* Sign up for a Digital Ocean account (https://www.digitalocean.com/)

* Login

* Create Droplet

* Under `Distributions` select `Debian` version `9.3 x64` or newer.

* Choose a size in your budget

* Choose a `datacenter region` likely one close to your target audience

* Leave out SSH Key unless you know how to create one.

* Finalize and create: 1 Droplet is fine

Let Droplet finish initializing. Check email for the following (unless you setup an SSH Key on step #7):

```
Droplet Name: <droplet_name>
IP Address: <ip_address>
Username: <username>
Password: <password>
``` 


#### 2. Digital Ocean & Terminal/PuTTY

1. In terminal([PuTTY](http://www.putty.org/) if on Windows) and enter:
    ```
     # format
     ssh root@<ip_address>

     #example:
     ssh root@104.131.146.34
    ```

2. You should see a message like:
    ```
    The authenticity of host '104.131.146.34 (104.131.146.34)' can't be established.
    ECDSA key fingerprint is SHA256:Dg4o2zcwBMMx72IJEgdhJm/iDWtoQzWVmSuRV8B4db4.
    Are you sure you want to continue connecting (yes/no)?   
    ```
Type `yes` then hit `enter`

3. It should ask for your `<password>`, we recevied in our email (unless you setup an SSH Key)

4. It will ask for your `<password>` again so you can reset it. Set a `<new_password>` and then confirm it. From now on, use this `<new_password>` with `ssh root@<ip_address>` to acccess your `Droplet`.
 

### 3. Setup local django project [here](./django_postgresql.md)

### 4. Setup your debian system for django + apache [here](./debian_django_apache2.md)


### 5. SSH & install postgresql

1. Execute the following commands
    ```
    sudo apt-get update
    sudo apt-get install postgresql-9.6 postgresql-client-9.6 postgis
    sudo apt-get install postgis
    ```

2. Check the installation
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
    psql (9.6.2)
    Type "help" for help.

    postgres=#
    ``` 

Create database and user. Replace `projname` with the settings of your database
    ```
    dropdb <projname>
    dropuser <projname>user

    # create user and database
    psql -c "CREATE USER <projname>user WITH PASSWORD '<projname>user_pswd'; "
    createdb --owner=<projname>user -E utf8 <projname>

    # create postgis extension to handle geometry data
    psql -d <projname> -c "CREATE EXTENSION postgis;"
    ```

Run the following:
    ```
    cd /var/www/env/
    source bin/activate
    cd ../<your-project>
    python manage.py makemigrations
    python manage.py migrate
    ```



### 6. Install other applications if required

If required zbar need to install development packages 
    ```
     sudo apt-get install libzbar-dev python3-dev
    ``` 


### 7. FTP Local Django Project to Digital Ocean

+ Open an FTP Client (like Transmit or Cyberduck)

+ SFTP into your `<ip_address>` using `root` and your `password`

Navigate to `/var/www`

Navigate to `<your-project>/django_proj/settings/` and remove `local.py`

Open Terminal/PuTTY

SSH into your `<ip_address>` like `ssh root@<ip_address>` 

Update Apache2 to your project's name/settings if needed.

Restart apache `sudo service apache2 restart`

Navigate to your `<ip_address>` (or domain name) in your browser.

