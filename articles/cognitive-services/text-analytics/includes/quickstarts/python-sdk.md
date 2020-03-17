---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: 0803a847e9e864b361917df9f1a9c6b059ca2fe9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79203429"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

[Dokumentacja referencyjna wersji 3](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b2/azure.ai.textanalytics.html) | [Kod źródłowy biblioteki w wersji 3](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [Pakiet wersji 3 (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [Przykłady wersji 3](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

[Dokumentacja referencyjna wersji 2](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Kod źródłowy biblioteki w wersji 2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [Pakiet wersji 2 (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [Przykłady wersji 2](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatnie](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* Gdy już będziesz mieć subskrypcję platformy Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Tworzenie zasobu analizy tekstu"  target="_blank">utwórz zasób analizy tekstu <span class="docon docon-navigate-external x-hidden-focus"></span></a> w witrynie Azure Portal, aby uzyskać klucz i punkt końcowy. 
    * Klucz i punkt końcowy z utworzonego zasobu będą potrzebne do połączenia aplikacji z interfejsem API analizy tekstu. Tę czynność wykonasz w dalszej części przewodnika Szybki start.
    * W celu wypróbowania usługi możesz użyć warstwy cenowej Bezpłatna, a później zaktualizować ją do płatnej warstwy na potrzeby środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Po zainstalowaniu środowiska Python możesz zainstalować bibliotekę klienta przy użyciu następującego polecenia:

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

```console
pip install azure-ai-textanalytics
```

> [!TIP]
> Czy chcesz wyświetlić cały plik z kodem przewodnika Szybki start od razu? Można go znaleźć [w witrynie GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py), która zawiera przykłady kodu z tego przewodnika Szybki start. 

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

> [!TIP]
> Czy chcesz wyświetlić cały plik z kodem przewodnika Szybki start od razu? Można go znaleźć [w witrynie GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py), która zawiera przykłady kodu z tego przewodnika Szybki start. 

---

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nowy plik w języku Python, a następnie utwórz zmienne dla klucza subskrypcji i punktu końcowego platformy Azure zasobu.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Model obiektów

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Klient analizy tekstu jest obiektem `TextAnalyticsClient` uwierzytelnianym na platformie Azure przy użyciu klucza. Klient udostępnia kilka metod zbiorczego analizowania tekstu. 

Tekst do przetwarzania wsadowego jest wysyłany do interfejsu API jako lista elementów `documents`, które są obiektami `dictionary` zawierającymi kombinację atrybutów `id`, `text` i `language` w zależności od używanej metody. Atrybut `text` przechowuje tekst, który ma zostać przeanalizowany, w języku źródłowym (określonym przez atrybut `language`), a atrybut `id` może mieć dowolną wartość. 

Obiekt odpowiedzi to lista zawierająca informacje o analizie dla każdego dokumentu. 

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Klient analizy tekstu jest obiektem [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) uwierzytelnianym na platformie Azure przy użyciu klucza. Klient udostępnia kilka metod analizowania tekstu, jako pojedynczego ciągu lub zbiorczo. 

Tekst jest wysyłany do interfejsu API jako lista elementów `documents`, które są obiektami `dictionary` zawierającymi kombinację atrybutów `id`, `text` i `language` w zależności od używanej metody. Atrybut `text` przechowuje tekst, który ma zostać przeanalizowany, w języku źródłowym (określonym przez atrybut `language`), a atrybut `id` może mieć dowolną wartość. 

---

## <a name="code-examples"></a>Przykłady kodu

Poniższe fragmenty kodu przedstawiają sposób wykonywania następujących zadań za pomocą biblioteki klienta analizy tekstu dla języka Python:

* [Uwierzytelnianie użytkownika](#authenticate-the-client)
* [Analiza tonacji](#sentiment-analysis)
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie jednostek nazwanych](#named-entity-recognition-ner) 
* [Łączenie jednostek](#entity-linking)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Uwierzytelnianie użytkownika

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Utwórz funkcję, aby utworzyć wystąpienie obiektu `TextAnalyticsClient` przy użyciu utworzonych powyżej wartości `key` i `endpoint`. Następnie utwórz nowego klienta. 

```python
from azure.ai.textanalytics import TextAnalyticsClient, TextAnalyticsApiKeyCredential

def authenticate_client():
    ta_credential = TextAnalyticsApiKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

[!code-python[imports statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

Utwórz funkcję, aby utworzyć wystąpienie obiektu `TextAnalyticsClient` przy użyciu utworzonych powyżej wartości `key` i `endpoint`. Następnie utwórz nowego klienta. 

[!code-python[version 2 authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

--- 

## <a name="sentiment-analysis"></a>Analiza tonacji

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Utwórz nową funkcję o nazwie `sentiment_analysis_example()`, która przyjmuje klienta jako argument, a następnie wywołuje funkcję `analyze_sentiment()`. Zwrócony obiekt odpowiedzi będzie zawierać ocenę i etykietę tonacji dla całego dokumentu wejściowego, a także analizę tonacji dla każdego zdania.


```python
def sentiment_analysis_example(client):

    document = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(inputs=document)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("[Length: {}]".format(sentence.grapheme_length))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))

            
sentiment_analysis_example(client)
```

### <a name="output"></a>Dane wyjściowe

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00

[Length: 30]
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

[Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Uwierzytelnij obiekt klienta i wywołaj funkcję [sentiment()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-). Wykonaj iterację po wynikach, a następnie wydrukuj identyfikator każdego dokumentu i ocenę tonacji. Ocena bliższa 0 wskazuje negatywną tonację, natomiast ocena bliższa 1 tonację pozytywną.

[!code-python[sentiment analysis](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=sentimentAnalysis)]

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

---

## <a name="language-detection"></a>Wykrywanie języka

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Utwórz nową funkcję o nazwie `language_detection_example()`, która przyjmuje klienta jako argument, a następnie wywołuje funkcję `detect_language()`. Zwrócony obiekt odpowiedzi będzie zawierać wykryty język w elemencie `primary_language` w przypadku powodzenia lub element `error` w przypadku niepowodzenia.

> [!Tip]
> W niektórych przypadkach może być trudno odróżnić języki w oparciu o dane wejściowe. Za pomocą parametru `country_hint` możesz podać 2-literowy kod kraju. Domyślnie interfejs API używa wartości „US” jako domyślnego ustawienia countryHint. Aby usunąć to zachowanie, możesz zresetować ten parametr, ustawiając wartość na pusty ciąg `country_hint : ""`. 

```python
def language_detection_example(client):
    try:
        document = ["Ce document est rédigé en Français."]
        response = client.detect_language(inputs = document, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Dane wyjściowe

```console
Language:  French
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Za pomocą utworzonego wcześniej klienta wywołaj funkcję [detect_language()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) i pobierz wynik. Następnie wykonaj iterację po wynikach oraz wydrukuj identyfikator każdego dokumentu i pierwszy zwrócony język.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Rozpoznawanie jednostek nazwanych

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

> [!NOTE]
> W wersji `3.0-preview`:
> * Rozpoznawanie jednostek nazwanych obejmuje oddzielne metody wykrywania danych osobowych. 
> * Łączenie jednostek to żądanie inne niż rozpoznawanie jednostek nazwanych.

Utwórz nową funkcję o nazwie `entity_recognition_example`, która przyjmuje klienta jako argument, a następnie wywołuje funkcję `recognize_entities()` i wykonuje iterację po wynikach. Zwrócony obiekt odpowiedzi będzie zawierać listę wykrytych jednostek w elemencie `entity` w przypadku powodzenia lub element `error` w przypadku niepowodzenia. Dla każdej wykrytej jednostki wydrukuj jej kategorię i podkategorię (jeśli istnieje).

```python
def entity_recognition_example(client):

    try:
        document = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(inputs= document)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tLength: \t", entity.grapheme_length, "\tConfidence Score: \t", round(entity.score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Dane wyjściowe

```console
Named Entities:

    Text:    Seattle        Category:        Location       SubCategory:     GPE
    Length:          7      Confidence Score:        0.92

    Text:    last week      Category:        DateTime       SubCategory:     DateRange
    Length:          9      Confidence Score:        0.8
```

## <a name="using-ner-to-detect-personal-information"></a>Wykrywanie danych osobowych za pomocą rozpoznawania jednostek nazwanych

Utwórz nową funkcję o nazwie `entity_pii_example()`, która przyjmuje klienta jako argument, a następnie wywołuje funkcję `recognize_pii_entities()` i pobiera wynik. Następnie wykonaj iterację po wynikach i wydrukuj jednostki.

```python
def entity_pii_example(client):

        document = ["Insurance policy for SSN on file 123-12-1234 is here by approved."]


        result = client.recognize_pii_entities(inputs= document)[0]
        
        print("Personally Identifiable Information Entities: ")
        for entity in result.entities:
            print("\tText: ",entity.text,"\tCategory: ", entity.category,"\tSubCategory: ", entity.subcategory)
            print("\t\tLength: ", entity.grapheme_length, "\tScore: {0:.2f}".format(entity.score), "\n")
        
entity_pii_example(client)
```

### <a name="output"></a>Dane wyjściowe

```console
Personally Identifiable Information Entities:
    Text:  123-12-1234      Category:  U.S. Social Security Number (SSN)    SubCategory:  None
        Length:  11     Score: 0.85
```


## <a name="entity-linking"></a>Łączenie jednostek

Utwórz nową funkcję o nazwie `entity_linking_example()`, która przyjmuje klienta jako argument, a następnie wywołuje funkcję `recognize_linked_entities()` i wykonuje iterację po wynikach. Zwrócony obiekt odpowiedzi będzie zawierać listę wykrytych jednostek w elemencie `entities` w przypadku powodzenia lub element `error` w przypadku niepowodzenia. Ponieważ połączone jednostki są jednoznacznie identyfikowane, wystąpienia tej samej jednostki są grupowane w ramach obiektu `entity` jako lista obiektów `match`.

```python
def entity_linking_example(client):

    try:
        document = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(inputs= document)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tScore: {0:.2f}".format(match.score), "\tLength: {}\n".format(match.grapheme_length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Dane wyjściowe

```console
Linked Entities:

    Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Score: 0.78     Length: 11

    Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Score: 0.55     Length: 10

        Text: Gates
        Score: 0.55     Length: 5

    Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Score: 0.53     Length: 10

    Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Score: 0.47     Length: 9

        Text: Microsoft
        Score: 0.47     Length: 9

    Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Score: 0.25     Length: 7

    Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Score: 0.28     Length: 5

```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

> [!NOTE]
> W wersji 2.1 odpowiedź rozpoznawania jednostek nazwanych obejmuje łączenie jednostek.

Za pomocą utworzonego wcześniej klienta wywołaj funkcję [entities()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) i pobierz wynik. Następnie wykonaj iterację po wynikach oraz wydrukuj identyfikator każdego dokumentu i znajdujące się w nim jednostki.

[!code-python[Entity recognition](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=entityRecognition)]

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz


#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Utwórz nową funkcję o nazwie `key_phrase_extraction_example()`, która przyjmuje klienta jako argument, a następnie wywołuje funkcję `extract_key_phrases()`. Wynik będzie zawierać listę wykrytych fraz kluczowych w elemencie `key_phrases` w przypadku powodzenia lub element `error` w przypadku niepowodzenia. Wydrukuj wszystkie wykryte frazy kluczowe.

```python
def key_phrase_extraction_example(client):

    try:
        document = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(inputs= document)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Dane wyjściowe

```console
    Key Phrases:
         cat
         veterinarian
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Za pomocą utworzonego wcześniej klienta wywołaj funkcję [key_phrases()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) i pobierz wynik. Następnie wykonaj iterację po wynikach oraz wydrukuj identyfikator każdego dokumentu i znajdujące się w nim frazy kluczowe.

[!code-python[key phrase extraction](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=keyPhrases)]


### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```

---