---
title: Tworzenie i wdrażanie modelu klasyfikacji tekstu przy użyciu usługi Azure Machine Learning pakietu w celu wykonania analizy tekstu.
description: Informacje o sposobie tworzenia, uczenia, testowania i wdrażania modelu klasyfikacji tekstu przy użyciu pakietu Learning maszyny Azure w celu wykonania analizy tekstu.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 05/07/2018
ms.openlocfilehash: a970bd889e6994833b2e34adc90af594f9db4d6b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="build-and-deploy-text-classification-models-with-azure-machine-learning"></a>Tworzenie i wdrażanie modeli klasyfikacji tekstu przy użyciu usługi Azure Machine Learning

W tym artykule można poznać sposoby nauczenia i wdrożyć model klasyfikacji tekst z **Azure Machine Learning pakiet dla Analiza tekstu** (AMLPTA). Celem klasyfikacji tekst jest przypisanie fragment tekstu do jednego lub więcej wstępnie zdefiniowanych klas lub kategorii. Ten tekst na przykład można dokumentu, artykuł, zapytania wyszukiwania, poczty e-mail, tweet, biletami pomocy technicznej.

Istnieją aplikacje szerokie klasyfikacji tekstu, takich jak: 
+ Artykuły gazecie kategoryzacji i wiadomości przesyłania zawartości do tematów
+ Organizowanie stron sieci web w hierarchiczne kategorie 
+ Filtrowania spamu pocztą e-mail
+ Analiza opinii
+ Przewidywanie zamiarów użytkownika z zapytania wyszukiwania
+ Routingu biletami pomocy technicznej
+ Analizowanie opinii klientów 

Tekst Klasyfikacja modelu tworzenia i wdrażania przepływu pracy dla modelu z AMLPTA wygląda następująco:

1. Ładowanie danych
2. Uczenie modelu
3. Zastosuj klasyfikatora 
4. Ocena wydajności modelu
5. Zapisz potoku
6. Testowanie potoku
8. Wdróż model jako usługę sieci web

Zapoznaj się [pakietu dokumentacji](https://aka.ms/aml-packages/text) szczegółowe odwołania dla każdego modułu i klasy.

Przykładowy kod w tym artykule używa scikit — Dowiedz się potoku.

## <a name="prerequisites"></a>Wymagania wstępne 

1. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

1. Następujące konta i aplikacji musi skonfigurować i zainstalować:
   - Konto usługi Azure Machine Learning eksperymenty 
   - Konto Azure Machine Learning Model zarządzania
   - Zainstalowana aplikacja Azure Machine Learning Workbench

   Jeśli te trzy nie zostały jeszcze utworzony lub zainstalowany, należy wykonać [Szybki Start Azure Machine Learning i Workbench instalacji](../service/quickstart-installation.md) artykułu. 

1. Musi być zainstalowany pakiet Azure Machine Learning Analiza tekstu. Dowiedz się, jak [zainstalować ten pakiet w tym miejscu](https://aka.ms/aml-packages/text).


## <a name="sample-data-and-jupyter-notebook"></a>Przykładowe dane i notesu Jupyter

### <a name="get-the-jupyter-notebook"></a>Pobierz notesu Jupyter

Wypróbuj ją samodzielnie. Pobierz notesu i uruchomić samodzielnie.

> [!div class="nextstepaction"]
> [Pobierz notesu Jupyter](https://aka.ms/aml-packages/text/notebooks/text_classification_sentiment_data)

### <a name="download-and-explore-the-sample-data"></a>Pobierz i Eksploruj przykładowe dane
W poniższym przykładzie użyto [dataset 20 grup dyskusyjnych](http://qwone.com/~jason/20Newsgroups/) dostępnej za pośrednictwem scikit — Dowiedz się biblioteki w celu zademonstrowania tworzenie Klasyfikator tekstu przy użyciu usługi Azure Machine Learning pakietu dla Analiza tekstu. 

Element dataset 20 grup dyskusyjnych zawiera wpisy około 18,000 grupy dyskusyjne na 20 różne tematy podzielony na dwie podzbiorów: jeden dla szkolenia i jeden z nich do oceny wydajności. Podziel na pociągu i testowania opiera się na każdej wiadomości post daty przed lub po określonej dacie.

```python
# Import Packages 
# Use Azure Machine Learning history magic to control history collection
# History is off by default, options are "on", "off", or "show"
#%azureml history on
%matplotlib inline
# Use the Azure Machine Learning data collector to log various metrics
from azureml.logging import get_azureml_logger
import os

logger = get_azureml_logger()

# Log cell runs into run history
logger.log('Cell','Set up run')
# from tatk.utils import load_newsgroups_data, data_dir, dictionaries_dir, models_dir
import pip
pip.main(["show", "azureml-tatk"])
```

### <a name="set-the-location-of-the-data"></a>Ustaw lokalizację danych
Ustaw lokalizację, w której zostały pobrane dane w parametrze dir danych. Można również użyć własnych danych, wejściowy zestaw danych musi być format pliku *.tsv.

```python
import os
import pandas as pd

#set the working directory where to save the training data files
resources_dir = os.path.join(os.path.expanduser("~"), "tatk", "resources")
data_dir = os.path.join(os.path.expanduser("~"), "tatk", "data")

from sklearn.datasets import fetch_20newsgroups
twenty_train = fetch_20newsgroups(data_home=data_dir, subset='train')
X_train, y_train = twenty_train.data, twenty_train.target
df_train = pd.DataFrame({"text":X_train, "label":y_train})

twenty_test = fetch_20newsgroups(data_home=data_dir, subset='test')
X_test, y_test = twenty_test.data, twenty_test.target   
df_test = pd.DataFrame({"text":X_test, "label":y_test})
    
# Training Dataset Location
#training_file_path = <specify-your-own-training-data-file-path-here>
# df_train = pd.read_csv(training_file_path,
#                        sep = '\t',                        
#                        header = 0, names= <specify-your-column-name-list-here>)
df_train.head()
print("df_train.shape= {}".format(df_train.shape))

# Test Dataset Location
#test_file_path = <specify-your-own-test-data-file-path-here>
# df_test = pd.read_csv(test_file_path,
#                       sep = '\t',                        
#                       header = 0, names= <specify-your-column-name-list-here>)

print("df_test.shape= {}".format(df_test.shape))
df_test.head()
```
    df_train.shape= (11314, 2)
    df_test.shape= (7532, 2)
 
 Dane składają się etykiety i tekstu
    
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Etykiety</th>
      <th>tekst</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7</td>
      <td>Od: v064mb9k@ubvmsd.cc.buffalo.edu (NEIL B....</td>
    </tr>
    <tr>
      <th>1</th>
      <td>5</td>
      <td>Od: Tomaszewski Rick &lt; rick@ee.uwm.edu &gt;\nSubject:...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>Od: mathew &lt; mathew@mantis.co.uk &gt;\nSubject: R....</td>
    </tr>
    <tr>
      <th>3</th>
      <td>17</td>
      <td>Od: bakken@cs.arizona.edu \nSub (Dave Bakken)...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>19</td>
      <td>Od: livesey@solntze.wpd.sgi.com (Jan Livesey...</td>
    </tr>
  </tbody>
</table>
</div>

Pobierz odpowiedników między kategorie i ich nazwy.

```python
int_to_categories = pd.DataFrame({'category':range(20), 'category_name': list(twenty_train.target_names)})
int_to_categories 
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>category</th>
      <th>category_name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>ALT.atheism</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>COMP.Graphics</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>COMP.OS.MS windows.misc</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>COMP.sys.IBM.PC.Hardware</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>COMP.sys.Mac.Hardware</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>COMP.Windows.x</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6</td>
      <td>Misc.forsale</td>
    </tr>
    <tr>
      <th>7</th>
      <td>7</td>
      <td>rec.Autos</td>
    </tr>
    <tr>
      <th>8</th>
      <td>8</td>
      <td>rec.motorcycles</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9</td>
      <td>rec.Sport.baseball</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10</td>
      <td>rec.Sport.hockey</td>
    </tr>
    <tr>
      <th>11</th>
      <td>11</td>
      <td>SCI.crypt</td>
    </tr>
    <tr>
      <th>12</th>
      <td>12</td>
      <td>SCI.Electronics</td>
    </tr>
    <tr>
      <th>13</th>
      <td>13</td>
      <td>SCI.MED</td>
    </tr>
    <tr>
      <th>14</th>
      <td>14</td>
      <td>SCI.Space</td>
    </tr>
    <tr>
      <th>15</th>
      <td>15</td>
      <td>SOC.religion.christian</td>
    </tr>
    <tr>
      <th>16</th>
      <td>16</td>
      <td>Talk.politics.guns</td>
    </tr>
    <tr>
      <th>17</th>
      <td>17</td>
      <td>Talk.politics.mideast</td>
    </tr>
    <tr>
      <th>18</th>
      <td>18</td>
      <td>Talk.politics.Misc</td>
    </tr>
    <tr>
      <th>19</th>
      <td>19</td>
      <td>Talk.religion.Misc</td>
    </tr>
  </tbody>
</table>
</div>

Teraz można tworzyć histogram kreślenia wstępne eksploracji częstotliwości klasy w szkolenia i przetestować zestawów danych. 

```python
import numpy as np
import math
from matplotlib import pyplot as plt

data = df_train["label"].values
labels = set(data)
print(labels)
bins = range(len(labels)+1) 

#plt.xlim([min(data)-5, max(data)+5])

plt.hist(data, bins=bins, alpha=0.8)
plt.title('training data distribution over the class labels)')
plt.xlabel('class label')
plt.ylabel('frequency')
plt.grid(True)
plt.show()

data = df_test["label"].values
labels = set(data)
print(labels)
bins = range(len(labels)+1) 

#plt.xlim([min(data)-5, max(data)+5])

plt.hist(data, bins=bins, alpha=0.8)
plt.title('test data distribution over the class labels)')
plt.xlabel('class label')
plt.ylabel('frequency')
plt.grid(True)
plt.show()
```

Podczas uruchamiania notesu Jupypter, wykresy są wyświetlane po uruchomieniu poprzedniego bloku kodu.


## <a name="train-the-model"></a>Uczenie modelu

### <a name="specify-scikit-learn-algorithm-and-define-the-text-classifier"></a>Określ scikit — Dowiedz się algorytmów i zdefiniowanie Klasyfikator tekstu

Ten krok obejmuje szkoleniowy scikit — Dowiedz się tekst Klasyfikacja modelu przy użyciu jednego i Rest Algorytm uczenia Regresja logistyczna.

Aby uzyskać pełną listę learnings, zapoznaj się [uczących Scikit](http://scikit-learn.org/stable/supervised_learning) dokumentacji.

```python
from sklearn.linear_model import LogisticRegression
import tatk
from tatk.pipelines.text_classification.text_classifier import TextClassifier

log_reg_learner =  LogisticRegression(penalty='l2', dual=False, tol=0.0001, 
                            C=1.0, fit_intercept=True, intercept_scaling=1, 
                            class_weight=None, random_state=None, 
                            solver='lbfgs', max_iter=100, multi_class='ovr',
                            verbose=1, warm_start=False, n_jobs=3) 

#train the model a text column "tweets"
text_classifier = TextClassifier(estimator=log_reg_learner, 
                                text_cols = ["text"], 
                                label_cols = ["label"], 
#                                 numeric_cols = None,
#                                 cat_cols = None, 
                                extract_word_ngrams=True, extract_char_ngrams=True)

```

    TextClassifier::create_pipeline ==> start
    :: number of jobs for the pipeline : 12
    0   text_nltk_preprocessor
    1   text_word_ngrams
    2   text_char_ngrams
    3   assembler
    4   learner
    TextClassifier::create_pipeline ==> end
    
### <a name="fit-the-model"></a>Dopasuj modelu

Parametry domyślne pakietu. Domyślnie wyodrębnia Klasyfikator tekstu:
+ Unigrams programu Word i bigrams
+ Znak 4 g

```python
text_classifier.fit(df_train)        
```
   
    TextClassifier::fit ==> start
    schema: col=label:I4:0 col=text:TX:1 header+
    NltkPreprocessor::tatk_fit_transform ==> start
    NltkPreprocessor::tatk_fit_transform ==> end     Time taken: 0.08 mins
    NGramsVectorizer::tatk_fit_transform ==> startNGramsVectorizer::tatk_fit_transform ==> start
    
                vocabulary size=216393
    NGramsVectorizer::tatk_fit_transform ==> end     Time taken: 0.41 mins
                vocabulary size=67230
    NGramsVectorizer::tatk_fit_transform ==> end     Time taken: 0.49 mins
    VectorAssembler::transform ==> start, num of input records=11314
    (11314, 216393)
    (11314, 67230)
    all_features::
    (11314, 283623)
    Time taken: 0.06 mins
    VectorAssembler::transform ==> end
    LogisticRegression::tatk_fit ==> start
    
    [Parallel(n_jobs=3)]: Done  20 out of  20 | elapsed:  2.4min finished
    
    LogisticRegression::tatk_fit ==> end     Time taken: 2.4 mins
    Time taken: 3.04 mins
    TextClassifier::fit ==> end

    TextClassifier(add_index_col=False, callable_proprocessors_list=None,
            cat_cols=None, char_hashing_original=False, col_prefix='tmp_00_',
            decompose_n_grams=False, detect_phrases=False,
            dictionary_categories=None, dictionary_file_path=None,
            embedding_file_path=None, embedding_file_path_fastText=None,
            estimator=None, estimator_vectorizers_list=None,
            extract_char_ngrams=True, extract_word_ngrams=True,
            label_cols=['label'], numeric_cols=None,
            pos_tagger_vectorizer=False,
            preprocessor_dictionary_file_path=None, regex_replcaement='',
            replace_regex_pattern=None, scale_numeric_cols=False,
            text_callable_list=None, text_cols=['text'], text_regex_list=None,
            weight_col=None)


Podczas uczenia, musi mieć tekst i etykiety kolumn. Gdy dla prognoz wymagany jest tylko kolumny tekstowej. 

### <a name="examine-and-set-the-parameters-of-the-different-pipeline-steps"></a>Przejrzyj i ustaw parametry procedury innym potoku
    
Zazwyczaj należy ustawić parametry, przed dopasowania modelu. 

***Przykład pokazany z text_word_ngrams*** 

Poniższe przykłady pokazują, jak nauczenia modelu, używając parametrów domyślnych potoku i modelu. 

Aby sprawdzić, jakie parametry są uwzględnione w "text_word_ngrams", należy użyć [get_step_param_names_by_name](https://docs.microsoft.com/python/api/tatk.core.base_text_model.basetextmodel). Ta funkcja zwraca parametry, takie jak małe litery, input_col, output_col i tak dalej. 

```python
text_classifier.get_step_param_names_by_name("text_word_ngrams")
```

    ['min_df',
     'strip_accents',
     'max_df',
     'decode_error',
     'max_features',
     'binary',
     'input',
     'vocabulary',
     'analyzer',
     'token_pattern',
     'encoding',
     'use_idf',
     'save_overwrite',
     'output_col',
     'stop_words',
     'sublinear_tf',
     'input_col',
     'lowercase',
     'ngram_range',
     'preprocessor',
     'tokenizer',
     'hashing',
     'dtype',
     'norm',
     'smooth_idf',
     'n_hashing_features']

Następnie sprawdź wartości parametru "text_char_ngrams":

```python
text_classifier.get_step_params_by_name("text_char_ngrams")        
```
    {'analyzer': 'char_wb',
     'binary': False,
     'decode_error': 'strict',
     'dtype': numpy.float32,
     'encoding': 'utf-8',
     'hashing': False,
     'input': 'content',
     'input_col': 'NltkPreprocessor5283a730506549cc880f074e750607b0',
     'lowercase': True,
     'max_df': 1.0,
     'max_features': None,
     'min_df': 3,
     'n_hashing_features': None,
     'ngram_range': (4, 4),
     'norm': 'l2',
     'output_col': 'NGramsVectorizer8eb11031f6b64eaaad9ff0fd3b0f5b80',
     'preprocessor': None,
     'save_overwrite': True,
     'smooth_idf': True,
     'stop_words': None,
     'strip_accents': None,
     'sublinear_tf': False,
     'token_pattern': '(?u)\\b\\w\\w+\\b',
     'tokenizer': None,
     'use_idf': True,
     'vocabulary': None}

W razie potrzeby można zmienić parametrów domyślnych.  Następującym kodem można zmienić zakres wyodrębnionego znak n gramów z (4,4) do (3,4) do wyodrębnienia zarówno znak tri g i 4 g:

```python
text_classifier.set_step_params_by_name("text_char_ngrams", ngram_range =(3,4)) 
text_classifier.get_step_params_by_name("text_char_ngrams")
```
    {'analyzer': 'char_wb',
     'binary': False,
     'decode_error': 'strict',
     'dtype': numpy.float32,
     'encoding': 'utf-8',
     'hashing': False,
     'input': 'content',
     'input_col': 'NltkPreprocessor5283a730506549cc880f074e750607b0',
     'lowercase': True,
     'max_df': 1.0,
     'max_features': None,
     'min_df': 3,
     'n_hashing_features': None,
     'ngram_range': (3, 4),
     'norm': 'l2',
     'output_col': 'NGramsVectorizer8eb11031f6b64eaaad9ff0fd3b0f5b80',
     'preprocessor': None,
     'save_overwrite': True,
     'smooth_idf': True,
     'stop_words': None,
     'strip_accents': None,
     'sublinear_tf': False,
     'token_pattern': '(?u)\\b\\w\\w+\\b',
     'tokenizer': None,
     'use_idf': True,
     'vocabulary': None}

### <a name="export-the-parameters-to-a-file"></a>Wyeksportuj do pliku parametrów
W razie potrzeby można zoptymalizować wydajności modelu przez ponowne uruchomienie modelu dopasowany kroku z parametrami poprawione:

```python
import os
params_file_path = os.path.join(data_dir, "params.tsv")
text_classifier.export_params(params_file_path)
```

## <a name="apply-the-classifier"></a>Zastosuj klasyfikatora

Zastosuj Klasyfikator tekstu przeszkolone zestawu danych testowych do generowania klasy prognoz:

```python
 df_test = text_classifier.predict(df_test)
```

    TextClassifier ::predict ==> start
    NltkPreprocessor::tatk_transform ==> start
    NltkPreprocessor::tatk_transform ==> end     Time taken: 0.05 mins
    NGramsVectorizer::tatk_transform ==> startNGramsVectorizer::tatk_transform ==> start
    
    NGramsVectorizer::tatk_transform ==> end     Time taken: 0.15 mins
    NGramsVectorizer::tatk_transform ==> end     Time taken: 0.37 mins
    VectorAssembler::transform ==> start, num of input records=7532
    (7532, 216393)
    (7532, 67230)
    all_features::
    (7532, 283623)
    Time taken: 0.03 mins
    VectorAssembler::transform ==> end
    LogisticRegression::tatk_predict_proba ==> start
    LogisticRegression::tatk_predict_proba ==> end   Time taken: 0.01 mins
    LogisticRegression::tatk_predict ==> start
    LogisticRegression::tatk_predict ==> end     Time taken: 0.01 mins
    Time taken: 0.46 mins
    TextClassifier ::predict ==> end
    Order of Labels in predicted probabilities saved to attribute label_order of the class object
    
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Etykiety</th>
      <th>tekst</th>
      <th>probabilities</th>
      <th>Prognozowanie</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7</td>
      <td>Od: v064mb9k@ubvmsd.cc.buffalo.edu (NEIL B....</td>
      <td>[0.0165036341329, 0.0548664746458, 0.020549685...</td>
      <td>12</td>
    </tr>
    <tr>
      <th>1</th>
      <td>5</td>
      <td>Od: Tomaszewski Rick &lt; rick@ee.uwm.edu &gt;\nSubject:...</td>
      <td>[0.025145498995, 0.125877400021, 0.03947047877...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>Od: mathew &lt; mathew@mantis.co.uk &gt;\nSubject: R....</td>
      <td>[0.67566338235, 0.0150749738583, 0.00992439163...</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>17</td>
      <td>Od: bakken@cs.arizona.edu \nSub (Dave Bakken)...</td>
      <td>[0.146063943868, 0.00232465192179, 0.002442807...</td>
      <td>18</td>
    </tr>
    <tr>
      <th>4</th>
      <td>19</td>
      <td>Od: livesey@solntze.wpd.sgi.com (Jan Livesey...</td>
      <td>[0.670712265297, 0.017332269703, 0.01062429663...</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="evaluate-model-performance"></a>Ocena wydajności modelu
[Moduł oceny](https://docs.microsoft.com/python/api/tatk.evaluation) ocenia dokładność Klasyfikator tekstu przeszkolone na zestawu danych testowych. Funkcja evaluate generuje macierzy pomyłek i udostępnia wynik makro F1.

```python
 text_classifier.evaluate(df_test)          
```

    TextClassifier ::evaluate ==> start
    Time taken: 0.0 mins
    TextClassifier ::evaluate ==> end
    

Wykreślenia pomyłek bez macierzy normalizacji dla wizualizacji.


```python
evaluator.plot_confusion_matrix(normalize=False,
                                title='Confusion matrix, without normalization', 
                                print_confusion_matrix=False,
                                figsize=(8,8),
                                colors=None)
```

Macierz pomyłek, bez normalizacji
    
Podczas uruchamiania notesu pomyłek macierzy będą wyświetlane



Wykreślenia macierzy pomyłek znormalizowane do wizualizacji.


```python
evaluator.plot_confusion_matrix(normalize=True,
                                title='Normalized Confusion matrix', 
                                print_confusion_matrix=False,
                                figsize=(8,8),
                                colors=None)
```

    Normalized confusion matrix
   
Podczas uruchamiania notesu pomyłek macierzy będą wyświetlane

## <a name="save-the-pipeline"></a>Zapisz potoku
Zapisz potoku klasyfikacji w pliku zip. Ponadto Zapisz ngrams programu word i znak n gramów jako pliki tekstowe.

```python
import os
working_dir = os.path.join(data_dir, 'outputs')  
if not os.path.exists(working_dir):
    os.makedirs(working_dir)

# you can save the trained model as a folder or a zip file
model_file = os.path.join(working_dir, 'sk_model.zip')    
text_classifier.save(model_file)
# %azureml upload outputs/models/sk_model.zip

```
    BaseTextModel::save ==> start
    TatkPipeline::save ==> start
    Time taken: 0.28 mins
    TatkPipeline::save ==> end
    Time taken: 0.38 mins
    BaseTextModel::save ==> end
    
```python
# for debugging, you can save the word n-grams vocabulary to a text file
word_vocab_file_path = os.path.join(working_dir, 'word_ngrams_vocabulary.tsv')
text_classifier.get_step_by_name("text_word_ngrams").save_vocabulary(word_vocab_file_path) 
# %azureml upload outputs/dictionaries/word_ngrams_vocabulary.pkl

# for debugging, you can save the character n-grams vocabulary to a text file
char_vocab_file_path = os.path.join(working_dir, 'char_ngrams_vocabulary.tsv')
text_classifier.get_step_by_name("text_char_ngrams").save_vocabulary(char_vocab_file_path) 
# %azureml upload outputs/dictionaries/char_ngrams_vocabulary.pkl
```

    save_vocabulary ==> start
    saving 216393 n-grams ...
    Time taken: 0.01 mins
    save_vocabulary ==> end
    save_vocabulary ==> start
    saving 67230 n-grams ...
    Time taken: 0.0 mins
    save_vocabulary ==> end
 
## <a name="load-the-pipeline"></a>Ładowanie potoku
Ładowanie klasyfikacji potoku i ngrams programu word i znak n gramów dla inferencing:

```python
# in order to deploy the trained model, you have to load the zip file of the classifier pipeline
loaded_text_classifier = TextClassifier.load(model_file)

from tatk.feature_extraction import NGramsVectorizer
word_ngram_vocab = NGramsVectorizer.load_vocabulary(word_vocab_file_path)
char_ngram_vocab = NGramsVectorizer.load_vocabulary(char_vocab_file_path)
```
    BaseTextModel::load ==> start
    TatkPipeline::load ==> start
    Time taken: 0.14 mins
    TatkPipeline::load ==> end
    Time taken: 0.15 mins
    BaseTextModel::load ==> end
    loading 216393 n-grams ...
    loading 67230 n-grams ...
    

## <a name="test-the-pipeline"></a>Testowanie potoku

Aby ocenić zestawu danych testowych, zastosuj potoku klasyfikacji załadowanego tekstu:

```python
predictions = loaded_text_classifier.predict(df_test)
loaded_evaluator = loaded_text_classifier.evaluate(predictions)
loaded_evaluator.get_metrics('macro_f1')
```
    TextClassifier ::predict ==> start
    NltkPreprocessor::tatk_transform ==> start
    NltkPreprocessor::tatk_transform ==> end     Time taken: 0.05 mins
    NGramsVectorizer::tatk_transform ==> startNGramsVectorizer::tatk_transform ==> start
    
    NGramsVectorizer::tatk_transform ==> end     Time taken: 0.14 mins
    NGramsVectorizer::tatk_transform ==> end     Time taken: 0.36 mins
    VectorAssembler::transform ==> start, num of input records=7532
    (7532, 216393)
    (7532, 67230)
    all_features::
    (7532, 283623)
    Time taken: 0.03 mins
    VectorAssembler::transform ==> end
    LogisticRegression::tatk_predict_proba ==> start
    LogisticRegression::tatk_predict_proba ==> end   Time taken: 0.01 mins
    LogisticRegression::tatk_predict ==> start
    LogisticRegression::tatk_predict ==> end     Time taken: 0.01 mins
    Time taken: 0.45 mins
    TextClassifier ::predict ==> end
    Order of Labels in predicted probabilities saved to attribute label_order of the class object
    TextClassifier ::evaluate ==> start
    Time taken: 0.0 mins
    TextClassifier ::evaluate ==> en
    
    0.82727029243808903

## <a name="operationalization-deploy-and-consume"></a>Operationalization: Wdrażanie i korzystanie

W tej sekcji Wdrażanie potoku klasyfikacji tekstu jako usługi Azure Machine Learning web service przy użyciu [interfejsu wiersza polecenia Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning). Następnie możesz korzystać z usługi sieci web szkolenia i oceniania.

**Zaloguj się do Twojej subskrypcji platformy Azure z wiersza polecenia platformy Azure**

Przy użyciu [Azure](https://azure.microsoft.com/) konto z prawidłową subskrypcją, zaloguj się za pomocą interfejsu wiersza polecenia następujące polecenie:
<br>`az login`

+ Aby przełączyć się do innej subskrypcji platformy Azure, użyj polecenia:
<br>`az account set --subscription [your subscription name]`

+ Aby wyświetlić bieżące konto zarządzania modelu, użyj polecenia:
  <br>`az ml account modelmanagement show`

**Tworzenie i ustawianie środowiska wdrażania**

Należy ustawić środowiska wdrażania na raz. Jeśli nie masz jeszcze, konfigurowanie środowiska wdrażania za pomocą [tych instrukcji](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup). 

1. Upewnij się, że środowisko usługi Azure Machine Learning, model administracyjny konta i grupy zasobów, które znajdują się w tym samym regionie.

1. Pobierz plik konfiguracji wdrożenia z magazynu obiektów Blob i zapisz ją lokalnie:

   ```python
   # Download the deployment config file from Blob storage `url` and save it locally under `file_name`:
   deployment_config_file_url = 'https://aztatksa.blob.core.windows.net/dailyrelease/tatk_deploy_config.yaml'
   deployment_config_file_path=os.path.join(resources_dir, 'tatk_deploy_config.yaml')
   import urllib.request
   urllib.request.urlretrieve(deployment_config_file_url, deployment_config_file_path)
   ```

1. Aktualizuj plik konfiguracji wdrożenia, aby odzwierciedlać zasobów:

   ```python
   web_service_name = 'please type your web service name'
   working_directory= os.path.join(resources_dir, 'deployment') 

   web_service = text_classifier.deploy(web_service_name= web_service_name, 
                          config_file_path=deployment_config_file_path,
                          working_directory= working_directory)  
   ```

1. Biorąc pod uwagę, że trenowanego modelu został wdrożony pomyślnie, należy wywołać usługę sieci web oceniania na nowy zestaw danych:

   ```python
   print("Service URL: {}".format(web_service._service_url))
   print("Service Key: {}".format(web_service._api_key))
   ```

1. Ładowanie usługi sieci web w dowolnej chwili za pomocą jego nazwy:

   ```python
   from tatk.operationalization.csi.csi_web_service import CsiWebService
   url = "<please type the service URL here>"
   key = "<please type the service Key here>"
   web_service = CsiWebService(url, key)
   ```

1. Przetestuj usługę sieci web z treści dwóch wiadomości e-mail z zestawu danych 20 newsgrpoups:

   ```python
   # Example input data for scoring
   import json
   dict1 ={}
   dict1["recordId"] = "a1" 
   dict1["data"]= {}
   dict1["data"]["text"] = """
   I'd be interested in a copy of this code if you run across it.
   (Mail to the author bounced)
    > / hpldsla:comp.graphics / email-address-removed / 12:53 am  May 13,
    1993 /
    > I fooled around with this problem a few years ago, and implemented a
    > simple method that ran on a PC.
    > was very simple - about 40 or 50 lines of code.
    . . .
    > Somewhere I still have it
    > and could dig it out if there was interest.
   """
   
   dict2 ={}
   dict2["recordId"] = "b2"
   dict2["data"] ={}
   dict2["data"]["text"] = """
   >>Could the people discussing recreational drugs such as mj, lsd, mdma, etc.,
   >>take their discussions to alt.drugs? Their discussions will receive greatest
   >>contribution and readership there. The people interested in strictly
   >>"smart drugs" (i.e. Nootropics) should post to this group. The two groups
   >>(alt.drugs & alt.psychoactives) have been used interchangably lately.
   >>I do think that alt.psychoactives is a deceiving name. alt.psychoactives
   >>is supposedly the "smart drug" newsgroup according to newsgroup lists on
   >>the Usenet. Should we establish an alt.nootropics or alt.sdn (smart drugs &
   >>nutrients)? I have noticed some posts in sci.med.nutrition regarding
   >>"smart nutrients." We may lower that groups burden as well.
   >   

   I was wondering if a group called 'sci.pharmacology' would be relevent.
   This would be used for a more formal discussion about pharmacological
   issues (pharmacodynamics, neuropharmacology, etc.)      

   Just an informal proposal (I don't know anything about the net.politics
   for adding a newsgroup, etc.)

   """

   dict_list =[dict1, dict2]
   data ={}
   data["values"] = dict_list
   input_data_json_str = json.dumps(data)
   print (input_data_json_str)
   prediction = web_service.score(input_data_json_str)
   prediction
   ```

   ```
   {"values": [{"recordId": "a1", "data": {"text": "\nI'd be interested in a copy of this code if you run across it.\n(Mail to the author bounced)\n > / hpldsla:comp.graphics / email-address-removed / 12:53 am  May 13,\n 1993 /\n > I fooled around with this problem a few years ago, and implemented a\n > simple method that ran on a PC.\n > was very simple - about 40 or 50 lines of code.\n . . .\n > Somewhere I still have it\n > and could dig it out if there was interest.\n"}}, {"recordId": "b2", "data": {"text": "\n>>Could the people discussing recreational drugs such as mj, lsd, mdma, etc.,\n>>take their discussions to alt.drugs? Their discussions will receive greatest\n>>contribution and readership there. The people interested in strictly\n>>\"smart drugs\" (i.e. Nootropics) should post to this group. The two groups\n>>(alt.drugs & alt.psychoactives) have been used interchangably lately.\n>>I do think that alt.psychoactives is a deceiving name. alt.psychoactives\n>>is supposedly the \"smart drug\" newsgroup according to newsgroup lists on\n>>the Usenet. Should we establish an alt.nootropics or alt.sdn (smart drugs &\n>>nutrients)? I have noticed some posts in sci.med.nutrition regarding\n>>\"smart nutrients.\" We may lower that groups burden as well.\n>\n\nI was wondering if a group called 'sci.pharmacology' would be relevent.\nThis would be used for a more formal discussion about pharmacological\nissues (pharmacodynamics, neuropharmacology, etc.)\n\nJust an informal proposal (I don't know anything about the net.politics\nfor adding a newsgroup, etc.)\n\n"}}]}
   F1 2018-05-02 00:10:58,272 INFO Web service scored. 
    
   '{"values": [{"recordId": "b2", "data": {"text": "\\n>>Could the people discussing recreational drugs such as mj, lsd, mdma, etc.,\\n>>take their discussions to alt.drugs? Their discussions will receive greatest\\n>>contribution and readership there. The people interested in strictly\\n>>\\"smart drugs\\" (i.e. Nootropics) should post to this group. The two groups\\n>>(alt.drugs & alt.psychoactives) have been used interchangably lately.\\n>>I do think that alt.psychoactives is a deceiving name. alt.psychoactives\\n>>is supposedly the \\"smart drug\\" newsgroup according to newsgroup lists on\\n>>the Usenet. Should we establish an alt.nootropics or alt.sdn (smart drugs &\\n>>nutrients)? I have noticed some posts in sci.med.nutrition regarding\\n>>\\"smart nutrients.\\" We may lower that groups burden as well.\\n>\\n\\nI was wondering if a group called \'sci.pharmacology\' would be relevent.\\nThis would be used for a more formal discussion about pharmacological\\nissues (pharmacodynamics, neuropharmacology, etc.)\\n\\nJust an informal proposal (I don\'t know anything about the net.politics\\nfor adding a newsgroup, etc.)\\n\\n", "class": 13}}, {"recordId": "a1", "data": {"text": "\\nI\'d be interested in a copy of this code if you run across it.\\n(Mail to the author bounced)\\n > / hpldsla:comp.graphics / email-address-removed / 12:53 am  May 13,\\n 1993 /\\n > I fooled around with this problem a few years ago, and implemented a\\n > simple method that ran on a PC.\\n > was very simple - about 40 or 50 lines of code.\\n . . .\\n > Somewhere I still have it\\n > and could dig it out if there was interest.\\n", "class": 1}}]}'
   ```


## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat usługi Azure Machine Learning pakietu analizy tekstu w tych artykułach:

+ Odczyt [pakietu Przegląd i Dowiedz się, jak zainstalować ją](https://aka.ms/aml-packages/text).

+ Eksploruj [odwołania dokumentacji](https://aka.ms/aml-packages/text) dla tego pakietu.

+ Dowiedz się więcej o [inne pakiety języka Python dla usługi Azure Machine Learning](reference-python-package-overview.md).

