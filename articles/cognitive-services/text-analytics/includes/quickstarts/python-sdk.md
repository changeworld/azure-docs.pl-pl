---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 09/05/2019
ms.author: aahi
ms.openlocfilehash: 6d4076563ac803c72f3749399bed6aaa235a0343
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795582"
---
<a name="HOLTop"></a>


[Dokumentacja referencyjna](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) |  | pakietu [kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) [(PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) [ | ](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-text-analytics-azure-resource"></a>Tworzenie zasobu analiza tekstu platformy Azure

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Po zainstalowaniu języka Python można zainstalować bibliotekę kliencką z:

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nowy plik w języku Python i zaimportuj następujące biblioteki.

[!code-python[import statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

Utwórz zmienne dla punktu końcowego platformy Azure i klucza subskrypcji zasobu. Uzyskaj te wartości ze zmiennych środowiskowych TEXT_ANALYTICS_SUBSCRIPTION_KEY i TEXT_ANALYTICS_ENDPOINT. Jeśli te zmienne środowiskowe zostały utworzone po rozpoczęciu edytowania aplikacji, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę używaną w celu uzyskania dostępu do zmiennych.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

[!code-python[endpoint and key variables](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=initialVars)]

## <a name="object-model"></a>Model obiektów

Klient analiza tekstu jest obiektem [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) , który jest uwierzytelniany na platformie Azure przy użyciu klucza. Klient udostępnia kilka metod analizowania tekstu, w postaci pojedynczego ciągu lub partii. 

Tekst jest wysyłany do interfejsu API jako lista `documents`, które są `dictionary` obiektów zawierających kombinację atrybutów `id`, `text`i `language` w zależności od używanej metody. Atrybut `text` przechowuje tekst, który ma być analizowany w `language`źródłowym, a `id` może być dowolną wartością. 

Obiekt Response jest listą zawierającą informacje o analizie dla każdego dokumentu. 

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta analiza tekstu dla języka Python:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [analiza tonacji](#sentiment-analysis)
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie jednostek](#entity-recognition)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz nowy [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) z punktem końcowym i obiektem `CognitiveServicesCredentials` zawierającym klucz.

[!code-python[client authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

## <a name="sentiment-analysis"></a>Analiza opinii

Uwierzytelnij obiekt klienta i wywołaj funkcję [tonacji ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) . Wykonaj iterację w wynikach i wydrukuj identyfikatory każdego dokumentu oraz tonacji ocenę. Wynik zbliżony do 0 wskazuje negatywną tonacji, natomiast wynik zbliżony do 1 wskazuje pozytywny tonacji.

[!code-python[sentiment analysis](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=sentimentAnalysis)]

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Wykrywanie języka

Korzystając z utworzonego wcześniej klienta, wywołaj [detect_language ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) i Pobierz wynik. Następnie można wykonać iterację w wynikach i wydrukować identyfikator każdego dokumentu oraz pierwszy zwracany język.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Rozpoznawanie jednostek

Za pomocą utworzonego wcześniej klienta wywołaj funkcję [Entities ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) i Pobierz wynik. Następnie można wykonać iterację w wynikach i wydrukować identyfikator każdego dokumentu oraz jednostki zawarte w nim.

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

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

Za pomocą utworzonego wcześniej klienta wywołaj funkcję [key_phrases ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) i uzyskaj wynik. Następnie można wykonać iterację w wynikach i wydrukować identyfikator każdego dokumentu oraz zawarte w nim kluczowe frazy.

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
