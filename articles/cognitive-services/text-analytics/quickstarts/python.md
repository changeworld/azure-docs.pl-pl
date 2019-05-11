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
ms.date: 05/09/2019
ms.author: aahi
ms.openlocfilehash: fb411b7e36d8658c5f46294a3b7025c3e93928e7
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540106"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>Szybki start: Za pomocą interfejsu API REST języka Python do wywoływania usług Cognitive analizy tekstu 
<a name="HOLTop"></a>

Użyj tego przewodnika Szybki Start, aby rozpocząć analizowanie język za pomocą interfejsu REST API analizy tekstu i Python. W tym artykule przedstawiono, jak do [Wykryj język](#Detect), [analizowanie tonacji](#SentimentAnalysis), [wyodrębnianie kluczowych fraz](#KeyPhraseExtraction), i [zidentyfikować połączonych jednostek](#Entities).

Zapoznaj się z tematem [API definitions (Definicje interfejsu API)](//go.microsoft.com/fwlink/?LinkID=759346), zawierającym dokumentację techniczną interfejsów API.

## <a name="prerequisites"></a>Wymagania wstępne

* [Python 3.x](https://python.org)

* [Punktu końcowego i klucza dostępu](../How-tos/text-analytics-how-to-access-key.md) wygenerowany dla Ciebie podczas tworzenia konta.

* Python żądań biblioteki
    
    Biblioteki można zainstalować za pomocą następującego polecenia:

    ```console
    pip install --upgrade requests
    ```

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]


## <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nową aplikację języka Python w ulubionym edytorze lub w środowisku IDE. Dodaj następujące instrukcje importu do pliku.

```python
import requests
# pprint is used to format the JSON response
from pprint import pprint
from IPython.display import HTML
```

Utwórz zmienne swój klucz subskrypcji i punkt końcowy interfejsu API REST analizy tekstu. Sprawdź, czy region w punkcie końcowym odpowiada z użytym podczas tworzenia konta (na przykład `westcentralus`). Jeśli używasz bezpłatnej wersji próbnej klucza, nie trzeba wprowadzić zmiany.
    
```python
subscription_key = "<ADD YOUR KEY HERE>"
text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/"
```

Poniżej opisano sposób wywoływania każdą z funkcji interfejsu API.

<a name="Detect"></a>

## <a name="detect-languages"></a>Wykrywanie języków

Dołącz `languages` do podstawowego punktu końcowego analizy tekstu w celu utworzenia adresu URL wykrywanie języka. Na przykład: `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
    
```python
language_api_url = text_analytics_base_url + "languages"
```

Ładunek do interfejsu API składa się z listy `documents`, które są spójne kolekcje zawierające `id` i `text` atrybutu. `text` Atrybut przechowuje tekst, który ma być analizowane i `id` może być dowolna wartość. 

```python
documents = { "documents": [
    { "id": "1", "text": "This is a document written in English." },
    { "id": "2", "text": "Este es un document escrito en Español." },
    { "id": "3", "text": "这是一个用中文写的文件" }
]}
```

Biblioteka żądań służy do wysyłania dokumenty do interfejsu API. Dodaj klucz subskrypcji, aby `Ocp-Apim-Subscription-Key` nagłówka i Wyślij żądanie za pomocą `requests.post()`. 

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

### <a name="output"></a>Dane wyjściowe

```json
{
"documents":[
    {
        "detectedLanguages":[
        {
            "iso6391Name":"en",
            "name":"English",
            "score":1.0
        }
        ],
        "id":"1"
    },
    {
        "detectedLanguages":[
        {
            "iso6391Name":"es",
            "name":"Spanish",
            "score":1.0
        }
        ],
        "id":"2"
    },
    {
        "detectedLanguages":[
        {
            "iso6391Name":"zh_chs",
            "name":"Chinese_Simplified",
            "score":1.0
        }
        ],
        "id":"3"
    }
],
"errors":[]
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analiza tonacji

Aby wykrywania tonacji (który zakresów między dodatnia lub ujemna) zestaw dokumentów, należy dołączyć `sentiment` do podstawowego punktu końcowego analizy tekstu w celu utworzenia adresu URL wykrywanie języka. Na przykład: `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
    
```python
sentiment_url = text_analytics_base_url + "sentiment"
```

Jak w przykładzie wykrywania języka Tworzenie słownika przy użyciu `documents` klucza, który składa się z listy dokumentów. Każdy dokument jest spójną kolekcją składająca się z elementów `id` i `text` do przeanalizowania oraz elementem `language` tekstu. 

```python
documents = {"documents" : [
  {"id": "1", "language": "en", "text": "I had a wonderful experience! The rooms were wonderful and the staff was helpful."},
  {"id": "2", "language": "en", "text": "I had a terrible time at the hotel. The staff was rude and the food was awful."},  
  {"id": "3", "language": "es", "text": "Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos."},  
  {"id": "4", "language": "es", "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."}
]}
```

Biblioteka żądań służy do wysyłania dokumenty do interfejsu API. Dodaj klucz subskrypcji, aby `Ocp-Apim-Subscription-Key` nagłówka i Wyślij żądanie za pomocą `requests.post()`. 

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(sentiment_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

### <a name="output"></a>Dane wyjściowe

Ocenę tonacji dla dokumentu jest od 0,0 do 1,0, z wyższą ocenę wskazująca, bardziej dodatnie wskaźniki nastrojów klientów.

```json
{
  "documents":[
    {
      "id":"1",
      "score":0.9708490371704102
    },
    {
      "id":"2",
      "score":0.0019068121910095215
    },
    {
      "id":"3",
      "score":0.7456425428390503
    },
    {
      "id":"4",
      "score":0.334433376789093
    }
  ],
  "errors":[

  ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Wyodrębnij frazy kluczowe
 
Aby wyodrębnić kluczowe frazy z zestaw dokumentów, należy dołączyć `keyPhrases` do podstawowego punktu końcowego analizy tekstu w celu utworzenia adresu URL wykrywanie języka. Na przykład: `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
    
```python
keyphrase_url = text_analytics_base_url + "keyPhrases"
```

Ten zbiór dokumentów, jest taka sama używane na przykład analizy tonacji.

```python
documents = {"documents" : [
  {"id": "1", "language": "en", "text": "I had a wonderful experience! The rooms were wonderful and the staff was helpful."},
  {"id": "2", "language": "en", "text": "I had a terrible time at the hotel. The staff was rude and the food was awful."},  
  {"id": "3", "language": "es", "text": "Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos."},  
  {"id": "4", "language": "es", "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."}
]}
```

Biblioteka żądań służy do wysyłania dokumenty do interfejsu API. Dodaj klucz subskrypcji, aby `Ocp-Apim-Subscription-Key` nagłówka i Wyślij żądanie za pomocą `requests.post()`. 

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(keyphrase_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

### <a name="output"></a>Dane wyjściowe

```json
{
  "documents":[
    {
      "keyPhrases":[
        "wonderful experience",
        "staff",
        "rooms"
      ],
      "id":"1"
    },
    {
      "keyPhrases":[
        "food",
        "terrible time",
        "hotel",
        "staff"
      ],
      "id":"2"
    },
    {
      "keyPhrases":[
        "Monte Rainier",
        "caminos"
      ],
      "id":"3"
    },
    {
      "keyPhrases":[
        "carretera",
        "tráfico",
        "día"
      ],
      "id":"4"
    }
  ],
  "errors":[

  ]
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Identyfikowanie jednostek

Aby zidentyfikować dobrze znanych jednostek (osoby, miejsca i rzeczy) w dokumentach tekstu, należy dołączyć `keyPhrases` do podstawowego punktu końcowego analizy tekstu w celu utworzenia adresu URL wykrywanie języka. Na przykład: `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
    
```python
entities_url = text_analytics_base_url + "keyPhrases"
```

Utwórz zbiór dokumentów, podobnie jak w poprzednich przykładach. 

```python
documents = {"documents" : [
  {"id": "1", "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."}
]}
```

Biblioteka żądań służy do wysyłania dokumenty do interfejsu API. Dodaj klucz subskrypcji, aby `Ocp-Apim-Subscription-Key` nagłówka i Wyślij żądanie za pomocą `requests.post()`.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(entities_url, headers=headers, json=documents)
entities = response.json()
```

### <a name="output"></a>Dane wyjściowe

```json
{
  "documents":[
    {
      "id":"1",
      "keyPhrases":[
        "Bill Gates",
        "Paul Allen",
        "BASIC interpreters",
        "Altair",
        "Microsoft"
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
