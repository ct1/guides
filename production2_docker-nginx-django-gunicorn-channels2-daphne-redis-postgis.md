# Production setup using Docker, django, gunicorn, channels2, daphne, redis, postgis, nginx

Document strucuture

```
.
├── .env
├── .env.dev
├── .env.prod
├── .git
├── .gitignore
├── docker-compose.yml              # production
├── docker-compose.override.yml     # development
├── nginx                           # config web server -> production
│   ├── Dockerfile
│   └── nginx.conf
├── ...
└── src
    ├── Dockerfile
    ├── django_proj
    │   ├── asgi.py                 # communication file for channels2 
    │   ├── routing.py
    │   ├── settings.py
    │   ├── urls.py
    │   ├── wsgi.py.py              # communication file for django http
    │   └── ...
    ├── manage.py
    ├── requirements.txt
    ├── static
    └── ...
```

----------

We use different docker structures for development and production.
In development we use daphne to handle all django requests.
In production, HTTP requests go through gunicorn and channels requests (websockets) go through daphne. Production uses a proxy server for static requests and pass all other requests to gunicorn and daphne

Development (docker-compose.override.yml)
├── db
├── redis
└── web

Production (docker-compose.yml)
├── channels
├── db
├── redis
├── nginx
└── web

When docker-compose is executed, if it finds an override version of compose.yaml it merges docker-compose.yaml with docker-compose.override.yaml
In production we have only docker-compose.yml (default environment) and in developments we have both docker-compose.yaml and docker-compose.override.yaml (development overrides production). This way we use the same command for both production adn development

```
docker-compose up 
```

If we had other environments, such as staging, we would use the command:
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


common docker-compose structure

```
version: '3'

services:
  # backing store for the channel layer
  redis:
    image: redis:latest
    command: redis-server
    ports:
      - '6379:6379'

  db:
    image: mdillon/postgis:9.6
    environment:
      - POSTGRES_DB=POSTGRES_DB
      - POSTGRES_USER=advisoruser
      - POSTGRES_PASSWORD=advisoruser_pswd
      #- PGDATA=/var/lib/postgresql/pgdata
    volumes:
      # if we remove the container the data is not lost. Create side-db called db_data
      - db_data:/var/lib/postgresql/pgdata
    env_file:
      - ./src/.env
    ports:
      - '5434:5432'

volumes:
  db_data:
```



### Production settings
.env.prod file contains production environment variables

```
DEBUG = False
SECRET_KEY=uv78bkpdd!6&wl$x1!gsz^ph+_v858($p^#jaud%j$t9cg1elf
ALLOWED_HOSTS=xx.101.85.xx
```

docker-compose structure

```
version: '3'

services:
  web:
    # web service is built from Dockerfile in the folder src
    build: ./src

    # execute this command in the container
    #bash -c "python manage.py makemigrations
    #  && python manage.py migrate
    #  && gunicorn -c gunicorn.conf django_proj.wsgi"
    env_file:
      - ./src/.env.prod
    command: >
      bash -c "python manage.py makemigrations
      && python manage.py migrate
      && gunicorn -c gunicorn.conf django_proj.wsgi"

    # synchronize container folders with disk folders
    volumes: 
      # every change produced in the container is synchronized with disk
      - ./src:/src

    ports:
      # match container with disk-app port
      - "8000:8000"
    depends_on:
      - db

  channels:
    build: ./src
    # execute daphne using asgi.py
    command: daphne -b 0.0.0.0 -p 8001 django_proj.asgi:application -v2
    volumes: 
      # every change produced in the container is synchronized with disk
      - ./src:/src
    ports:
      # match container with disk-app port
      - "8001:8001"
    env_file:
      - ./src/.env.prod
    links:
      - redis

  nginx:
    # web service is built from Dockerfile existing in the folder nginx
    build: ./nginx
    depends_on:
      - web
    command: nginx -g 'daemon off;'
    ports:
      - "80:80"
    volumes:
      # sync disk folder with container folder
      - ./src/static:/var/www/static
      - ./src/media:/var/www/media
    depends_on:
      - web
      - channels
```
	

### Development settings
.env.dev file contains production environment variables

```
DEBUG = True
SECRET_KEY=ksbwxbmi%f1%kc=zkljpuxtc6i4dyq2j-%2%un##oh+9$-o*m3
ALLOWED_HOSTS=*
```

docker-compose structure

```
version: '3'

services:
  web:
    build: ./src
    # execute daphne using asgi.py
    command: >
      bash -c "python manage.py makemigrations
      && python manage.py migrate
      && daphne -b 0.0.0.0 -p 8000 django_proj.asgi:application -v2
    volumes: 
      # every change produced in the container is synchronized with disk
      - ./src:/src
    ports:
      # match container with disk-app port
      - "8000:8000"
    env_file:
      - ./src/.env.dev
    links:
      - redis
    depends_on:
      - db
    ports:
      # match container with disk-app port
      - "8000:8000"
```


