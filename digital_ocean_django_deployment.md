# Digital Ocean & Django Deployment Guide

A installation guide for getting Django setup on Digital Ocean

----------


### Digital Ocean Setup
1. Sign up for a Digital Ocean account (https://www.digitalocean.com/)

2. Login

3. Create Droplet

4. Under `Distributions` select `Debian` version `9.3 x64` or newer.

5. Choose a size in your budget

6. Choose a `datacenter region` likely one close to your target audience

7. Leave out SSH Key unless you know how to create one.

8. Finalize and create: 1 Droplet is fine

9. Let Droplet finish initializing

10. Check email for the following (unless you setup an SSH Key on step #7):

```
Droplet Name: <droplet_name>
IP Address: <ip_address>
Username: <username>
Password: <password>
``` 


#### Digital Ocean & Terminal/PuTTY

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
 

### Setup Local Django Project [here](./django_postgresql.md)

### Setup your Debian System for Django + Apache [here](./debian_django_apache2.md)


### SSH & Install postgresql

1. Open an FTP Client (like Transmit or Cyberduck)

2. SFTP into your `<ip_address>` using `root` and your `password`

3. sudo apt-get update

4. sudo apt-get install postgresql-9.6 postgresql-client-9.6 postgis

5. sudo apt-get install postgis

6. Check the installation
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

7. su - postgres

8. Logged as postgres. Check psql is working
     ```
     psql
     ```
     You should see something like this on the terminal:
     ```
     psql (9.4.2)
     Type "help" for help.

     postgres=#
     ``` 

8. Create database and user. Replace `projname` with the settings of your database
    ```
    dropdb <projname>
    dropuser <projname>user

    # create user and database
    psql -c "CREATE USER <projname>user WITH PASSWORD '<projname>user_pswd'; "
    createdb --owner=<projname>user -E utf8 <projname>

    # create postgis extension to handle geometry data
    psql -d <projname> -c "CREATE EXTENSION postgis;"
    ```

9. Run the following:
     ```
     cd /var/www/env/
     source bin/activate
     cd ../<your-project>
     python manage.py makemigrations
     python manage.py migrate
     ```



### Install other applications if required

10. If required zbar need to install development packages 
     ```
     sudo apt-get install libzbar-dev python3-dev
     ``` 


### FTP Local Django Project to Digital Ocean

11. Navigate to `/var/www`

12. Navigate to `<your-project>/django_proj/settings/` and remove `local.py`

13. Open Terminal/PuTTY

14. SSH into your `<ip_address>` like `ssh root@<ip_address>` 

15. Update Apache2 to your project's name/settings if needed.

10. Restart apache `sudo service apache2 restart`

11. Navigate to your `<ip_address>` (or domain name) in your browser.
