# Classify products

----------

### Step1 Assign grp
1. Headings determine grp

    ```
    rm ~/.android/adbkey.pub
    ```

### Step1 Assign subgrp

Item parsing aims to extract a list of tags from its title text, to classify that item in terms of product type and characteristics

Parsing is done at category level.

Parsing is done by scrapy supported by a custom category vocabulary (pattern rules by category)

Pattern rules will help in finding product attributes and in eliminating text segments from the parsing process

Three groups of pattern rules (by product category):

PROD: list of 'real-world product' types (sentence subjects in linguistic terms). 

FEATURES: List of product characteristics that may be found during parsing. Examples include tokens or expressions like 'sin gluten' or '100 % natural'

EXPRESSIONS: List of text segments to be removed from the parsing process if found and add complexity to the parsing process. Examples include 'Gratis'

Parsing text through a natural language processing includes the part-of-speech tagging (subjects, noun, verbs, adjectives, ...) and NER tagging (Named Entity Recogniton -> custom pattern rules)


Scrapy start from the root (master subject in the sentence)



1. Look for pre-defined list of subgroup terms by group


product_xx.yaml
  Compound:
  Expr:         expression to remove from sentence
  Feature:      expressions to be included in product classification terms
  Prod:         compound product terms
  Root:


SPACY
1. NER (Named Entity Recogniton)
A named entity is a "real-world object" that's assigned a name.

    ```
    doc = nlp("Next week I'll be in Madrid.")
    for ent in doc.ents:
        print(ent.text, ent.label_)
     
    # Next week DATE
    # Madrid GPE
    ```

2. Custom pattern rules added to Spacy
    ```
    ...
    patternRules1 = ['term1', 'multi word term', 'another term']
    patternRules2 = ['term2', 'term3', 'term4']
    patt1 = tuple(patternRules1)
    patt2 = tuple(patternRules2)
    entity_matcher = EntityMatcher(nlp, patt1, 'TERMS1')
    entity_matcher.add_terms(nlp, patt2, 'TERM2')

    if 'entity_matcher' in nlp.pipe_names:
        nlp.remove_pipe('entity_matcher')

    nlp.add_pipe(entity_matcher, after='ner')
    ...
    ```

Add PROD pattern rules to SPACY

PROD defines tokens or text expressions to be found in text
PROD patterns found on record titles, will become 'prod' attributes of the record




Parsing after all cleaning
1. Look for PROD in title