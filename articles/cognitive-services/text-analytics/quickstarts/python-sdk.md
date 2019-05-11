---
title: 'Szybki start: Wywoływanie usługi Analiza tekstu przy użyciu zestawu SDK języka Python'
titleSuffix: Azure Cognitive Services
description: Pobierz informacje oraz przykłady kodu w celu szybkiego Rozpocznij pracę przy użyciu interfejsu API analizy tekstu w usługach Azure Cognitive Services.
services: cognitive-services
author: ctufts
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: aahi
ms.openlocfilehash: 2e9e26243511972c7d05dc55c8e041d83efcbc7d
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546587"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-python-sdk"></a>Szybki start: Wywoływanie usługi Analiza tekstu przy użyciu zestawu SDK języka Python 
<a name="HOLTop"></a>

Użyj tego przewodnika Szybki Start, aby rozpocząć analizowanie języka przy użyciu zestawu SDK analizy tekstu w języku Python. Gdy interfejs API REST analizy tekstu jest zgodny z większość języków programowania, zestaw SDK udostępnia łatwy sposób zintegrować usługę ze swoimi aplikacjami bez serializację i deserializację formatu JSON.


> [!Note]
> * Zapoznaj się z tematem [API definitions (Definicje interfejsu API)](//go.microsoft.com/fwlink/?LinkID=759346), zawierającym dokumentację techniczną interfejsów API.
> * Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py).  

## <a name="prerequisites"></a>Wymagania wstępne

* [Python 3.x](https://www.python.org/)

* Analiza tekstu [zestawu SDK dla języka python](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) można zainstalować pakiet przy użyciu:

    `pip install --upgrade azure-cognitiveservices-language-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Musisz również posiadać [punktu końcowego i klucza dostępu](../How-tos/text-analytics-how-to-access-key.md) które zostały wygenerowane automatycznie podczas tworzenia konta.

## <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nową aplikację języka Python w ulubionym edytorze lub w środowisku IDE. Następnie dodaj następujące instrukcje import do pliku.

```python
from azure.cognitiveservices.language.textanalytics import TextAnalyticsClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="authenticate-your-credentials"></a>Uwierzytelnienia poświadczeń użytkownika

> [!Tip]
> W bezpieczne wdrażanie kluczy tajnych w systemach produkcyjnych zalecamy używanie [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

Po wprowadzeniu zmienną swój klucz subskrypcji Text Analytics należy utworzyć wystąpienie `CognitiveServicesCredentials` obiektu z nim.

```python
subscription_key = "enter-your-key-here"
credentials = CognitiveServicesCredentials(subscription_key)
```

## <a name="create-a-text-analytics-client"></a>Tworzenie klienta analizy tekstu

Utwórz nową `TextAnalyticsClient` obiekt z `credentials` i `text_analytics_url` jako parametr. Użyj poprawny region platformy Azure dla Twojej subskrypcji analizy tekstu (na przykład `westcentralus`).

```
text_analytics_url = "https://westcentralus.api.cognitive.microsoft.com/"
text_analytics = TextAnalyticsClient(endpoint=text_analytics_url, credentials=credentials)
```

## <a name="sentiment-analysis"></a>Analiza tonacji

Ładunek do interfejsu API składa się z listy `documents`, które są słowników zawiera `id` i `text` atrybutu. `text` Atrybut przechowuje tekst, który ma być analizowane i `id` może być dowolna wartość. 

```python
documents = [
  {
    "id": "1", 
    "language": "en", 
    "text": "I had the best day of my life."
  },
  {
    "id": "2", 
    "language": "en", 
    "text": "This was a waste of my time. The speaker put me to sleep."
  },  
  {
    "id": "3", 
    "language": "es", 
    "text": "No tengo dinero ni nada que dar..."
  },  
  {
    "id": "4", 
    "language": "it", 
    "text": "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
  }
]
```

Wywołaj `sentiment()` funkcji i uzyskać wynik. Następnie iteracyjnego przeglądania wyników i Drukuj identyfikator każdego dokumentu i ocenę tonacji. Podczas oceny bliższa 1 oznacza pozytywną tonację, wyniku przybliżania 0 wskazuje negatywną tonację.

```python
response = text_analytics.sentiment(documents=documents)
for document in response.documents:
     print("Document Id: ", document.id, ", Sentiment Score: ", "{:.2f}".format(document.score))
```

### <a name="output"></a>Dane wyjściowe

```console
Document Id:  1 , Sentiment Score:  0.87
Document Id:  2 , Sentiment Score:  0.11
Document Id:  3 , Sentiment Score:  0.44
Document Id:  4 , Sentiment Score:  1.00
```

## <a name="language-detection"></a>Wykrywanie języka

Utwórz listę słowników, każdy z nich zawiera dokument, który chcesz analizować. `text` Atrybut przechowuje tekst, który ma być analizowane i `id` może być dowolna wartość. 

```python
documents = [
    { 
        'id': '1', 
        'text': 'This is a document written in English.' 
    },
    {
        'id': '2', 
        'text': 'Este es un document escrito en Español.' 
    },
    { 
        'id': '3', 
        'text': '这是一个用中文写的文件' 
    }
]
``` 

Za pomocą klienta utworzony wcześniej, wywołaj `detect_language()` i uzyskać wynik. Następnie iteracyjnego przeglądania wyników i Drukuj identyfikator każdego dokumentu i zwrócony pierwszy język.

```python
response = text_analytics.detect_language(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id , ", Language: ", document.detected_languages[0].name)
```

### <a name="output"></a>Dane wyjściowe

```console
Document Id:  1 , Language:  English
Document Id:  2 , Language:  Spanish
Document Id:  3 , Language:  Chinese_Simplified
```

## <a name="entity-recognition"></a>Rozpoznawanie jednostek

Utwórz listę słowników, zawierającą dokumenty, które mają być analizowane. `text` Atrybut przechowuje tekst, który ma być analizowane i `id` może być dowolna wartość. 


```python
documents = [
    {
        "id": "1",
        "language": "en", 
        "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."
    },
    {
        "id": "2",
        "language": "es", 
        "text": "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    }
]
```

Za pomocą klienta utworzony wcześniej, wywołaj `entities()` funkcji i uzyskać wynik. Następnie iteracyjnego przeglądania wyników i Drukuj identyfikator każdego dokumentu i jednostki zawarte w nim.

```python
response = text_analytics.entities(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Entities:")
    for entity in document.entities:
        print("\t\t", "NAME: ",entity.name, "\tType: ", entity.type, "\tSub-type: ", entity.sub_type)
        for match in entity.matches:
            print("\t\t\tOffset: ", match.offset, "\tLength: ", match.length, "\tScore: ",
                  "{:.2f}".format(match.entity_type_score))
```


### <a name="output"></a>Dane wyjściowe

```console
Document Id:  1
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  0  Length:  9  Score:  1.00
         NAME:  Bill Gates  Type:  Person   Sub-type:  None
            Offset:  25     Length:  10     Score:  1.00
         NAME:  Paul Allen  Type:  Person   Sub-type:  None
            Offset:  40     Length:  10     Score:  1.00
         NAME:  April 4     Type:  Other    Sub-type:  None
            Offset:  54     Length:  7  Score:  0.80
         NAME:  April 4, 1975   Type:  DateTime     Sub-type:  Date
            Offset:  54     Length:  13     Score:  0.80
         NAME:  BASIC   Type:  Other    Sub-type:  None
            Offset:  89     Length:  5  Score:  0.80
         NAME:  Altair 8800     Type:  Other    Sub-type:  None
            Offset:  116    Length:  11     Score:  0.80
Document Id:  2
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  21     Length:  9  Score:  1.00
         NAME:  Redmond (Washington)    Type:  Location     Sub-type:  None
            Offset:  60     Length:  7  Score:  0.99
         NAME:  21 kilómetros   Type:  Quantity     Sub-type:  Dimension
            Offset:  71     Length:  13     Score:  0.80
         NAME:  Seattle     Type:  Location     Sub-type:  None
            Offset:  88     Length:  7  Score:  1.00
```

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

Utwórz listę słowników, zawierającą dokumenty, które mają być analizowane. `text` Atrybut przechowuje tekst, który ma być analizowane i `id` może być dowolna wartość. 


```python
documents = [
    {
        "id": "1", 
        "language": "ja", 
        "text": "猫は幸せ"
    },
    {
        "id": "2", 
        "language": "de", 
        "text": "Fahrt nach Stuttgart und dann zum Hotel zu Fu."
    },
    {
        "id": "3", 
        "language": "en",
        "text": "My cat might need to see a veterinarian."
    },
    {
        "id": "4", 
        "language": "es", 
        "text": "A mi me encanta el fútbol!"
    }
]
```

Za pomocą klienta utworzony wcześniej, wywołaj `key_phrases()` funkcji i uzyskać wynik. Następnie iteracyjnego przeglądania wyników i Drukuj identyfikator każdego dokumentu i kluczowych fraz zawartych w nim.

```python
response = text_analytics.key_phrases(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Phrases:")
    for phrase in document.key_phrases:
        print("\t\t",phrase)
```

### <a name="output"></a>Dane wyjściowe

```console
Document Id:  1
    Phrases:
         幸せ
Document Id:  2
    Phrases:
         Stuttgart
         Hotel
         Fahrt
         Fu
Document Id:  3
    Phrases:
         cat
         veterinarian
Document Id:  4
    Phrases:
         fútbol
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Analiza tekstu przy użyciu usługi Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Zobacz także

* [Co to jest interfejs API analizy tekstu?](../overview.md)
* [Przykładowe scenariusze użytkownika](../text-analytics-user-scenarios.md)
* [Często zadawane pytania](../text-analytics-resource-faq.md)
