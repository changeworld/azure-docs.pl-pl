---
title: 'Szybki start: Biblioteka klienta automatycznego przełączania Bing dla Go | Dokumenty firmy Microsoft'
description: Wprowadzenie do biblioteki klienta automatycznego fakturowania Bing dla go i uzyskać sugestie wyszukiwania na podstawie częściowych ciągów zapytań.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 59f704377695be0f3253bdd0d7a7b82b460c86d0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239005"
---
# <a name="quickstart-bing-autosuggest-client-library-for-go"></a>Szybki start: Biblioteka klienta automatycznego przełączania Bing dla go

Wprowadzenie do biblioteki klienta automatycznego zasysania umołki usługi Bing dla go. Wykonaj następujące kroki, aby zainstalować bibliotekę i wypróbować nasze przykłady podstawowych zadań. 

Użyj biblioteki klienta autosuggest Bing dla Go, aby uzyskać sugestie wyszukiwania na podstawie częściowych ciągów zapytań.

[Dokumentacja](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest) | [referencyjna Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [Przykładowy kod](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)
* Najnowsza wersja [programu Go](https://golang.org/dl/)

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-an-azure-resource"></a>Tworzenie zasobu platformy Azure 

Rozpocznij korzystanie z biblioteki klienta autosuggest Bing, tworząc zasób platformy Azure. Wybierz typ zasobu poniżej, który jest odpowiedni dla Ciebie:

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

### <a name="create-an-environment-variable"></a>Tworzenie zmiennej środowiskowej

>[!NOTE]
> Punkty końcowe dla zasobów niepodstawowych utworzonych po 1 lipca 2019 r. używają niestandardowego formatu poddomeny przedstawionego poniżej. Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [Niestandardowe nazwy poddomen dla usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Korzystając z klucza i punktu końcowego z utworzonego zasobu, utwórz dwie zmienne środowiskowe do uwierzytelniania:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`- Klucz zasobu do uwierzytelniania żądań.
* `AUTOSUGGEST_ENDPOINT`- Punkt końcowy zasobu do wysyłania żądań interfejsu API. Będzie to wyglądać tak: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Użyj instrukcji dla systemu operacyjnego.
<!-- replace the below endpoint and key examples -->
#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

Po dodaniu zmiennej środowiskowej uruchom ponownie okno konsoli.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.bashrc` z okna konsoli, aby zmiany zostały uwzględnione.

#### <a name="macos"></a>[Macos](#tab/unix)

Edytuj `.bash_profile`swój program i dodaj zmienną środowiskową:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source .bash_profile` z okna konsoli, aby zmiany zostały uwzględnione.
***

### <a name="create-a-new-go-project"></a>Tworzenie nowego projektu Go

W oknie konsoli (cmd, PowerShell, Terminal, Bash) utwórz nowy obszar roboczy dla projektu Go i przejdź do niego. Obszar roboczy będzie zawierał trzy foldery: 

* **src** - Ten katalog zawiera kod źródłowy i pakiety. Wszystkie pakiety `go get` zainstalowane za pomocą polecenia znajdą się tutaj.
* **pkg** — ten katalog zawiera skompilowane obiekty pakietu Go. Wszystkie te pliki `.a` mają rozszerzenie.
* **bin** — ten katalog zawiera binarne pliki wykonywalne, które są tworzone po uruchomieniu `go install`.

> [!TIP]
> Dowiedz się więcej o strukturze [obszaru roboczego Go](https://golang.org/doc/code.html#Workspaces). Ten przewodnik zawiera `$GOPATH` informacje `$GOROOT`dotyczące ustawień i .

Utwórzmy obszar roboczy `my-app` wywoływany i wymagane `src`podkatastrony dla , `pkg`i `bin`:

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-client-library-for-go"></a>Instalowanie biblioteki klienta dla go

Teraz zainstalujmy bibliotekę klienta dla Go: 

```bash
$ go get -u <library-location-or-url>
```

a jeśli używasz programu dep, w ramach repozytorium uruchom:

```bash
$ dep ensure -add <library-location-or-url>
```

### <a name="create-your-go-application"></a>Tworzenie aplikacji Go

Następnie utwórzmy plik o `src/sample-app.go`nazwie:

```bash
$ cd src
$ touch sample-app.go
```

Otwórz `sample-app.go` i dodaj nazwę pakietu i zaimportuj następujące biblioteki:

```Go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"
)
```

Tworzenie funkcji `main`o nazwie . Następnie utwórz zmienne środowiskowe dla klucza autosugesty Bing i punktu końcowego.

```go
func main() {
    // Add your Bing Autosuggest subscription key to your environment variables.
    if "" == os.Getenv("BING_AUTOSUGGEST_SUBSCRIPTION_KEY") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_SUBSCRIPTION_KEY.")
    }
    // Add your Bing Autosuggest endpoint to your environment variables.
    var subscription_key string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
    if "" == os.Getenv("BING_AUTOSUGGEST_ENDPOINT") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_ENDPOINT.")
    }
    var endpoint string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
}
```

## <a name="code-examples"></a>Przykłady kodu

Te przykłady kodu pokazują, jak wykonać podstawowe zadania przy użyciu biblioteki klienta automatycznego przełączania Bing dla go:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Wysyłanie żądania interfejsu API](#send-an-api-request)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

> [!NOTE] 
> Ten przewodnik Szybki start zakłada utworzenie [zmiennej środowiskowej](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza autosugesty Bing o nazwie `BING_AUTOSUGGEST_SUBSCRIPTION_KEY`i jednej dla punktu końcowego o nazwie `BING_AUTOSUGGEST_ENDPOINT`.

W `main()` funkcji wystąpienia klienta z punktu końcowego i klucza. 

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

## <a name="send-an-api-request"></a>Wysyłanie żądania interfejsu API

W tej samej metodzie użyj metody [AutoSuggestMethodAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) klienta, aby wysłać zapytanie do usługi Bing. Następnie iteruj nad [odpowiedzią Sugestie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) i wydrukuj pierwszą sugestię.

```Go
// This should return the query suggestion "xbox."
result, err := client.AutoSuggest (ctx, "xb", "", "", "", "", "", "", "", "", "", "", []autosuggest.ResponseFormat{"Json"})
if nil != err {
    log.Fatal(err)
}

groups := *result.SuggestionGroups
if len(groups) > 0 {
    group, _ := groups[0].AsSuggestionsSuggestionGroup()
    fmt.Printf ("First suggestion group: %s\n", (*group).Name)
    suggestions := *(*group).SearchSuggestions
    if len(suggestions) > 0 {
        fmt.Println("First suggestion:")
        fmt.Printf("Query: %s\n", *suggestions[0].Query)
        fmt.Printf("Display text: %s\n", *suggestions[0].DisplayText)
    } else {
        fmt.Println("No suggestions found in this group.")
    }
} else {
    fmt.Println("No suggestions found.")
}
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację Go `go run [arguments]` za pomocą polecenia z katalogu aplikacji.

```Go
go run sample-app.go
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Bing Autosuggest tutorial (Samouczek dotyczący automatycznego sugerowania Bing)](../tutorials/autosuggest.md)

## <a name="see-also"></a>Zobacz też

- [Czym jest funkcja automatycznego sugerowania Bing?](../get-suggested-search-terms.md)
- [Bing Autosuggest API v7 reference (Dokumentacja dotycząca automatycznego sugerowania Bing w wersji 7)](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
