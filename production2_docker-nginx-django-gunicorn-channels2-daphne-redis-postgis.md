# Production setup for django, gunicorn, channels2, daphne, redis, postgis and nginx using Docker

Document strucuture

```
.
├── .env                            # variables for all environments
├── .env.dev                        # variables for development only
├── .env.prod                       # variables for production only
├── docker-compose.yml              # production
├── docker-compose.override.yml     # development
├── .git                            # git repo generated from this level
├── .gitignore
├── nginx                           # config web server -> production
│   ├── Dockerfile
│   └── nginx.conf
├── postgres
│   ├── Dockerfile
│   └── init_docker_postgres.sh     # initial populate DB for testing environment
└── src
    ├── Dockerfile
    ├── django_proj
    │   ├── asgi.py                 # communication file for channels2 
    │   ├── routing.py              # 'urls.py equivalent' for channels
    │   ├── settings.py
    │   ├── urls.py
    │   ├── wsgi.py.py              # communication file for django http
    │   └── ...
    ├── manage.py
    ├── requirements.txt
    ├── static
    └── ...
```

The structure includes .env files and docker-compose files used to control the distinct docker environments.

Then, includes version control files (.git -> manage pack at docker level vs django level), nginx folder with its configuration files, postgres folder with initalization script (production only) and src folder with the django channels2 app

----------

We use different container structures for development and production.
In development we use daphne to handle all django requests. We use 3 containers

```
Development (docker-compose.override.yml)
├── db
├── redis
└── web
```

In production, we use nginx as proxy server to serve static files and ditribute HTTP requests to gunicorn and websocket requests to daphne. We use 5 containers 

```
Production (docker-compose.yml)
├── channels
├── db
├── redis
├── nginx
└── web
```

When docker-compose is executed, if it finds an override version of compose.yaml it merges docker-compose.yaml with docker-compose.override.yaml

In production we have only docker-compose.yml (default environment) and in developments we have both docker-compose.yaml and docker-compose.override.yaml (development overrides production). This way we use the same command for both production adn development

```
docker-compose up 
```

If we had other environments, such as staging, we would need additional compose .yaml files and we would use the command:
```
docker.compose -f docker-compose.yaml -f docker-compose.staging.yaml up
```


### Common settings to production and development
.env file contains environment variables shared by many systems and scripts

```
POSTGRES_HOST=db
POSTGRES_DB=advisor
POSTGRES_PORT=5432
POSTGRES_USER=my_advisor_user
POSTGRES_PASSWORD=my_advisor_user_password
```

Database variables are coomon to all environments, all use potgis. These variables are used by the docker containers and by the django app. Docker reads them from the file and 'export' them in the container OS to be used in applications as environment variables

common docker-compose structure

```
version: '3'

services:
  redis:
    image: redis:latest
    command: redis-server
    ports:
      - '6379:6379'

  db:
    image: mdillon/postgis:9.6
    environment:
      - POSTGRES_DB=POSTGRES_DB
      - POSTGRES_USER=POSTGRES_USER
      - POSTGRES_PASSWORD=POSTGRES_PASSWORD
    volumes:
	 db_data
      - db_data:/var/lib/postgresql/pgdata
    env_file:
      - ./src/.env
    ports:
      - '5434:5432'

volumes:
  db_data:
```

The redis container uses an exisiting image (latest) and executes the command redis-server and communicates with other containes via the standard port 6379

The db container differs in production and development since in production it has a build to perform the initial population of the database. Appart the build, they are similar in operation
The container is based on an existing postgis image (mdillon/postgis:9.6), uses environmnet variables read from ./env file. The volumes directive is used to persist the container DB data (synchronize the container disk with the server disk), otherwise when the container would be dismissed or crashed, its data would be lost.
This container communicates with other containers through the standard port 5432

### Production settings
.env.prod file contains production environment variables

```
DEBUG = False
SECRET_KEY=uv78bkpdd!6&wl$x1!gsz^ph+_v858:$p^#jaud%j$t9cg1elf  (just an example)
ALLOWED_HOSTS=xx.101.85.xx   (replace by the web server IP ADDRESS)
```

This variables are specific for production


Daphne could handle all django requests.
We are splitting the django traffic into two services for performance reasons. With isolated containers performing different types of traffic we simplify the performance management. Containers can be separated servers. Container scalability is also simplified in this way.


docker-compose structure

```
version: '3'

services:
  web:
    build: ./src
    env_file:
      - ./src/.env
      - ./src/.env.prod
    command: >
      bash -c "python manage.py makemigrations
      && python manage.py migrate
      && gunicorn -c gunicorn.conf django_proj.wsgi"
    volumes: 
      - ./src:/src
    ports:
      - "8000:8000"
    depends_on:
      - db

  channels:
    build: ./src
    command: daphne -b 0.0.0.0 -p 8001 django_proj.asgi:application -v2
    volumes: 
      - ./src:/src
    ports:
      - "8001:8001"
    env_file:
      - ./src/.env
      - ./src/.env.prod
    links:
      - redis

  db:
    build: ./postgres
    environment:
      - POSTGRES_DB=POSTGRES_DB
      - POSTGRES_USER=POSTGRES_USER
      - POSTGRES_PASSWORD=POSTGRES_PASSWORD
    volumes:
	db_data
      - db_data:/var/lib/postgresql/pgdata
    env_file:
      - ./src/.env
    ports:
      - '5434:5432'

  nginx:
    build: ./nginx
    depends_on:
      - web
    command: nginx -g 'daemon off;'
    ports:
      - "80:80"
    volumes:
      - ./src/static:/var/www/static
      - ./src/media:/var/www/media
    depends_on:
      - web
      - channels
```

Two services to handle django servers (gunicorn and daphne), the nginx service to proxy the django services, and the postgres service as previously explained but her built from a Dockerfile to perform initial population of the database.

The web service manages the HTTP requests proxied by nginx.
Tis container runs the gunicorn server. It communicates with django via the wsgi protocol (django_proj.wsgi.py file)

The web service directives:
	
```
build - tells docker to build an image from a Dockerfile (no base image) indicating where it is located
env_file - file with envrionment variables to read. Variables can be used in docker and/or in apps running in this container
command - command to be executed in this container
volumes - tells docker to persist conatiner directories in external disk. In case of crash or stop the container data is lost if not synchronized This way we ensure to keep the managed data. In this directive we map external disk with container folders
ports - define port to communicate with other containers. Container ports are also mapped to server ports
depends_on - tells docker that a list of other containers should start prior to this container.
```

The channels service manages the websockets requests proxied by nginx.
Tis container runs the daphne server. It communicates with django via the asgi protocol (django_proj.asgi.py file). Daphne requires redis to handle in-memory channel communications.

The web service directives (only those not explained previously):
```
links - tells docker that this container is linked to redis container. Operations are integrated but for performance reasons the processing is splitted by containers
```

The nginx service handles the client requests. It serves directly static requests, and passes (proxies) all other request to gunicorn (HTTP) or daphne (websockets).
Requests starting by /ws/ (in our case) are passed to daphne, all the other are passed to gunicorn

nginx Dockerfile
```
FROM nginx:latest

ADD nginx.conf /etc/nginx/nginx.conf

RUN mkdir -p /var/www/static
RUN mkdir -p /var/www/media

WORKDIR /var/www/media
RUN chown -R nginx:nginx /var/www/media

WORKDIR /var/www/static
RUN chown -R nginx:nginx /var/www/static
```

The niginx Dockerfile directives:

FROM defines the starting point, a base image. In this case nginx:latest
Then we copy the nginx configuration to the container, to the folder where nginx app expects to find its configurations. Applications in containers run like they would run in any other server.So, the configuration file needs to be where nginx app expects it to be.

We also create the folders where the static content (delivered directly by nginx, not by django servers) and change its permissons

WORKDIR defines the reference folder from where commands are executed
RUN executes commands

the nginx configuration file (nginx.conf)
```
user nginx;

events {
	worker_connections 1024;
}

http {
	include /etc/nginx/mime.types;
	client_max_body_size 100M;

	map $http_upgrade $connection_upgrade {
	        default upgrade;
	        '' close;
	}

	server {
		listen 80;
		charset utf-8;
		server_name the_shopping_advisor.com;

		access_log /dev/stdout;
		error_log /dev/stdout info;

		location = /favicon.ico { access_log off; log_not_found off; }

		location /media/ {
			alias /var/www/media/;
		}

		location /static/ {
			alias /var/www/static/;
		}

		location /ws/ {
			proxy_pass http://channels:8001;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection $connection_upgrade;
            proxy_redirect off;
		}

		location / {
			proxy_pass http://web:8000;
			proxy_set_header Host $host;
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			proxy_set_header X-Forwarded-Host $server_name;
		}
	}
}
```

The important directives are in the server directive

It start by by saying that the server is listening clients using the standard port 80 (applications don't have to state port with this config)
The server_name defined here is not very important since django is controlling the allowed hosts, in our case.
Then we define log locations and we tell nginx how to deal if it doesn't found the favicon

The we configure statics and media, which are served directly by nginx. The directive states if '/static/' exists (starts?) in the request, then the request is served by the folder defined in alias

The requests starting by '/ws/' are passed to dpahne. The remaining requests are passed to gunicorn.
In this lat two cases the proxy_pass uses the http protocol (dont'comunicate through socket files as in the unix protocol) calling the docker service by it's name and port. In the docker-compose file we have defined as port for daphne 8001 and defined 8000 for gunicorn. The rest of the directives, are standard directives for websockets in the case of daphne, and standar diretcives of http for the case of gunicorn

The postgres Dockerfile directives:
```
FROM mdillon/postgis:9.6
COPY ./src/py/dbexport.pgsql /tmp/psql_data
COPY ./postgres/init_docker_postgres.sh /docker-entrypoint-initdb.d/
```


The postgres initialization script (init_docker_postgres.sh)
```
#!/bin/bash
DATABASE_NAME="advisor"
DATABASE_USER=<my_advisor_user>
DB_DUMP_LOCATION="/tmp/psql_data/dbexport.pgsql"

echo "*** CREATING DATABASE ***"

# create default database
gosu postgres postgres --single <<EOSQL
	dropdb "$DATABASE_NAME";
	createdb -T template0 "$DATABASE_NAME";
	GRANT ALL PRIVILEGES ON DATABASE "$DATABASE_NAME" TO "$DATABASE_USER";
	psql "$DATABASE_NAME" < "$DB_DUMP_LOCATION";
EOSQL

echo "*** DATABASE CREATED! ***"
```



### Development settings

.env.dev file contains production environment variables

```
DEBUG = True
SECRET_KEY=ksbwxbmi%f1%kc=zkljpuxtc6i4dyq2j-%2%un##oh+9$-o*m3   (an example)
ALLOWED_HOSTS=*         (list of localshost, lan IP's, ...)
```

docker-compose structure

```
version: '3'

services:
  web:
    build: ./src
    command: >
      bash -c "python manage.py makemigrations
      && python manage.py migrate
      && daphne -b 0.0.0.0 -p 8000 django_proj.asgi:application -v2
    volumes: 
      - ./src:/src
    ports:
      - "8000:8000"
    env_file:
      - ./src/.env.dev
    links:
      - redis
    depends_on:
      - db
    ports:
      - "8000:8000"
```


