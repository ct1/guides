# Production issues

----------

### Create production environment
1. Python in development is ahead of django in Digital Ocean

Keep development version aligned with most update version of python3

2. As of Aug/2018, pipenv used in development does not work for some modules in production (e.g. spacy)

Do manual creation of virtual environment in production. use virtualenv + pip.

Spacy has to be installed as one of the first packages (after django) to ensure it install its correct versions of dependencies. Some other module share dependency with spacy and if this module is installed prior to scrapy can create problems to install scrapy later (due to a dependency requirement already satisfied but with the wrong version)

    ```
    cd /var/www/<projname>
    virtualenv -p python3 env
    . env/bin/activate

    pip install django
    pip install spacy

    pip install ...
    ```

3. All the rest fall the documented process
