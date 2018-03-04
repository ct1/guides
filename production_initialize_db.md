# Initialize database

A installation guide to initialize postgresql database

----------

### 1. Initialize database
Login to your production server.
  ```
  ssh root@<production-server-ip-address>
  ``` 

### 2. Initialize database
Initial content is created executing scripts in the production server (not in the other environments)

#### Create base geo tables
Go to your production server.
  ```
  ssh root@<production-server-ip-address>
  ``` 
Activate virtualenv, then go the production scripts folder.
Replace proj-name with your project name.
  ```
  cd /var/www
  source env/bin/activate
  cd <proj-name>/py/production
  ```
execute script and confirm output.
  ```
  ./1-loaddata_geo.sh
  ```
  You should see something like this on the terminal:
  ```
  Installed 323 object(s) from 1 fixture(s)
  ```
