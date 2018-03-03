# Initialize database

A installation guide to initialize postgresql database

----------

### 7. Initialize database

1. Create django models in database
    ```
    cd /var/www/<your-project>
    source env/bin/activate

    python manage.py migrate auth
    python manage.py makemigrations
    python manage.py migrate --noinput
    python manage.py collectstatic
    ```

2. Create superuser
    Replace `superuser` with the settings of your  admin
    ```
    echo "from django.contrib.auth import get_user_model; User = get_user_model(); User.objects.create_superuser(<superuser_name>, '<superuser_email>', '<superuser_password>')" | python manage.py shell
    ```

    Alternatively, the admin user ca be created interactively
    ```
    python manage.py createsuperuser
    ```

