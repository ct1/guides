# Machine learning

To run machine learning algorithms convert text files into numerical feature vectors

----------

### SPACY
Get text's grammatical structure

Spacy manages linguistic concepts with machine learning functionallity
Spacy manages lingusitc concepts such as sentence boundary detection (SBD), tokenization, part-of-speech (POS) tagging, dependency parsing, lemmatization, and named entity recognition (NER).
Spacy uses statistical (neural) models to make efficient predicitions for tagging, parsing and entity recognition.
Models were designed to balance of speed, size and accuracy.
Other statistical functionalities such as text similarity (e.g. for recommendation systems), text classification, and model training (Prodigy).
A document is generated via processing text through process pipelines that depend on the used models. Pipelines define component sequential tasks that can be customized

Model decisions (part-of-speech tag to assign, whether a word is a named entity) is a prediction. Predicitions result from examples the model has seen during training. Data is required to train a model (examples of text, labels you want the model to predict (part-of-speech tag, named entity or any other information).

1. A model is loaded via:
    ```
    spacy.load()
    ```

2. This will return a language object contaning all components and data needed to process text. We usually call it nlp:
    ```
    nlp = spacy.load('es_core_news_sm')
    ```

3. Calling the nlp object on a string of text will return a processed doc 
    ```
    doc = nlp(u'Apple is looking at buying U.K. startup for $1 billion')
    ```


### Parsing title
	
1. Pre-process:
    ```
	Text cleaning for all
	Text cleaning for the family specifics
    ```
	



