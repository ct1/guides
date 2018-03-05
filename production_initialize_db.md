# Initialize database

A installation guide to initialize postgresql database

----------

### 1. Backup development database
In your development machine dump (create a backup) your development database
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
Ensure an empty destination database. Drop the existing database. The empty database should be created using "template0" as the base.
  ```
  dropdb <dbname>
  createdb -T template0 <dbname>

  cd /var/www/<projname>/src/py
  psql <dbname> < dbexport.pgsql
  ``` 

### 4. Run cleaning scripts


### 5. Restart apache2
  ```
  sudo service apache2 restart
  ``` 
