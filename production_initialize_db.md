# Initialize database

A installation guide to initialize postgresql database

----------

### 1. Backup development database
In your development machine
  ```
  cd ~/dev/<projname>/<proj_name>/py
  pg_dump <dbname> > dbexport.pgsql
  ```
Transfer database dump to your production environment
  ```
  git add .
  git commit -m 'db export'
  git push production master
  ``` 

### 2. Login to production server
  ```
  ssh root@<production-server-ip-address>
  ``` 

### 2. Initialize database
Ensure an empty destination database. Drop the existing database. The empty database should be created using "template0" as the base.
  ```
  dropdb <dbname>
  createdb -T template0 <dbname>

  cd /var/www/<projname>/src/py
  psql <dbname> < dbexport.pgsql
  ``` 

### 3. Run cleaning scripts


### 4. Restart apache2
  ```
  sudo service apache2 restart
  ``` 
