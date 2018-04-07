# Operations database

Database updating process

----------

### Weekly process
1. Extract
    ```
    Extracts data from sources to data files
    Per country, launch extracting per source
    Individual extracters per source
    Supervise individual results (relaunch process until success)
    Confirm success activating 'country green light' for next steps
    ``` 

2. Import
    ```
    Imports country extracted files into the database
    Upon each 'country green light', launch importer
    Keep base base files until next cycle
    Database mantain all weekly cyle imports
    ```

3. Parse:
    ```
    Pre-process titles for each contry/retailer
    Language by country, therefore parsing by country
    Parsing is exceuted upon importing
    The parser pre-processes the title field with two objectives
    1 - uniform syntax by country
    2 - generate article search terms
    ``` 

4. Split
    ```
    Generate pack information for articles
    Where applicable split title into title and packaging info
    Where applicable split title into title and brand info
    ``` 
