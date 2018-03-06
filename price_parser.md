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

#### 1. Basic text cleaning
    Text fields
    ```
    Convert to title format (first capital letter)
    ``` 
    Numeric fields
    ```
    Convert numeric strings to european decimal system ( comma-to-dot ) 
    ``` 
    Uniformize text, separate words

#### 2. Separate brand from title if required
Determine list of words
Look for brand pattern
Break title into title + brand

#### 3. Separate format from title if required
Determine list of words
Look for format pattern
Break title into title + format


### Parsing at deploying level

#### 1. Uniform unit measures
Maintain unitMeasures dictionary by retailer

#### 2. Simplify format, remove unneeded
Maintain replace dictionary by retailer
Maintain unneeds dictionary by retailer

