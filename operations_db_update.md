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

3. Parse & Split:
    ```
    Pre-process titles, packs, brand, offers for each contry/retailer
    Language by country, therefore parsing by country
    Parser pre-processes to
    1 - create uniform vocabulary and syntax
    2 - generate search terms
    Upon parsing success, launch the splitting process
    Where applicable, split title into:
    -> title + packaging
    -> title + brand
    ```


### Information process
1. Extract, import, parse and split
    ```
    Similar to weekly processes, processed every 3-4 months
    ``` 
2. Product codes links
    ```
    TBD
    ``` 
3. Product info links
    ```
    TBD
    ``` 
