---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: 75d32cc038d3dbf0f06a844d35a3e626ffaa67f9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446217"
---
[Dokumentacja referencyjna](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [przykłady](https://github.com/Azure-Samples/cognitive-services-quickstart-code) | [kodu źródłowego](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [pakietu (GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Najnowsza wersja języka [go](https://golang.org/dl/)

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-text-analytics-azure-resource"></a>Tworzenie zasobu analiza tekstu platformy Azure 

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="create-a-new-go-project"></a>Utwórz nowy projekt przejdź

W oknie konsoli (cmd, PowerShell, Terminal, bash) Utwórz nowy obszar roboczy dla projektu go i przejdź do niego. Obszar roboczy będzie zawierać trzy foldery: 

* **src** — ten katalog zawiera kod źródłowy i pakiety. Wszystkie pakiety zainstalowane za pomocą polecenia `go get` będą znajdować się w tym miejscu.
* **pkg** — ten katalog zawiera skompilowane obiekty pakietu języka go. Wszystkie te pliki mają rozszerzenie `.a`.
* **bin** — ten katalog zawiera binarne pliki wykonywalne, które są tworzone podczas uruchamiania `go install`.

> [!TIP]
> Dowiedz się więcej o strukturze [obszaru roboczego go](https://golang.org/doc/code.html#Workspaces). Ten przewodnik zawiera informacje dotyczące ustawiania `$GOPATH` i `$GOROOT`.

Utwórz obszar roboczy o nazwie `my-app` i wymagane podkatalogi dla `src`, `pkg`i `bin`:

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>Zainstaluj analiza tekstuą bibliotekę kliencką dla języka go

Zainstaluj bibliotekę kliencką dla języka go: 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

a jeśli używasz programu dep, w ramach repozytorium uruchom:

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>Tworzenie aplikacji języka go

Następnie utwórz plik o nazwie `src/quickstart.go`:

```bash
$ cd src
$ touch quickstart.go
```

Otwórz `quickstart.go` w ulubionym środowisku IDE lub edytorze tekstów. Następnie Dodaj nazwę pakietu i zaimportuj następujące biblioteki:

[!code-go[Import statements](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=imports)]

## <a name="object-model"></a>Model obiektów 

Klient analiza tekstu jest obiektem [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) , który jest uwierzytelniany na platformie Azure przy użyciu klucza. Klient udostępnia kilka metod analizowania tekstu, w postaci pojedynczego ciągu lub partii. 

Tekst jest wysyłany do interfejsu API jako lista `documents`, które są `dictionary` obiektów zawierających kombinację atrybutów `id`, `text`i `language` w zależności od używanej metody. Atrybut `text` przechowuje tekst, który ma być analizowany w `language`źródłowym, a `id` może być dowolną wartością. 

Obiekt Response jest listą zawierającą informacje o analizie dla każdego dokumentu. 

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta analiza tekstu dla języka Python:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Analiza tonacji](#sentiment-analysis)
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie jednostek](#entity-recognition)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta


W nowej funkcji Utwórz zmienne dla punktu końcowego platformy Azure i klucza subskrypcji zasobu.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

Utwórz nowy obiekt [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) . Przekaż klucz do [AutoRest. Funkcja NewCognitiveServicesAuthorizer ()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) , która zostanie następnie przeniesiona do właściwości `authorizer` klienta.

```go
func GetTextAnalyticsClient() textanalytics.BaseClient {
    var key string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"

    textAnalyticsClient := textanalytics.New(endpoint)
    textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(key)

    return textAnalyticsClient
}
```

## <a name="sentiment-analysis"></a>Analiza opinii

Utwórz nową funkcję o nazwie `SentimentAnalysis()` i Utwórz klienta przy użyciu metody `GetTextAnalyticsClient()` utworzonej wcześniej. Utwórz listę obiektów [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) zawierających dokumenty, które chcesz przeanalizować. Każdy obiekt będzie zawierać `id`, `Language` i `text` atrybut. Atrybut `text` przechowuje tekst do przeanalizowania, `language` jest językiem dokumentu, a `id` może być dowolną wartością. 

Wywołaj funkcję [tonacji ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) klienta i uzyskaj wynik. Następnie można wykonać iterację w wynikach i wydrukować identyfikator każdego dokumentu oraz tonacji ocenę. Wynik zbliżony do 0 wskazuje negatywną tonacji, natomiast wynik zbliżony do 1 wskazuje pozytywny tonacji.

[!code-go[Sentiment analysis sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=sentimentAnalysis)]

Wywołaj `SentimentAnalysis()` w projekcie.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Wykrywanie języka

Utwórz nową funkcję o nazwie `LanguageDetection()` i Utwórz klienta przy użyciu metody `GetTextAnalyticsClient()` utworzonej wcześniej. Utwórz listę obiektów [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) zawierających dokumenty, które chcesz przeanalizować. Każdy obiekt będzie zawierać `id` i atrybut `text`. Atrybut `text` przechowuje tekst do przeanalizowania, a `id` może być dowolną wartością. 

Wywołaj DetectLanguage klienta [()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) i Pobierz wynik. Następnie można wykonać iterację w wynikach i wydrukować każdy dokument o IDENTYFIKATORze oraz wykrytym języku.

[!code-go[Language detection sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=languageDetection)]

Wywołaj `LanguageDetection()` w projekcie.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 0 , Language: English 
Document ID: 1 , Language: Spanish
Document ID: 2 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Rozpoznawanie jednostek

Utwórz nową funkcję o nazwie `ExtractEntities()` i Utwórz klienta przy użyciu metody `GetTextAnalyticsClient()` utworzonej wcześniej. Utwórz listę obiektów [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) zawierających dokumenty, które chcesz przeanalizować. Każdy obiekt będzie zawierać atrybut `id`, `language`i `text`. Atrybut `text` przechowuje tekst do przeanalizowania, `language` jest językiem dokumentu, a `id` może być dowolną wartością. 

Wywołaj jednostki klienta [()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) i uzyskaj wynik. Następnie wykonuje iterację w wynikach i drukuje identyfikator każdego dokumentu oraz wynik wyodrębnionych jednostek.

[!code-go[entity recognition sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=entityRecognition)]

Wywołaj `ExtractEntities()` w projekcie.

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

Utwórz nową funkcję o nazwie `ExtractKeyPhrases()` i Utwórz klienta przy użyciu metody `GetTextAnalyticsClient()` utworzonej wcześniej. Utwórz listę obiektów [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) zawierających dokumenty, które chcesz przeanalizować. Każdy obiekt będzie zawierać atrybut `id`, `language`i `text`. Atrybut `text` przechowuje tekst do przeanalizowania, `language` jest językiem dokumentu, a `id` może być dowolną wartością.

Wywołaj [frazy kluczowe klienta ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) i Pobierz wynik. Następnie wykonuje iterację w wynikach i drukuje identyfikator każdego dokumentu oraz wyodrębnia kluczowe frazy.

[!code-go[key phrase extraction sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=keyPhrases)]

Wywołaj `ExtractKeyPhrases()` w projekcie.

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
