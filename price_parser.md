# Price parser

Price parser approach

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
1. Categ1-3
    ```
    Capital case
    ``` 

2. Title
    ```
    Capital case
    Numeric compatible with database
    Separated words
    ``` 
3. Brand
    ```
    Capital case
    Separated words
    ``` 
4. Pack
    ```
    None
    ``` 

4. Price
    ```
    Numeric compatible with database
    remove euro symbol
    remove parenthesis
    ``` 
5. Price
    ```
    Numeric compatible with database
    remove parenthesis
    ``` 



### Parsing at importing level
1. Categ1-3
    ```
    None
    ``` 

1. Title
    Pack splitting (selective)
    ```
    1 - double split term process (left to right)
    2 - split at expression
    3 - split at term
    4 - split at <number> <unit measure> pattern (right to left)
    ``` 
    Brand splitting (selective)
    ```
    split uppercase terms
    Capital case
    ``` 
    Brand remove (selective)
    ```
    remove brand from title if title wan't become null
    ``` 

2. Brand
    ```
    None
    ``` 

3. Pack
    ```
    1 - Remove from expressions
    2 - Remove expressions
    3 - Remove terms
    4 - Replace expressions
    5 - Replace terms
    6 - Beautify floats
    7 - Beautify ints
    8 - Remove after expressions
    ``` 

4. Price
    ```
    None
    ``` 

5. Price_ref
    ```
    None
    ``` 
