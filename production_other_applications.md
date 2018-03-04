# Install other applications if required

An installation guide to install in Digital Ocean other applications required by the django project

----------

### 1. Zbar
zbar module requires the installation of development packages
```
sudo apt-get install libzbar-dev python3-dev
``` 


### 2. Setup triggers in postgresql for full text search
Open pgAdmin 4 in your local machine.

Go to Servers/<myproj_production>/Databases/<database-name>/Schemas/public/Tables.

For each table you need to create triggers execute their corresponding script. Example: script for table campaigns.
  ```
  Right-click/Scripts/SELECT script and go to the command line. 
  ```
  Replace existing script with:
  ```
  DROP TRIGGER IF EXISTS tsvectorupdate ON shared_campaigns;
  DROP FUNCTION IF EXISTS campaigns_trigger();
  CREATE FUNCTION campaigns_trigger() RETURNS trigger AS $$
  begin
    new.search_vector :=
       setweight(to_tsvector('pg_catalog.english', coalesce(new.title,'')), 'A') ||
       setweight(to_tsvector('pg_catalog.english', coalesce(new.body,'')), 'B');
    return new;
  end
  $$ LANGUAGE plpgsql;

  CREATE TRIGGER tsvectorupdate BEFORE INSERT OR UPDATE
  ON shared_campaigns FOR EACH ROW EXECUTE PROCEDURE campaigns_trigger();
  ``` 

