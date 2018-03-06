# Install other applications if required

An installation guide to install in Digital Ocean other applications required by the django project

----------

### 1. Zbar
zbar module requires the installation of development packages
```
sudo apt-get install libzbar-dev python3-dev
``` 


### 2. Database triggers for FTS (full text search)
Full text search requires for the creation of triggers on postgresql.

Open pgAdmin 4 in your local machine.

Go to Servers/<myproj_production>/Databases/<database-name>/Schemas/public/Tables.

For each table you need to create triggers execute their corresponding script. Example: script for table campaigns.
  ```
  Right-click/Scripts/SELECT script and go to the command line. 
  ```
  Replace existing script with:
  ```
  DROP TRIGGER IF EXISTS tsvectorupdate ON shared_priceitempt;
  DROP FUNCTION IF EXISTS priceitempt_trigger();

  CREATE FUNCTION priceitempt_trigger() RETURNS trigger AS $$
  begin
    new.search_vector :=
    setweight(to_tsvector(coalesce(new.title,'')), 'A') ||
    setweight(to_tsvector(coalesce(new.brand,'')), 'A') ||
    setweight(to_tsvector(coalesce(new.pack,'')), 'A') ||
      setweight(to_tsvector(
        (SELECT coalesce(string_agg(categ3, ' '), '')
         FROM shared_priceheader WHERE id = NEW.header_id)), 'B') ||
      setweight(to_tsvector(
        (SELECT coalesce(string_agg(categ2, ' '), '')
         FROM shared_priceheader WHERE id = NEW.header_id)), 'C') ||
      setweight(to_tsvector(
        (SELECT coalesce(string_agg(categ1, ' '), '')
         FROM shared_priceheader WHERE id = NEW.header_id)), 'D');
    return new;
  end
  $$ LANGUAGE plpgsql;

  CREATE TRIGGER tsvectorupdate BEFORE INSERT OR UPDATE
      ON shared_priceitempt FOR EACH ROW EXECUTE PROCEDURE priceitempt_trigger();
  ``` 

