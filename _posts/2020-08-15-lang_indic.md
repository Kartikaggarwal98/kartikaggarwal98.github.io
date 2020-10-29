---
layout: post
comments: true
title: 'Performance of Language Identification Libraries on Indian Languages'
date: 2020-08-15 00:00:00
tags: MachineTranslation nlp
---
> Accuracy and Performance of Language Identification Libraries on Indian Languages


<!--more-->


{: class="table-of-content"}
* TOC
{:toc}

Usually after collecting textual data from sources such as twitter, reddit, blogs etc., we need to identify the language of the tweets in order to filter out the noisy data. Depending upon the use case, you might use:

1. Any python library if the goal is to quickly identify the tweets in unwanted languages 
2. Use language modelling if the task intensively calls for accurate language identification.

The following is the review of various python libraries for identification of popular Indian Languages such as `Hindi, Bengali, Gujarati, Kannada, Marathi, Malayalam, Oriya, Punjabi, Tamil & Telugu.`

1. [Langid](https://github.com/saffsd/langid.py)
1. [Polyglot](https://polyglot.readthedocs.io/en/latest/Installation.html#installing-upgrading-from-the-pypi)
1. [LangDetect](https://pypi.org/project/langdetect/)
1. [pyCLD2](https://pypi.org/project/pycld2/)
1. [pyCLD3](https://github.com/bsolomon1124/pycld3)

## pyCld2

pycld2 is based on [Cld2 C++ Library](https://github.com/CLD2Owners/cld2 ) which uses a Naïve Bayes classifier to detect multiple languages by scoring sequences of 4 letters (quadgrams). Scoring is done on lowercase unicode letters; digits, punctuation,tags are removed; single letter words are ignored. Chunks of about 40 to 120 bytes are individually scored, to allow intermixed text in multiple languages.
```
>>> import pycld2 as cld2

>>> text="हमारी Strategic Partnership में भी नए अध्याय जुड़ रहे हैं।"
>>> cld2.detect(text)[2]
(('HINDI', 'hi', 78, 1057.0), ('ENGLISH', 'en', 19, 1163.0), ('Unknown', 'un', 0, 0.0))

>>> text="അന്താരാഷ്ട്ര അനുരജ്ഞന വർഷം"
>>> cld2.detect(text)[2]
(('MALAYALAM', 'ml', 100, 1024.0), ('Unknown', 'un', 0, 0.0), ('Unknown', 'un', 0, 0.0))
```

pyCld2 returns a list of 3 possible languages in order of the reliability score. The output can be interpreted as: `subscript, language, bytes, and reliability score`. For Indian languages, the results are highly reliable.

#### When does it fail?

1. When the text is too short
```
>>> text='hi'
>>> cld2.detect(text)[2]
(('Unknown', 'un', 0, 0.0), ('Unknown', 'un', 0, 0.0), ('Unknown', 'un', 0, 0.0))
```
2. When text contains multiple languages with bytes more than the actual language.
```
>>> text='हमारी Strategic Partnership में '
>>> cld2.detect(text)[2]
(('HINDI', 'hi', 50, 1181.0), ('ENGLISH', 'en', 44, 1163.0), ('Unknown', 'un', 0, 0.0))
>>> text='हमारी Strategic Partnership '
>>> cld2.detect(text)[2]
(('ENGLISH', 'en', 48, 1163.0), ('HINDI', 'hi', 45, 1024.0), ('Unknown', 'un', 0, 0.0))
```
3. When text in one language is transliterated to other language.

## pyCld3

[pyCld3](https://github.com/bsolomon1124/pycld3) is based on [google Cld3 library](https://github.com/google/cld3/). Cld3 uses a neural network model to identify the language. The input embeddings are passed as combination of character-based n-grams (unigram + bigram + trigram). Cld3 returns only single identified language with its probability score (from softmax function). CLD3 (unlike CLD2) does almost none of the text cleaning and preprocessing before sending it as input.

```
>>> import cld3
>>> text="what am I doing here?"
>>> cld3.get_language(text)
LanguagePrediction(language='en', probability=0.9903876781463623, is_reliable=True, proportion=1.0)
```

Cld3 also provides **support for romanized form (transliteration)** of some languages. For ex: The text "क्या है भाई" written in romanized form is detected correctly identified as "hindi-latin":
```
>>> text="kya hai bhai"
>>> cld3.get_language(text)
LanguagePrediction(language='hi-Latn', probability=1.0, is_reliable=True, proportion=1.0)
```

#### When does it fail?
1. When languages are very similar (ex: due to between their same origin scripts). For instance: Some words in Marathi and Hindi share devanagari script, hence either output is likely. <br>Also, in this example, hindi text is identified as nepali as they are both similar languages.
```
>>> text="मेरी आप सबको शुभकामनांए, फिर एक बार भोले बाबा को वंदन करता हूं।"
>>> cld3.get_language(text)
LanguagePrediction(language='ne', probability=0.6112947463989258, is_reliable=False, proportion=1.0)
```
2. When text in original language contains words from English. As Cld3 provides support for some languages written in Latin, the detected language is the latin version of the original language.
```
>>> text="हमारी Strategic Partnership"
>>> cld3.get_language(text)
LanguagePrediction(language='hi-Latn', probability=0.7101280689239502, is_reliable=True, proportion=1.0)
```

## Langid

[langid](https://github.com/saffsd/langid.py) also works based on a naive Bayes classifier with a multinomial event model over a mixture of byte n-grams(1≤n≤4). The model is pretrained on 97 languages. Langid also provides support for training custom models from different datasets.

```
>>> import langid
>>> text='how are you brother'
>>> langid.classify(text)
('en', -21.630419731140137)
```

This outputs a single language with highest probability. The second attribute is the negative log probability which needs to be normalized in order to convert to linear probability space (confidence score). 

#### When does it fail?

1. Incorrect identification due to pre-trained model. The following english text is identified as roman.
```
>>> text="you are a nice person"
>>> langid.classify(text)
('ro', -25.05718755722046)
```

## Polyglot

[Polygot](https://github.com/aboSamoor/polyglot) is based on pycld2 library which itself is based on the c++ Cld2 library. Hence the rules are nearly same which apply for cld2 as described [earlier](##pycld3).

## LangDetect

[LangDetect](https://github.com/shuyo/language-detection) implementation is also based on character n-gram features and naive bayes classifier. They added a noise filter and character normalization. LangDetect supports 55 languages.

To be continued ...