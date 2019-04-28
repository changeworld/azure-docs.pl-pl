---
title: 'Szybki start: Wywoływanie interfejsu API analizy tekstu przy użyciu języka Python'
titleSuffix: Azure Cognitive Services
description: Pobierz informacje oraz przykłady kodu w celu szybkiego Rozpocznij pracę przy użyciu interfejsu API analizy tekstu w usługach Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 04/16/2019
ms.author: aahi
ms.openlocfilehash: 69eb3789586233b824da1ef6a9c338b07281f324
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60828109"
---
# <a name="quickstart-using-python-to-call-the-text-analytics-cognitive-service"></a>Szybki start: Wywoływanie analizy tekstu usługi Cognitive Service przy użyciu języka Python 
<a name="HOLTop"></a>

W tym przewodniku opisano, jak [wykrywać język](#Detect), [analizować tonację](#SentimentAnalysis) i [wyodrębniać kluczowe frazy](#KeyPhraseExtraction) przy użyciu [interfejsów API analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759711) i języka Python.

Można uruchomić ten przykład z poziomu wiersza polecenia lub jako notesu programu Jupyter na [MyBinder](https://mybinder.org) , klikając polecenie Uruchom integratora znaczków:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=TextAnalytics.ipynb)

### <a name="command-line"></a>Wiersz polecenia

Może być konieczne zaktualizowanie [IPython](https://ipython.org/install.html), jądra dla platformy Jupyter:
```bash
pip install --upgrade IPython
```

Może być konieczne zaktualizowanie [żądań](http://docs.python-requests.org/en/master/) biblioteki:
```bash
pip install requests
```

Zapoznaj się z tematem [API definitions (Definicje interfejsu API)](//go.microsoft.com/fwlink/?LinkID=759346), zawierającym dokumentację techniczną interfejsów API.

## <a name="prerequisites"></a>Wymagania wstępne

* [!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

* [Punktu końcowego i klucza dostępu](../How-tos/text-analytics-how-to-access-key.md) wygenerowany dla Ciebie podczas tworzenia konta.

* Następujące instrukcje importu, klucz subskrypcji i `text_analytics_base_url` są używane dla wszystkich przewodników Szybki Start poniżej. Dodaj polecenie importuje.

    ```python
    import requests
    # pprint is pretty print (formats the JSON)
    from pprint import pprint
    from IPython.display import HTML
    ```
    
    Dodaj następujące wiersze, a następnie zastąp `subscription_key` z kluczem ważnej subskrypcji, który został uzyskany wcześniej.
    
    ```python
    subscription_key = '<ADD KEY HERE>'
    assert subscription_key
    ```
    
    Następnie dodaj następujący wiersz, a następnie upewnij się, że region w `text_analytics_base_url` odnosi się do używana podczas konfigurowania usługi. Jeśli używasz bezpłatnej wersji próbnej klucza, nie trzeba wprowadzić zmiany.
    
    ```python
    text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/"
    ```

<a name="Detect"></a>

## <a name="detect-languages"></a>Wykrywanie języków

Interfejs API wykrywania języka wykrywa język dokumentu tekstowego przy użyciu [metody Detect Language](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7). Punkt końcowy usługi interfejsu API wykrywania języka w Twoim regionie jest dostępny za pośrednictwem następującego adresu URL:

```python
language_api_url = text_analytics_base_url + "languages"
print(language_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/languages


Ładunek interfejsu API składa się z listy elementów `documents`, z których każdy zawiera atrybut `id` i `text`. Atrybut `text` przechowuje tekst do przeanalizowania. 

Zastąp słownik `documents` dowolnym innym tekstem, którego język ma zostać wykryty.

```python
documents = { 'documents': [
    { 'id': '1', 'text': 'This is a document written in English.' },
    { 'id': '2', 'text': 'Este es un document escrito en Español.' },
    { 'id': '3', 'text': '这是一个用中文写的文件' }
]}
```

Poniższe wiersze kodu wywołują interfejs API wykrywania języka przy użyciu biblioteki `requests` w języku Python, aby określić język w dokumentach.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

Następujące wiersze kodu renderują dane JSON jako tabelę HTML.

```python
table = []
for document in languages["documents"]:
    text  = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]
    langs = ", ".join(["{0}({1})".format(lang["name"], lang["score"]) for lang in document["detectedLanguages"]])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, langs))
HTML("<table><tr><th>Text</th><th>Detected languages(scores)</th></tr>{0}</table>".format("\n".join(table)))
```

Odpowiedź oznaczająca Powodzenie JSON:

```json
    {'documents': [{'detectedLanguages': [{'iso6391Name': 'en',
                                           'name': 'English',
                                           'score': 1.0}],
                    'id': '1'},
                   {'detectedLanguages': [{'iso6391Name': 'es',
                                           'name': 'Spanish',
                                           'score': 1.0}],
                    'id': '2'},
                   {'detectedLanguages': [{'iso6391Name': 'zh_chs',
                                           'name': 'Chinese_Simplified',
                                           'score': 1.0}],
                    'id': '3'}],
     'errors': []}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analiza tonacji

Interfejs API analizy tonacji wykrywa tonację (zakres od dodatnie lub ujemne) zestawu rekordów tekstowych przy użyciu [metoda tonacji](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9). W poniższym przykładzie oceniane są dwa dokumenty, jeden w języku angielskim, a drugi w hiszpańskim.

Punkt końcowy usługi analizy tonacji jest dostępny w Twoim regionie za pośrednictwem następującego adresu URL:

```python
sentiment_api_url = text_analytics_base_url + "sentiment"
print(sentiment_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment

Podobnie jak w przykładzie dotyczącym wykrywania języka usługa jest dostarczana ze słownikiem z kluczem `documents` zawierającym listę dokumentów. Każdy dokument jest spójną kolekcją składająca się z elementów `id` i `text` do przeanalizowania oraz elementem `language` tekstu. To pole można wypełnić przy użyciu interfejsu API wykrywania języka z poprzedniej sekcji.

```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

Przy użyciu interfejsu API tonacji można teraz analizować dokumenty pod kątem tonacji.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(sentiment_api_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

Odpowiedź oznaczająca Powodzenie JSON:

```json
{'documents': [{'id': '1', 'score': 0.7673527002334595},
                {'id': '2', 'score': 0.18574094772338867},
                {'id': '3', 'score': 0.5}],
    'errors': []}
```

Ocenę tonacji dla dokumentu jest od 0,0 do 1,0, z wyższą ocenę wskazująca, bardziej dodatnie wskaźniki nastrojów klientów.

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Wyodrębnianie kluczowych fraz

Interfejs API wyodrębniania kluczowych fraz wyodrębnia kluczowe frazy w dokumencie tekstowym przy użyciu [metody Key Phrases](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6). W tej sekcji przewodnika opisano wyodrębnianie kluczowych fraz z dokumentów w języku angielskim i hiszpańskim.

Punkt końcowy usługi wyodrębniania kluczowych fraz jest dostępny za pośrednictwem następującego adresu URL:

```python
key_phrase_api_url = text_analytics_base_url + "keyPhrases"
print(key_phrase_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases

Kolekcja dokumentów jest taka sama jak w przypadku analizy tonacji.

```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

Obiekt JSON może być renderowany jako tabela HTML przy użyciu następujące wiersze kodu:

```python
table = []
for document in key_phrases["documents"]:
    text    = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]    
    phrases = ",".join(document["keyPhrases"])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, phrases))
HTML("<table><tr><th>Text</th><th>Key phrases</th></tr>{0}</table>".format("\n".join(table)))
```

Poniższe wiersze kodu wywołują interfejs API wykrywania języka przy użyciu biblioteki `requests` w języku Python, aby określić język w dokumentach.
```python
headers   = {'Ocp-Apim-Subscription-Key': subscription_key}
response  = requests.post(key_phrase_api_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

Odpowiedź oznaczająca Powodzenie JSON:
```json
{'documents': [
    {'keyPhrases': ['wonderful experience', 'staff', 'rooms'], 'id': '1'},
    {'keyPhrases': ['food', 'terrible time', 'hotel', 'staff'], 'id': '2'},
    {'keyPhrases': ['Monte Rainier', 'caminos'], 'id': '3'},
    {'keyPhrases': ['carretera', 'tráfico', 'día'], 'id': '4'}],
    'errors': []
}
```

## <a name="identify-entities"></a>Identyfikowanie jednostek

Interfejs API jednostek identyfikuje dobrze znane jednostki w dokumencie tekstowym przy użyciu [metody Entities](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634). W poniższym przykładzie zidentyfikowano jednostki dla dokumentów w języku angielskim.

Punkt końcowy usługi łączenia jednostek jest dostępny za pośrednictwem następującego adresu URL:

```python
entity_linking_api_url = text_analytics_base_url + "entities"
print(entity_linking_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/entities

Kolekcja dokumentów znajduje się poniżej:

```python
documents = {'documents' : [
  {'id': '1', 'text': 'Microsoft is an It company.'}
]}
```
Teraz dokumenty można wysłać do interfejsu API analizy tekstu w celu otrzymania odpowiedzi.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(entity_linking_api_url, headers=headers, json=documents)
entities = response.json()
```

Odpowiedź oznaczająca Powodzenie JSON:
```json
{  
   "documents":[  
      {  
         "id":"1",
         "entities":[  
            {  
               "name":"Microsoft",
               "matches":[  
                  {  
                     "wikipediaScore":0.20872054383103444,
                     "entityTypeScore":0.99996185302734375,
                     "text":"Microsoft",
                     "offset":0,
                     "length":9
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Microsoft",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Microsoft",
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type":"Organization"
            },
            {  
               "name":"Technology company",
               "matches":[  
                  {  
                     "wikipediaScore":0.82123868042800585,
                     "text":"It company",
                     "offset":16,
                     "length":10
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Technology company",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Technology_company",
               "bingId":"bc30426e-22ae-7a35-f24b-454722a47d8f"
            }
         ]
      }
   ],
    "errors":[]
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Analiza tekstu przy użyciu usługi Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Zobacz także 

 [Text Analytics overview (Omówienie analizy tekstu)](../overview.md)  
 [Często zadawane pytania](../text-analytics-resource-faq.md)
