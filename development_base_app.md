# Base app development steps

----------

### Build from green field

1. Create django project using the DjangoX application (install DjangoX)

This app manages custom user account and has configured all-auth (web templates) and rest-framework (json data) API

It has already implemented setting to login by email (no username)

Create project environment. Use in developmente the latest python version allowed by production environment (Digital Ocean). Currently it is python 3.5
```
cd ~/
mkdir <projname> && cd <projname>
pipenv install --python 3.5
pipenv install django=='2.0.8'
pipenv install spacy
```
Activate virtualenv
```
pipenv shell
```
Create django project
```
django-admin startproject django_proj
```
Renama project directory
```
mv django_proj advisor && cd advisor
```
Check django proj is working
```
python manage.py runserver
```

2. Copy djangoX into django_proj

Create app frontend. Copy pages into frontend
```
django-admin startapp frontend
```
Copy from djangox.pages into frontend

Copy folder djangox.static into <projname>.advisor
Copy folder djangox.templates into <projname>.advisor

Create apps shared and consumers. Copy users into consumers
```
django-admin startapp shared
django-admin startapp consumers
```

3. Recreate DjangoX application

Configure shared (models and admin)

Configure consumers (forms and views)

Configure frontend (views)

Configure django_proj (settings)

4. Install python modules required by DjangoX
```
pipenv install django-allauth
pipenv install django_crispy_forms
pipenv install django_debug_toolbar
```


Create database
```
python manage.py makemigrations
python manage.py migrate
```

Create superuser (make sure you have override username field in your custom model and also included username in the REQUIRED_FIELDS. Otehrwise you cannot create a supersuser with the command line).
```
python manage.py createsuperuser
```

5. Change email verification from 'optional' to manadatory in settings.py
```
ACCOUNT_EMAIL_VERIFICATION = 'mandatory'
```

6. Test user sign up, login, logout, change password


7. Adapt django file structure to development & production environment

Redefine django_app structure
```
cd django_proj
mkdir settings
```

Convert settings into common + local + production + user_local
```
mv settings.py settings/commons.py
```

Adjust manage.py
```
    os.environ.setdefault("DJANGO_SETTINGS_MODULE", "django_proj.settings.local")
```

Adjust wsgi.py
```
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "django_proj.settings.production")
```

Redefine static and templates base in commons.py
```
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "django_proj.settings.production")
```

8. Migrate from sqlite to postgresql

Install all python modules (non-DjangoX)

in settings.py complete list of installed applications

```
pipenv install django-cors-headers
pipenv install djangorestframework
pipenv install django_rest_auth
pipenv install django_star_ratings
pipenv install django_smart_selects
pipenv install sorl-thumbnail
pipenv install qrcode
pipenv install psycopg2-binary
pipenv install Pillow
pipenv install djangorestframework-jwt
pipenv install pandas
pipenv install nltk
```
Install language models for spacy
```
python -m spacy download es
python -m spacy download pt
```

Complete shared.models with all additional (non-DjangoX) models

Synchronize models with new database
```
python manage.py makemigrations
python manage.py migrate
```

Drop sqlite file(s)

9. Test user sign up, login, logout, change password, forgot password

10. Create new applications

Create apps merchants and shops. Go to the project directory (where is manage.py)
```
django-admin startapp merchants
django-admin startapp shops
```

11. Customize templates

Change homepage
Change login
Change logout
Change sign in
Change sign in sent email
Change confirmation email

Change change password (and message changed password)

Change reset password
Change reset password sent email
Change reset password change password template
Change reset password password changed


12. Introduce languages

Create locale
```
mkdir locale
```
Configure internatiolization in common.py
```
TEMPLATES = [
    {
        ...
        'OPTIONS': {
            'context_processors': [
                ...
                'django.template.context_processors.i18n',
            ],
        },
    },
]

MIDDLEWARE_CLASSES = (
    ...
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.locale.LocaleMiddleware',
    'django.middleware.common.CommonMiddleware',
    ...
)

```
Specify languages to use
```
from django.utils.translation import ugettext_lazy as _

TIME_ZONE = 'Europe/London'

USE_I18N = True

USE_L10N = True

USE_TZ = True

LANGUAGE_CODE = 'en-us'

LANGUAGES = (
    ('en', _('English')),
    ('pt', _('Portuguese')),
    ('es', _('Spanish')),
)

LOCALE_PATHS = (
    os.path.join(PROJECT_ROOT, 'locale'),
)
```
Create internationalization urls (project level)
```
urlpatterns = [
    url(r'^(?P<filename>(robots.txt)|(humans.txt))$',
        home_files, name='home-files'),
]
 
urlpatterns += i18n_patterns(
    url(r'^$', home, name='home'),
    url(r'^admin/', include(admin.site.urls)),
)
```
Create translation patterns in templates and emails
```
trans is used to translate a single line – we will use it for the title
blocktrans is used for extended content – we will use it for a paragraph

<div class="jumbotron">
    <div class="container">
        <h1>{% trans "Welcome to TaskBuster!"%}</h1>
        <p>{% blocktrans %}TaskBuster is a simple Task manager that helps you organize your daylife. </br> You can create todo lists, periodic tasks and more! </br></br> If you want to learn how it was created, or create it yourself!, check www.django-tutorial.com{% endblocktrans %}</p>
        <p><a class="btn btn-primary btn-lg" role="button">Learn more &raquo;</a></p>
      </div>
    </div>
</div>
```
Translate strings
```
python manage.py makemessages -l es
python manage.py makemessages -l pt
```
Make all your translations and then compile messages
```
python manage.py compilemessages -l es
python manage.py compilemessages -l pt
```

13. Introduce rest-auth API

Implement rest-auth authorization. Ensure the acount registration workflow in initiated by the rest-auth-registration API and migrates its processing to all-auth API

14. If its the case, move from session authentication to JWT authentication. The basic difference is that a JWT token expires and session token no. For security reasons better work with tokend that expire

15. Install reset password from mobile

Ensure password reset can be initiated by rest API but then the workflow is processed through all-auth (via templates -> change password screen is web and not mobile)

16. Implement custom login to receive device statistics

17. Introduce rest API

18. Migrate to production


