# Operations database

Database updating process

----------

### Language definition
1. Grammar and syntax
    ```
    Country
    Define vocabulary based on the natural language (NLTK)
    Customize parts-of-speech wording 
    Customize phrases or constituents
    Customize hierarchical syntactic structures (parse trees)

    Source
    Sources inherit country properties and customize them
    Customize sentence expressions
    ```

### Weekly process
1. Extract
    ```
    Extracts data from sources
    Per country, launch extracting per source
    Individual extracters per source
    Extractors generate product files
    Supervise individual results (relaunch process until success)
    Confirm success activating 'country green light' for next steps
    ``` 

2. Import
    ```
    Imports country extracts into DB
    Upon each 'country green light', launch importer by country
    Extract files are kept until the next extracting cycle
    Database keeps all weekly extracts
    ```

3. Parse:
    ```
    Pre-process titles for each contry/retailer
    Language by country, therefore parsing by country
    Parsing is executed automatically upon importing
    Parser pre-processes title to
    1 - create uniform syntax by country
    2 - generate smart search terms

    Each country has .yaml configuration file
    Each source has its own .yaml configuration file

    Steps by source
    1 - pre-process sentence
        - token merges for syntax conventions ('200 w' -> '200w', '100 120 gr' -> '100-120 gr', '10 x 4' -> '10x4', 'lr 03' -> 'lr03')
        - token merges (two or more) for custom expressions ('h & s' -> 'h&s')
        - element modifiers for vocabulary conformity 
            - tokens ('unidades' -> 'uni')
            - sentence ('d . o . m' -> 'd.o.m.')
    2 - generate search terms
        Process executed at token level
        - first preserve the removal of some stop_words by token merges (['2', 'en', '1'] -> ['2 en 1'])
        - remove country's stop_words
        - filter punctuation
        - stem tokens
    ```

4. Split
    ```
    Upon parsing success, launch the splitting process
    Where applicable split title into
    -> title + packaging
    -> title + brand

    Steps by source
    Identify pack phrases
    extract pack phrases
    ``` 

### Information process
1. Extract, import, parse and split
    ```
    Similar to weekly processes, processed every 3-4 months
    ``` 
2. Product codes links
    ```
    Common products must have the same 
    ``` 
3. Product info links
    ```
    Link reference product information to source products
    ``` 
