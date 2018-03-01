# Setup postgresql for production

Configure PostgreSQL to allow remote connection and initialize data

----------

### 1. Create project database in remote PostgreSQL
Login to your production server.
```
ssh root@<production-server-ip-address>
``` 

#### Create postgresql database
Create database as explained [here](./django_postgresql.md). Look for topic [Create postgresql database](./django_postgresql.md)


### 2. Configure PostgreSQL  to allow remote connection
By default PostgreSQL is configured to be bound to “localhost”.

#### Configure postgresql.conf
First, find where is postgresql.conf. Its location varies with postgresql version.
```
find /etc/postgresql -name "postgresql.conf"
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
Open pg_hba.conf.
```
sudo nano pg_hba.conf
``` 
Add following entry at the very end.
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
```
systemctl status postgresql

* postgresql.service - PostgreSQL RDBMS
   Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor prese
   Active: active (exited) since Thu 2018-03-01 13:55:06 UTC; 4s ago
  Process: 16988 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
 Main PID: 16988 (code=exited, status=0/SUCCESS)

Mar 01 13:58:04 debian-s-1vcpu-1gb-lon1 systemd[1]: Starting PostgreSQL 
Mar 01 13:58:04 debian-s-1vcpu-1gb-lon1 systemd[1]: Started PostgreSQL R
``` 


### 3. Check remote access
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


### 4. Configure remote database in pgadmin
Open pgAdmin 4 in your local machine. Configure a remote server
You need to have a database created for your project
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


### 5. Setup triggers for full text search tables
If you don't need to manually create triggers for tables (e.g. full text search) skip this topic.

In pgAdmin 4 go to Servers/<myproj_production>/Databases/<database-name>/Schemas/public/Tables.

For each table you need to create triggers execute their corresponding script. Example: script for table campaigns.
```
Right-click/Scripts/SELECT script and go to the command line. 
```
Replace existing script with:
```
DROP TRIGGER IF EXISTS tsvectorupdate ON shared_campaigns;
DROP FUNCTION IF EXISTS campaigns_trigger();
CREATE FUNCTION campaigns_trigger() RETURNS trigger AS $$
begin
  new.search_vector :=
     setweight(to_tsvector('pg_catalog.english', coalesce(new.title,'')), 'A') ||
     setweight(to_tsvector('pg_catalog.english', coalesce(new.body,'')), 'B');
  return new;
end
$$ LANGUAGE plpgsql;

CREATE TRIGGER tsvectorupdate BEFORE INSERT OR UPDATE
ON shared_campaigns FOR EACH ROW EXECUTE PROCEDURE campaigns_trigger();
``` 


### 6. Initialize database
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
./geo_loaddata.sh

Installed 323 object(s) from 1 fixture(s)
```




