# Debian + Apache2 + Django 

Setup Django, Apache2, Python Tools, and mod_wsgi on Debian Linux Systems. (Debian Version 9.4 & Up)


1. Install Python Tools, Apache2, and Mod_WSGI on your Debian Linux System

    ```
    sudo apt-get update

    sudo apt-get upgrade

    sudo apt-get install apache2

    sudo apt-get install python-pip python-virtualenv python-setuptools python-dev build-essential

    sudo apt-get install libapache2-mod-wsgi-py3
    ```

2. Start Virtualenv & Django Project.
We assume the project folder is already created and python project already deployed
    ```
    sudo pip install virtualenv 
    cd /var/www<projname>
    ```
    Create project folder and virtual environment
    ```
    virtualenv -p python3 env
    source env/bin/activate
    python --version    #should return Python 3.5
    ```

3. Setup Static & Media Root directories.
    ```
    mkdir static && mkdir media
    ```

4. Open `sudo nano /etc/apache2/sites-available/000-default.conf`

5. Change Apache2 configuration
    Replace `projname` with the name of your project
    ```
    <VirtualHost *:80>
        ServerName localhost
        ServerAdmin webmaster@localhost

        Alias /static /var/www/<projname>/static
        <Directory /var/www/<projname>/static>
           Require all granted
         </Directory>

        Alias /media /var/www/<projname>/media
        <Directory /var/www/<projname>/media>
           Require all granted
        </Directory>

        <Directory /var/www/<projname>/<projname>/settings>
            <Files wsgi.py>
                Require all granted
            </Files>
        </Directory>

        WSGIDaemonProcess <projname> python-path=/var/www/<projname> python-home=/var/www/<projname>/env
        WSGIProcessGroup <projname>
        WSGIScriptAlias / /var/www/<projname>/<projname>/settings/wsgi.py

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

    </VirtualHost>
    ```


6. Add User Permissions (to modify sqlite3 Database)
    ```
    chmod 664 ~/<projname>/db.sqlite3
    sudo chown :www-data ~/<projname>/db.sqlite3
    sudo chown :www-data ~/<projname>
    ```

7. Start, Stop, Restart Apache2
    ```
    # Restart in two different ways:
    sudo apachectl restart
    sudo service apache2 restart

    # Start Apache in two different ways:
    sudo apachectl start
    sudo service apache2 start

    # Stop Apache in two different ways:
    sudo apachectl stop
    sudo service apache2 stop
    ```

8. Setup Domain Name DNS records:
    Add your server's ip_address such as `45.79.183.218` as a `A` record for your domain name. Such as:

    | Type          | Host                |  Answer        |  TTL  |
    | ------------- |:-------------------:|:--------------:|:-----:|
    | A             | www.yourdomain.com  | 45.79.183.218  |  300  |
    | A             | blog.yourdomain.com | 45.79.183.218  |  300  |


9. Add Any/All Hosts to Django Settings:
    ```
    ALLOWED_HOSTS = ['45.79.183.218', 'www.yourdomain.com', 'blog.yourdomain.com']
    ```
    
    Add Static + Media Settings
    ```
    STATIC_URL = '/static/'
    STATIC_ROOT = '/var/www/<projname>/static/'
    MEDIA_URL = '/media/'
    MEDIA_ROOT = '/var/www/<projname>/media/'
    ```

10. Restart Apache (see Step 7)
