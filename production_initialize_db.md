# Initialize database

A installation guide to initialize postgresql database

----------

### 1. Backup development database
In your development machine dump (backup) your development database
  ```
  cd ~/dev/<projname>/<projname>/py
  pg_dump <dbname> > dbexport.pgsql
  ```
Transfer database dump to production
  ```
  git add .
  git commit -m 'db export'
  git push production master
  ``` 


### 2. Login to production server
  ```
  ssh root@<production-server-ip-address>
  ``` 

### 3. Initialize database
Ensure an empty destination database. Disconnect all conections from production database (e.g. pgAdmin). Drop the existing database. The empty database should be created using "template0" as the base.
  ```
  su - postgres
  dropdb <dbname>
  createdb -T template0 <dbname>

  cd /var/www/<projname>/src/py
  psql <dbname> < dbexport.pgsql
  exit
  ``` 

### 4. Manually create media folder
* Static files are files that you want to serve as part of the site (usually CSS, JavaScript and images).
* Media files are files that users upload to the application.
* Therefore, collectstatic will ignore media files.
After database import, media files have to be collected manually
  ```
  mv /var/www/<projname>/src/media /var/www/<projname>
  ``` 
Ensure media folder is included in apache configuration file

### 5. Restart apache2
  ```
  sudo service apache2 restart
  ``` 
