# Setup postgresql for production

Configure PostgreSQL to allow remote connection and initialize data

----------

### 1. Install postgresql and postgis
Connect to your remote server connect with ssh. In terminal([PuTTY](http://www.putty.org/) if on Windows) enter:
```
 # format
 ssh root@<ip_address>

 #example:
 ssh root@104.131.146.34
```

Install postgresql and postgis
```
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
Replace `projname`, `userid` and `password` with the settings of your database
```
dropdb <projname>
dropuser <userid>

# create user and database
psql -c "CREATE USER <userid> WITH PASSWORD '<password>';"
createdb --owner=<userid> -E utf8 <projname>

# create postgis extension to handle geometry data
psql -d <projname> -c "CREATE EXTENSION postgis;"

# exit postgres
exit
```
Ensure these settings match the database settings on your django project

NOTE - Data in database is initialized later, after the setup of django project
* python manage.py makemigrations
* python manage.py migrate
* python manage.py createsuperuser
* python manage.py collectstatic


### 3. Configure PostgreSQL to allow remote connection
By default PostgreSQL is configured to be bound to “localhost”.

#### Configure postgresql.conf
First, find where is postgresql.conf. Its location varies with postgresql version.
  ```
  find /etc/postgresql -name "postgresql.conf"
  ```
  You should see something like this on the terminal:
  ```
  /etc/postgresql/9.6/main/postgresql.conf
  ``` 
Open postgresql.conf file.
  ```
  cd /etc/postgresql/9.6/main
  sudo nano postgresql.conf
  ``` 
Replace line
  ```
  # listen_addresses = 'localhost'
  ``` 
with
  ```
  listen_addresses = '*'
  ``` 

#### Configure pg_hba.conf
Open pg_hba.conf in the same folder
  ```
  sudo nano pg_hba.conf
  ``` 
Replace line
  ```
  local   all        all                peer
  ``` 
with
  ```
  #local   all        all                peer
  local   all        all                md5
  ``` 
Add also the following entries at the very end of the file.
Replace 0.0.0.0 by your local machine ip address (replace `0.0.0.0/0` by `your-local-ip-address/0`)
  ```
  host	all    all    0.0.0.0/0    md5
  host    all    all    ::/0         md5
  ``` 
The second entry is for IPv6 network.
“md5” means that client needs to provide a password. If you want client to connect without password change “md5” to “trust”.


Restart postgresql server.
  ```
  systemctl restart postgresql
  ``` 

#### Confirm service active
Check postgresql status
  ```
  systemctl status postgresql
  ```
  You should see something like this on the terminal:
  ```
  * postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor prese
     Active: active (exited) since Thu 2018-03-01 13:55:06 UTC; 4s ago
    Process: 16988 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
   Main PID: 16988 (code=exited, status=0/SUCCESS)

  Mar 01 13:58:04 debian-s-1vcpu-1gb-lon1 systemd[1]: Starting PostgreSQL 
  Mar 01 13:58:04 debian-s-1vcpu-1gb-lon1 systemd[1]: Started PostgreSQL R
  ``` 


### 4. Check remote access
Login to the client machine and check the remote connection.
Replace the ip-address with your production server ip-address
Replace database settings with your production database settings
or use the postgres user to check connection.
  ```
  psql -h 107.170.158.89 -U postgres

  Password for user postgres:
  psql (9.6.1, server 9.6.6)
  Type "help" for help.

  postgres=# \l
  ``` 
  You should be able to see list of databases.


### 5. Configure remote database in pgAdmin
1. Open pgAdmin 4 in your local machine.

2. Configure a remote server.
  You need to have a database created for your project.
  Use the database-name and database-username used in step 1.
  ```
  On the browser tree on the left, right-click on servers
  Select create server
  On the server window:
  	in 'general' tab introduce name 'myproj_production'
  	in 'connection' tab
  		introduce host <production server ip-address>
  		introduce maintenance database <database-name>
  		introduce Username <database-username>
  ``` 
  If connection is established you should have your remote connection configured now.


### NOTE: In case you want to completely remove postgresql from your server do
  ```
  sudo apt-get purge 'postgresql-*'
  sudo apt-get autoremove 'postgresql-*'
  ```

