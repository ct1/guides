# Setup postgresql for django in Digital Ocean

An installation guide for postgresql and postgis for django

----------


### 2. Create database and user
Replace `projname` with the settings of your database
```
dropdb <projname>
dropuser <projname>user

# create user and database
psql -c "CREATE USER <userid> WITH PASSWORD 'password';"
createdb --owner=<userid> -E utf8 <projname>

# create postgis extension to handle geometry data
psql -d <projname> -c "CREATE EXTENSION postgis;"

# exit postgres
exit
```


#### NOTE - Data in database is initialized after the setup of django project
* python manage.py makemigrations
* python manage.py migrate
* python manage.py collectstatic
