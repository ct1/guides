# Price parser

Price parser methodology

----------

### Postgresql setup
1. Activate unaccent PostgreSQL module 
    ```
    CREATE EXTENSION unaccent;
    ```
    We can search without worrying about accented characters, useful in different languages.
    Example
    ```
    Django
    Author.objects.filter(name__unaccent='Helene Joy').values_list('name', flat=True))

    SQL 
    SELECT "blog_author"."name" FROM "blog_author" WHERE UNACCENT("blog_author"."name") = (UNACCENT('Helene Joy'))

    ['Hélène Joy']
    ```
    `Warning`
    Queries using this filter will generally perform full table scans, which can be slow on large tables.


### Parsing at extraction level
1. Basic text cleaning.
    ```
    Text
    Convert to capitalize case (first capital letter)
    text.capitalize()

    Numeric
    Convert to db decimal format ( comma-to-dot ) 
    ``` 

2. Uniform text, separate words
    
    Inject spaces where needed, mutiple spaces to single space

3. Separate brand from title if required

    Search for brand pattern and split title.
    Pattern can vary by retailer

4. Separate format from title if required

    Search for format pattern and split title.
    Pattern can vary by retailer


### Parsing at deploying level

Process pack field
    Standardize field (all retailers)
    ```
    Remove text terms at the start
    Beautify numbers
    Standardize unit measures terminology
    ``` 

Process brand field

