# Digital Ocean & Django Deployment Guide

A installation guide for getting Django setup on Digital Ocean

----------

### 1. Digital Ocean setup

1. Sign up for a Digital Ocean account (https://www.digitalocean.com/)

2. Login

3. Create Droplet

4. Under `Distributions` select `Debian` version `9.3 x64` or newer.

5. Choose a size in your budget

6. Choose a `datacenter region` likely one close to your target audience

7. Leave out SSH Key unless you know how to create one.

8. Finalize and create: 1 Droplet is fine

Let Droplet finish initializing. Check email for the following (unless you setup an SSH Key on step #7):

```
Droplet Name: <droplet_name>
IP Address: <ip_address>
Username: <username>
Password: <password>
``` 


#### 2. Connect to Digital Ocean server

1. In terminal([PuTTY](http://www.putty.org/) if on Windows) enter:
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

3. It should ask for your `<password>`, we received in our email (unless you setup an SSH Key)

4. It will ask for your `<password>` again so you can reset it. Set a `<new_password>` and then confirm it.
From now on, use this `<new_password>` with `ssh root@<ip_address>` to acccess your `Droplet`.
 

### 3. Setup postgresql in Digital Ocean [here](./production_postgresql.md)


### 4. Install other applications if required
zbar module requires the installation of development packages
```
 sudo apt-get install libzbar-dev python3-dev
``` 

### 5. Deploy your django project to Digital Ocean [here](./production_git.md)


### 6. Setup debian for django + apache [here](./debian_django_apache2.md)


### 7. Initialize database
1. Update Apache2 to your project's name/settings if needed.
    confirm 

2. Restart apache `sudo service apache2 restart`

3. Create django models in database
    ```
    cd /var/www/<your-project>
    source env/bin/activate
    python manage.py makemigrations
    python manage.py migrate
    python manage.py collectstatic
    ```
    Two options to create a database superuser
    ```
    # option 1
    python manage.py createsuperuser

    # option 2. Replace superuser (name, email, password) values and execute the command
    echo "from django.contrib.auth import get_user_model; User = get_user_model(); User.objects.create_superuser('superuser_name', 'superuser_email', 'superuser_password')" | python manage.py shell
    ```

4. Restart apache `sudo service apache2 restart`


### 8. Alternatively, you can FTP your local django project to Digital Ocean

1. Open an FTP Client (like Transmit or Cyberduck)

2. SFTP into your `<ip_address>` using `root` and your `password`

3. Navigate to `/var/www`

4. Navigate to `<your-project>/django_proj/settings/` and remove `local.py`

5. Open Terminal/PuTTY

6. SSH into your `<ip_address>` like `ssh root@<ip_address>` 

7. Update Apache2 to your project's name/settings if needed.
    confirm 

8. Restart apache `sudo service apache2 restart`

9. Create django models in database
    ```
    cd /var/www/<your-project>
    source env/bin/activate
    python manage.py makemigrations
    python manage.py migrate
    python manage.py collectstatic
    ```
    Two options to create a database superuser
    ```
    # option 1
    python manage.py createsuperuser

    # option 2. Replace superuser (name, email, password) values and execute the command
    echo "from django.contrib.auth import get_user_model; User = get_user_model(); User.objects.create_superuser('superuser_name', 'superuser_email', 'superuser_password')" | python manage.py shell
    ```

10. Restart apache `sudo service apache2 restart`

11. Navigate to your `<ip_address>` (or domain name) in your browser.

