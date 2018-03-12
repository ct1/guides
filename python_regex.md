# Python regex

Regex basic tutorial

----------

### Regex basics
1. Identifiers
    ```
    \d  any number
    \D  anything but a number
    \s  any space
    \S  anything but a space
    \w  any character
    \W  anything but a character
    .   any character, except for a newline
    \b  the white space around words
    \.  a period
    ``` 

2. Modifiers
    ```
    {1,3} we're expecting 1-3
    +   match one or more
    ?   match zero or 1
    *   match zero or more
    $   match the end of a string
    ^   matching the beginning of a string
    |   either or
    []  range or 'variance'  [A-Z]   [1-5a-qA-Z]
    {x} expecting x amount
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
