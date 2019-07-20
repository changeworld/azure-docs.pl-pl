---
title: 'Szybki start: Wywoływanie usługi analiza tekstu przy użyciu zestawu SDK języka Python'
titleSuffix: Azure Cognitive Services
description: Uzyskaj informacje i przykłady kodu, aby szybko rozpocząć korzystanie z interfejs API analizy tekstu na platformie Azure Cognitive Services.
services: cognitive-services
author: ctufts
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: aahi
ms.openlocfilehash: c24979d9aef74b6cc840427a010b9ce70f2c0b8a
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356949"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-python-sdk"></a>Szybki start: Wywoływanie usługi analiza tekstu przy użyciu zestawu SDK języka Python 
<a name="HOLTop"></a>

Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć analizowanie języka za pomocą zestawu analiza tekstu SDK dla języka Python. Chociaż interfejs API REST analiza tekstu jest zgodny z większością języków programowania, zestaw SDK zapewnia łatwy sposób integracji usługi z aplikacjami bez serializowania i deserializacji kodu JSON. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py).

## <a name="prerequisites"></a>Wymagania wstępne

* [Python 3.x](https://www.python.org/)

* Zestaw analiza tekstu [SDK dla języka Python](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) można zainstalować pakiet przy użyciu:

    `pip install --upgrade azure-cognitiveservices-language-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Musisz również mieć [punkt końcowy i klucz dostępu](../How-tos/text-analytics-how-to-access-key.md) , które zostały wygenerowane podczas rejestracji.

## <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nową aplikację w języku Python w ulubionym edytorze lub w środowisku IDE. Następnie Dodaj następujące instrukcje importu do pliku.

```python
from azure.cognitiveservices.language.textanalytics import TextAnalyticsClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="authenticate-your-credentials"></a>Uwierzytelnianie poświadczeń

> [!Tip]
> W celu bezpiecznego wdrożenia wpisów tajnych w systemach produkcyjnych zalecamy używanie [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

Po wprowadzeniu zmiennej dla klucza subskrypcji analiza tekstu Utwórz wystąpienie `CognitiveServicesCredentials` obiektu z nim.

```python
subscription_key = "enter-your-key-here"
credentials = CognitiveServicesCredentials(subscription_key)
```

## <a name="create-a-text-analytics-client"></a>Tworzenie klienta analiza tekstu

Utwórz nowy `TextAnalyticsClient` obiekt z `credentials` i `text_analytics_url` jako parametr. Użyj poprawnego regionu platformy Azure dla subskrypcji analiza tekstu (na `westcentralus`przykład).

```
text_analytics_url = "https://westcentralus.api.cognitive.microsoft.com/"
text_analytics = TextAnalyticsClient(endpoint=text_analytics_url, credentials=credentials)
```

## <a name="sentiment-analysis"></a>Analiza tonacji

Ładunek do interfejsu API składa się z listy `documents`, które są słownikami `id` zawierającymi `text` atrybut i. Ten `text` atrybut przechowuje tekst do przeanalizowania, `id` a może być dowolną wartością. 

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

Wywołaj `sentiment()` funkcję i uzyskaj wynik. Następnie można wykonać iterację w wynikach i wydrukować identyfikator każdego dokumentu oraz tonacji ocenę. Wynik zbliżony do 0 wskazuje negatywną tonacji, natomiast wynik zbliżony do 1 wskazuje pozytywny tonacji.

```python
response = text_analytics.sentiment(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id, ", Sentiment Score: ",
          "{:.2f}".format(document.score))
```

### <a name="output"></a>Output

```console
Document Id:  1 , Sentiment Score:  0.87
Document Id:  2 , Sentiment Score:  0.11
Document Id:  3 , Sentiment Score:  0.44
Document Id:  4 , Sentiment Score:  1.00
```

## <a name="language-detection"></a>Wykrywanie języka

Utwórz listę słowników zawierającą dokument, który chcesz analizować. Ten `text` atrybut przechowuje tekst do przeanalizowania, `id` a może być dowolną wartością. 

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

Przy użyciu utworzonego wcześniej klienta należy wywołać `detect_language()` i uzyskać wynik. Następnie można wykonać iterację w wynikach i wydrukować identyfikator każdego dokumentu oraz pierwszy zwracany język.

```python
response = text_analytics.detect_language(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id, ", Language: ",
          document.detected_languages[0].name)
```

### <a name="output"></a>Output

```console
Document Id:  1 , Language:  English
Document Id:  2 , Language:  Spanish
Document Id:  3 , Language:  Chinese_Simplified
```

## <a name="entity-recognition"></a>Rozpoznawanie jednostek

Utwórz listę słowników zawierającą dokumenty, które chcesz przeanalizować. Ten `text` atrybut przechowuje tekst do przeanalizowania, `id` a może być dowolną wartością. 


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

Przy użyciu wcześniej utworzonego klienta należy wywołać `entities()` funkcję i uzyskać wynik. Następnie można wykonać iterację w wynikach i wydrukować identyfikator każdego dokumentu oraz jednostki zawarte w nim.

```python
response = text_analytics.entities(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Entities:")
    for entity in document.entities:
        print("\t\t", "NAME: ", entity.name, "\tType: ",
              entity.type, "\tSub-type: ", entity.sub_type)
        for match in entity.matches:
            print("\t\t\tOffset: ", match.offset, "\tLength: ", match.length, "\tScore: ",
                  "{:.2f}".format(match.entity_type_score))
```


### <a name="output"></a>Output

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

Utwórz listę słowników zawierającą dokumenty, które chcesz przeanalizować. Ten `text` atrybut przechowuje tekst do przeanalizowania, `id` a może być dowolną wartością. 


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

Przy użyciu wcześniej utworzonego klienta należy wywołać `key_phrases()` funkcję i uzyskać wynik. Następnie można wykonać iterację w wynikach i wydrukować identyfikator każdego dokumentu oraz zawarte w nim kluczowe frazy.

```python
response = text_analytics.key_phrases(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Phrases:")
    for phrase in document.key_phrases:
        print("\t\t", phrase)
```

### <a name="output"></a>Output

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
