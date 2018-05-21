# Product information parser

Product information parsing approach

----------

The parse process has several objectives
1. Uniform language at country level
2. Develop a search algorithm at country level
3. Simplify product identification
4. Share information among common products


### Approach
Process data aggregated at country level
Use NLTK (Natural Language Toolkit) technologies for language analysis
Develop own language using a context-free grammar and custom lexical categories

Title and pack information have different treatments
1. Title
    Parsed using portuguese/spanish taggers trained for the country language
    Through word-tokenizing segment title search terms
2. Pack
    Parsed using own language (vocabulary and contex-free grammar)
    Pack serach tokens are identified in the parsng process
3. Brand
    No specific process but key for the classifying process


### Process
1. Clean data and uniformize vocabulary
This step has general and token-specific processes
2. Classify products
There is too much information in product description, we need to identify the relevant and discard the remaining
3. Identifiy common products on multiple sources
A classifier algorithm helps to identify similar products on different sources


### Language
Common language help to relate products from differnt sources.
Steps for the language development
    ```
    Develop lexical categories
    Develop a context-free grammar 
    ```

Use NLTK (Natural Language Tool Kit) to parse text at country level
    ```
    Use countrie's corpus to train tagger models
    Apply taggers to determine part-of-speech for sentences
    ```

#### Title parser
First read tagged sentence from a corpus.
Then choose tagger and train it. There are more fancy options but we use N-gram taggers.
Unigram tagger doesn’t take the ordering of the words into account.
Bigram tagger do care about the order of the words, so it considers the context of each word by analyzing it by pairs. 
    ```
    from nltk.corpus import cess_esp as cess
    from nltk import UnigramTagger as ut
    from nltk import BigramTagger as bt

    # Read the corpus into a list, 
    # each entry in the list is one sentence.
    cess_sents = cess.tagged_sents()

    # Train the unigram tagger
    uni_tag = ut(cess_sents)

    sentence = "Hola , esta foo bar ."

    # Tagger reads a list of tokens.
    uni_tag.tag(sentence.split(" "))

    # Split corpus into training and testing set.
    train = int(len(cess_sents)*90/100) # 90%

    # Train a bigram tagger with only training data.
    bi_tag = bt(cess_sents[:train])

    # Evaluates on testing data remaining 10%
    bi_tag.evaluate(cess_sents[train+1:])

    # Using the tagger.
    bi_tag.tag(sentence.split(" "))

    ```


### Tokenize for non-english
Spanish tokenizer is meant for sentence parsing (not word)
    ```
    import nltk.databse
    sp_tokenizer = nltk.data.load('tokenizers/punkt/spanish.pickle')
    sp_tokenizer.tokenize(text)
    ```



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

