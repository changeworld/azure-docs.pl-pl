---
title: 'Szybki start: biblioteka klientów analizy tekstu dla Go | Dokumenty firmy Microsoft'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start wykryj język przy użyciu biblioteki klienta Go Text Analytics z usługi Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: aahi
ms.openlocfilehash: 0b4495616c750b2b3e8431e011d71ae8671af1ef
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77912648"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-go"></a>Szybki start: korzystanie z biblioteki klienta analizy tekstu dla Go

[Przykłady](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | referencyjnej[(GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [Samples](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

> [!NOTE]
> Ten szybki start dotyczy tylko analizy tekstu w wersji 2.1. Obecnie biblioteka klienta w wersji 3 dla go jest niedostępna.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)
* Najnowsza wersja [programu Go](https://golang.org/dl/)
* Po utworzeniu subskrypcji <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="platformy Azure utwórz"  target="_blank">zasób <span class="docon docon-navigate-external x-hidden-focus"></span> </a> analizy tekstu w witrynie Azure portal, aby uzyskać klucz i punkt końcowy. 
    * Potrzebny będzie klucz i punkt końcowy z zasobu, który tworzysz, aby połączyć aplikację z interfejsem API analizy tekstu. Zrobisz to później w przewodniku Szybki start.
    * Można użyć bezpłatnej warstwy cenowej, aby wypróbować usługę i uaktualnić później do warstwy płatnej dla produkcji.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-go-project"></a>Tworzenie nowego projektu Go

W oknie konsoli (cmd, PowerShell, Terminal, Bash) utwórz nowy obszar roboczy dla projektu Go i przejdź do niego. Obszar roboczy będzie zawierał trzy foldery: 

* **src** - Ten katalog zawiera kod źródłowy i pakiety. Wszystkie pakiety `go get` zainstalowane za pomocą polecenia znajdą się tutaj.
* **pkg** — ten katalog zawiera skompilowane obiekty pakietu Go. Wszystkie te pliki `.a` mają rozszerzenie.
* **bin** — ten katalog zawiera binarne pliki wykonywalne, które są tworzone po uruchomieniu `go install`.

> [!TIP]
> Dowiedz się więcej o strukturze [obszaru roboczego Go](https://golang.org/doc/code.html#Workspaces). Ten przewodnik zawiera `$GOPATH` informacje `$GOROOT`dotyczące ustawień i .

Tworzenie obszaru `my-app` roboczego wywoływanego i wymaganego podkatastrojów dla `src`, `pkg`i `bin`:

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>Instalowanie biblioteki klienta analizy tekstu dla go

Zainstaluj bibliotekę klienta dla Go: 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

a jeśli używasz programu dep, w ramach repozytorium uruchom:

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>Tworzenie aplikacji Go

Następnie utwórz plik `src/quickstart.go`o nazwie :

```bash
$ cd src
$ touch quickstart.go
```

Otwórz `quickstart.go` w ulubionym edytorze IDE lub tekstu. Następnie dodaj nazwę pakietu i zaimportuj następujące biblioteki:

[!code-go[Import statements](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=imports)]

## <a name="object-model"></a>Model obiektu 

Klient analizy tekstu jest obiektem [BaseClient,](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) który uwierzytelnia się na platformie Azure przy użyciu klucza. Klient udostępnia kilka metod analizowania tekstu, jako pojedynczy ciąg lub partii. 

Tekst jest wysyłany do interfejsu `documents`API `dictionary` jako lista , `id`które `text`są `language` obiektami zawierającymi kombinację , i atrybutów w zależności od zastosowanej metody. Atrybut `text` przechowuje tekst do analizy w `language`źródle i `id` może być dowolną wartością. 

Obiekt odpowiedzi jest listą zawierającą informacje analityczne dla każdego dokumentu. 

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta analizy tekstu dla języka Python:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Analiza tonacji](#sentiment-analysis)
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie jednostek](#entity-recognition)
* [Wyodrębnianie fraz kluczowych](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta


W nowej funkcji utwórz zmienne dla punktu końcowego i klucza subskrypcji platformy Azure zasobu.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

Utwórz nowy [obiekt BaseClient.](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) Przekaż klucz do [autorest. NewCognitiveServicesAuthorizer()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) funkcja, która zostanie następnie przekazana `authorizer` do właściwości klienta.

```go
func GetTextAnalyticsClient() textanalytics.BaseClient {
    var key string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"

    textAnalyticsClient := textanalytics.New(endpoint)
    textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(key)

    return textAnalyticsClient
}
```

## <a name="sentiment-analysis"></a>Analiza tonacji

Utwórz nową `SentimentAnalysis()` funkcję wywołaną i `GetTextAnalyticsClient()` utwórz klienta przy użyciu metody utworzonej wcześniej. Utwórz listę [multijangieżowychwładnych](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) obiektów zawierających dokumenty, które chcesz analizować. Każdy obiekt będzie `id` `Language` zawierał `text` atrybut i atrybut. Atrybut `text` przechowuje tekst do analizy, `language` jest językiem dokumentu i `id` może być dowolną wartością. 

Wywołanie funkcji [Sentiment()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) klienta i uzyskanie wyniku. Następnie iterować przez wyniki i wydrukować identyfikator każdego dokumentu i wynik tonacji. Wynik bliższy 0 wskazuje na negatywne nastroje, podczas gdy wynik bliższy 1 wskazuje na pozytywne nastroje.

[!code-go[Sentiment analysis sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=sentimentAnalysis)]

w `SentimentAnalysis()` projekcie.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Wykrywanie języka

Utwórz nową `LanguageDetection()` funkcję wywołaną i `GetTextAnalyticsClient()` utwórz klienta przy użyciu metody utworzonej wcześniej. Utwórz listę obiektów [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) zawierającą dokumenty, które chcesz analizować. Każdy obiekt będzie `id` zawierać `text` i atrybut. Atrybut `text` przechowuje tekst do analizy i `id` może być dowolną wartością. 

Zadzwoń do klienta [DetectLanguage()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) i uzyskać wynik. Następnie iteruj przez wyniki i wydrukuj identyfikator każdego dokumentu i wykryty język.

[!code-go[Language detection sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=languageDetection)]

Zadzwoń `LanguageDetection()` do swojego projektu.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 0 , Language: English 
Document ID: 1 , Language: Spanish
Document ID: 2 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Rozpoznawanie jednostek

Utwórz nową `ExtractEntities()` funkcję wywołaną i `GetTextAnalyticsClient()` utwórz klienta przy użyciu metody utworzonej wcześniej. Utwórz listę [multijangieżowychwładnych](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) obiektów zawierających dokumenty, które chcesz analizować. Każdy obiekt będzie `id` `language`zawierał atrybut `text` , i atrybut. Atrybut `text` przechowuje tekst do analizy, `language` jest językiem dokumentu i `id` może być dowolną wartością. 

Zadzwoń do klienta [Entities()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) i uzyskać wynik. Następnie iterować przez wyniki i wydrukować identyfikator każdego dokumentu i wyodrębnione jednostki wynik.

[!code-go[entity recognition sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=entityRecognition)]

w `ExtractEntities()` projekcie.

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

Utwórz nową `ExtractKeyPhrases()` funkcję wywołaną i `GetTextAnalyticsClient()` utwórz klienta przy użyciu metody utworzonej wcześniej. Utwórz listę [multijangieżowychwładnych](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) obiektów zawierających dokumenty, które chcesz analizować. Każdy obiekt będzie `id` `language`zawierał atrybut `text` , i atrybut. Atrybut `text` przechowuje tekst do analizy, `language` jest językiem dokumentu i `id` może być dowolną wartością.

Zadzwoń do klienta [KeyPhrases()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) i uzyskać wynik. Następnie iteruj przez wyniki i wydrukuj identyfikator każdego dokumentu i wyodrębnione frazy kluczowe.

[!code-go[key phrase extraction sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=keyPhrases)]

Zadzwoń `ExtractKeyPhrases()` do swojego projektu.

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
