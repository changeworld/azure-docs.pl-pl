---
title: Szybki Start języka Python dla usług Azure kognitywnych, analiza tekstu interfejsu API | Dokumentacja firmy Microsoft
description: Pobierz informacje i przykładowy kod w celu szybkiego Rozpoczynanie pracy przy użyciu interfejsu API z analizy tekstu w kognitywnych usług Microsoft Azure.
services: cognitive-services
author: ashmaka
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 05/02/2018
ms.author: ashmaka
ms.openlocfilehash: b4c02767320b71912050ad511811767e6b5decf4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347761"
---
# <a name="quickstart-for-text-analytics-api-with-python"></a>Szybki Start dla Analiza tekstu interfejsu API za pomocą języka Python 
<a name="HOLTop"></a>

W tym przewodniku przedstawiono sposób do [wykrywać język](#Detect), [analizowanie wskaźniki nastrojów klientów](#SentimentAnalysis), i [wyodrębnić klucza fraz](#KeyPhraseExtraction) przy użyciu [tekst Analytics API](//go.microsoft.com/fwlink/?LinkID=759711)języka Python.

W tym przykładzie można uruchomić jako notesu Jupyter na [MyBinder](https://mybinder.org) , klikając polecenie Uruchom integratora znaczków: 

[![Obiekt wiążący](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=TextAnalytics.ipynb)

Zapoznaj się [definicji interfejsu API](//go.microsoft.com/fwlink/?LinkID=759346) dokumentacja techniczna dla interfejsów API.

## <a name="prerequisites"></a>Wymagania wstępne

Musi mieć [kognitywnych interfejsu API usług konta](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsu API z analizy tekstu**. Można użyć **warstwę bezpłatna do 5000 miesięcznie transakcji** w tym przewodniku.

Musi mieć również [punktu końcowego i klucz dostępu](../How-tos/text-analytics-how-to-access-key.md) wygenerowany automatycznie podczas tworzenia konta. 

Aby kontynuować, z tym przewodnikiem, Zastąp `subscription_key` z kluczem ważnej subskrypcji, który został uzyskany wcześniej.


```python
subscription_key = None
assert subscription_key
```

Następnie upewnij się, że region w `text_analytics_base_url` odpowiada używany podczas konfigurowania usługi. Jeśli używasz bezpłatnej wersji próbnej klucza jest konieczne wprowadzanie zmian.


```python
text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/"
```

<a name="Detect"></a>

## <a name="detect-languages"></a>Wykrycie języków

Interfejs API wykrywania języka wykrywa język tekstu dokumentu za pomocą [metody wykrywania języka](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7). Punkt końcowy usługi wykrywania języka interfejsu API w Twoim regionie jest dostępna za pośrednictwem następującego adresu URL:


```python
language_api_url = text_analytics_base_url + "languages"
print(language_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/languages


Ładunek do interfejsu API składa się z listy `documents`, każdy z kolei zawierającą `id` i `text` atrybutu. `text` Tekst, który ma zostać przeanalizowany magazyny atrybutów. 

Zastąp `documents` słownik z dowolny tekst do wykrywania języka. 


```python
documents = { 'documents': [
    { 'id': '1', 'text': 'This is a document written in English.' },
    { 'id': '2', 'text': 'Este es un document escrito en Español.' },
    { 'id': '3', 'text': '这是一个用中文写的文件' }
]}
```

Następny kilku wierszy kodu wyróżnienia do wykrywania interfejsu API języka przy użyciu `requests` biblioteki w języku Python można ustalić języka w dokumentach.


```python
import requests
from pprint import pprint
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

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


Następujące wiersze kodu renderować dane JSON jako tabeli HTML.


```python
from IPython.display import HTML
table = []
for document in languages["documents"]:
    text  = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]
    langs = ", ".join(["{0}({1})".format(lang["name"], lang["score"]) for lang in document["detectedLanguages"]])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, langs))
HTML("<table><tr><th>Text</th><th>Detected languages(scores)</th></tr>{0}</table>".format("\n".join(table)))
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analiza tonacji

Wskaźniki nastrojów klientów analizy API detexts wskaźniki nastrojów klientów zestawu rekordów tekstu, za pomocą [metody wskaźniki nastrojów klientów](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9). Poniższy przykład wyników dwa dokumenty, jeden w języku angielskim i drugą w języku hiszpańskim.

Punkt końcowy usługi dla analizy wskaźniki nastrojów klientów jest dostępny w Twoim regionie za pośrednictwem następującego adresu URL:


```python
sentiment_api_url = text_analytics_base_url + "sentiment"
print(sentiment_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment


Jako przykładu wykrywania języka, usługa jest udostępniona z słownika przy `documents` klucz, który składa się z listy dokumentów. Każdy dokument jest krotka składająca się z `id`, `text` do analizy i `language` tekstu. Wykrywanie języka interfejsu API z poprzedniej sekcji służy do wypełniania tego pola. 


```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

Wskaźniki nastrojów klientów interfejsu API można teraz używać do analizowania w dokumentach ich opinie.


```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(sentiment_api_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

    {'documents': [{'id': '1', 'score': 0.7673527002334595},
                   {'id': '2', 'score': 0.18574094772338867},
                   {'id': '3', 'score': 0.5}],
     'errors': []}


Wynik wskaźniki nastrojów klientów dla dokumentu się pomiędzy 0 $$ i $ $1 więcej punktów wskazujący więcej dodatnią wskaźniki nastrojów klientów.

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Wyodrębnianie kluczowych fraz

Klucz frazy wyodrębniania wyodrębnia fraz klucza z tekstu dokumentów za pomocą [metody fraz klucza](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6). Ta część przewodnika wyodrębnia klucza fraz dokumentów zarówno angielskim i hiszpańskim.

Punkt końcowy usługi dla usługi wyodrębniania frazy klucza jest dostępna za pośrednictwem następującego adresu URL:


```python
key_phrase_api_url = text_analytics_base_url + "keyPhrases"
print(key_phrase_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases


Kolekcja dokumentów jest taka sama jak której był używany do analizy wskaźniki nastrojów klientów.


```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
headers   = {'Ocp-Apim-Subscription-Key': subscription_key}
response  = requests.post(key_phrase_api_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```


    {'documents': [
        {'keyPhrases': ['wonderful experience', 'staff', 'rooms'], 'id': '1'},
        {'keyPhrases': ['food', 'terrible time', 'hotel', 'staff'], 'id': '2'},
        {'keyPhrases': ['Monte Rainier', 'caminos'], 'id': '3'},
        {'keyPhrases': ['carretera', 'tráfico', 'día'], 'id': '4'}],
     'errors': []
    }


Obiekt JSON renderowana ponownie tabeli HTML przy użyciu następujących wierszy kodu:


```python
from IPython.display import HTML
table = []
for document in key_phrases["documents"]:
    text    = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]    
    phrases = ",".join(document["keyPhrases"])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, phrases))
HTML("<table><tr><th>Text</th><th>Key phrases</th></tr>{0}</table>".format("\n".join(table)))
```

## <a name="identify-linked-entities"></a>Identyfikowanie jednostek połączonego

API łączenie jednostki identyfikuje dobrze znanych jednostek w tekście dokumentu za pomocą [łączenie jednostki — metoda](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634). Poniższy przykład identyfikuje jednostki dla dokumentów w języku angielskim.

Punkt końcowy usługi dla usługi połączeń jednostki jest dostępna za pośrednictwem następującego adresu URL:


```python
entity_linking_api_url = text_analytics_base_url + "entities"
print(entity_linking_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/entities


Kolekcja dokumentów znajduje się poniżej:


```python
documents = {'documents' : [
  {'id': '1', 'text': 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.'},
  {'id': '2', 'text': 'The Seattle Seahawks won the Super Bowl in 2014.'}
]}
```

Teraz dokumenty mogą być wysyłane do interfejsu API z analizy tekstu do otrzymania odpowiedzi.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(entity_linking_api_url, headers=headers, json=documents)
entities = response.json()
```
    {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "name": "Xbox One",
                        "matches": [
                            {
                                "text": "XBox One",
                                "offset": 23,
                                "length": 8
                            }
                        ],
                        "wikipediaLanguage": "en",
                        "wikipediaId": "Xbox One",
                        "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                        "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                    },
                    {
                        "name": "Ultra-high-definition television",
                        "matches": [
                            {
                                "text": "4K",
                                "offset": 63,
                                "length": 2
                            }
                        ],
                        "wikipediaLanguage": "en",
                        "wikipediaId": "Ultra-high-definition television",
                        "wikipediaUrl": "https://en.wikipedia.org/wiki/Ultra-high-definition_television",
                        "bingId": "7ee02026-b6ec-878b-f4de-f0bc7b0ab8c4"
                    }
                ]
            },
            {
                "id": "2",
                "entities": [
                    {
                        "name": "2013 Seattle Seahawks season",
                        "matches": [
                            {
                                "text": "Seattle Seahawks",
                                "offset": 4,
                                "length": 16
                            }
                        ],
                        "wikipediaLanguage": "en",
                        "wikipediaId": "2013 Seattle Seahawks season",
                        "wikipediaUrl": "https://en.wikipedia.org/wiki/2013_Seattle_Seahawks_season",
                        "bingId": "eb637865-4722-4eca-be9e-0ac0c376d361"
                    }
                ]
            }
        ],
        "errors": []
    }

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Analiza tekstu przy użyciu usługi Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Zobacz także 

 [Omówienie Analiza tekstu](../overview.md)  
 [Często zadawane pytania (FAQ)](../text-analytics-resource-faq.md)
