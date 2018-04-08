# Python regex

Regex basic tutorial

----------

### Regex basics
1. Identifiers
    ```
    .       - Any Character Except New Line
    \d      - Digit (0-9)
    \D      - Not a Digit (0-9)
    \w      - Word Character (a-z, A-Z, 0-9, _)
    \W      - Not a Word Character
    \s      - Whitespace (space, tab, newline)
    \S      - Not Whitespace (space, tab, newline)

    \b      - Word Boundary
    \B      - Not a Word Boundary
    ^       - Beginning of a String
    $       - End of a String

    []      - Matches Characters in brackets
    [^ ]    - Matches Characters NOT in brackets
    |       - Either Or
    ( )     - Group

    Sample Regexs
    [a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+

    MetaCharacters (Need to be escaped):
    .[{()\^$|?*+
    ``` 

2. Quantifiers
    ```
    *       - 0 or More
    +       - 1 or More
    ?       - 0 or One
    {3}     - Exact Number
    {3,4}   - Range of Numbers (Minimum, Maximum)
    ```

3. White space characters:
    ```
    \n  new line
    \s  space
    \t  tab
    \e  escape
    \f  form feed
    \r  return
    ``` 

4. Example
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
