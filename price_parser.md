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





### Regex basics
Identifiers
    ```
    \d any number
    \D anything but a number
    \s any space
    \S anything but a space
    \s any character
    \S anything but a character
    .  any character, except for a newline
    \b the white space around words
    \. a period
    ``` 

Modifiers
    ```
    {1,3} we're expecting 1-3
    +  match one or more
    ? match zero or 1
    * match zero or more
    $ match the end of a string
    ^ matching the beginning of a string
    | either or
    [] range or 'variance'  [A-Z]   [1-5a-qA-Z]
    {x} expecting x amount
    ```

White space characters:
    ```
    \n  new line
    \s  space
    \t  tab
    \e  escape
    \f  form feed
    \r  return
    ``` 

Example
    ```
    import re

    exampleString = """
    Jessica is 15 years old, and Daniel is 27 years old.
    Edward is 97, and his grandfather, Oscar, is 102. 
    """

    ages = re.findall(r'\d{1-3}', exampleString)
    names = re.findall(r'[A-Z][a-z]*', exampleString)

    print(ages)
    ['15', '27', '97', '102']
    print(names)
    ['Jessica', 'Daniel', 'Edward', 'Oscar']

    ageDict = {}
    x = 0
    for eachName in names:
        ageDict[eachname] = ages[x]
        x += 1
    print(ageDict)

    {'Daniel': '27', 'Edward': '97', 'Jessica': '15', 'Oscar':'102' }
    ``` 
