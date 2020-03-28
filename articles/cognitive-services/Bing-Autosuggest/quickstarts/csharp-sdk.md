---
title: 'Szybki start: Biblioteka klienta automatycznego przełączania Bing dla platformy .NET | Dokumenty firmy Microsoft'
description: Wprowadzenie do biblioteki klienta automatycznego fakturowania Bing dla platformy .NET i uzyskanie sugestii wyszukiwania na podstawie częściowych ciągów zapytań.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 12/20/2019
ms.author: aahi
ms.openlocfilehash: 6bcd89422188d8c0064547c37b4d425f47dd6496
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75473927"
---
# <a name="quickstart-bing-autosuggest-client-library-for-net"></a>Szybki start: Biblioteka klienta automatycznego przełączania Bing dla platformy .NET

Wprowadzenie do biblioteki klienta automatycznego zasysania umoli usługi Bing dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Użyj biblioteki klienta autosuggest Bing dla platformy .NET, aby uzyskać sugestie wyszukiwania na podstawie częściowych ciągów zapytań.

[Dokumentacja](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingautosuggest?view=azure-dotnet) | [referencyjna Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingAutoSuggest) | [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.AutoSuggest/) | [Przykładowy kod](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/BingAutoSuggest/Program.cs)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* Bieżąca wersja programu [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-an-azure-resource"></a>Tworzenie zasobu platformy Azure

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
setx AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
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

### <a name="create-a-new-c-application"></a>Tworzenie nowej aplikacji języka C#

Utwórz nową aplikację .NET Core w preferowanym edytorze lub ide. 

W oknie konsoli (takim jak cmd, PowerShell `dotnet new` lub Bash) użyj polecenia, `bing-autosuggest-quickstart`aby utworzyć nową aplikację konsoli o nazwie . To polecenie tworzy prosty projekt "Hello World" C# z jednym plikiem źródłowym: *program.cs*. 

```console
dotnet new console -n bing-autosuggest-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można utworzyć za pomocą:

```console
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać żadnych ostrzeżeń ani błędów. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

W katalogu projektu otwórz plik *program.cs* w preferowanym edytorze lub IDE. Dodaj następujące `using` dyrektywy:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
```

W `Program` klasie utwórz zmienne dla punktu końcowego i klucza platformy Azure zasobu. Jeśli po uruchomieniu aplikacji utworzono zmienną środowiskową, należy zamknąć i ponownie otworzyć edytor, IDE lub powłokę uruchamianą ją, aby uzyskać dostęp do zmiennej.

```csharp
private const string key_var = "AUTOSUGGEST_SUBSCRIPTION_KEY";
private static readonly string subscription_key = Environment.GetEnvironmentVariable(key_var);

// Note you must use the same region as you used to get your subscription key.
private const string endpoint_var = "AUTOSUGGEST_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);
```

W `Main` metodzie aplikacji dodaj następujące wywołania metody, które zostaną zdefiniowane później.

```csharp
static void Main(string[] args)
{
    Task.WaitAll(RunQuickstart());
    Console.WriteLine("Press any key to exit.");
    Console.Read();
}
```

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

W katalogu aplikacji zainstaluj bibliotekę klienta autosuggest usługi Bing dla platformy .NET za pomocą następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Search.AutoSuggest --version 2.0.0
```

Jeśli używasz środowiska IDE programu Visual Studio, biblioteka klienta jest dostępna jako pakiet NuGet do pobrania.

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania za pomocą biblioteki klienta automatycznego przełączania NSDA dla platformy .NET:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Wyślij żądanie autosugestii](#send-an-autosuggest-request)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

> [!NOTE]
> Ten przewodnik Szybki start zakłada utworzenie [zmiennej środowiskowej](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza autosueszności Bing o nazwie `AUTOSUGGEST_SUBSCRIPTION_KEY`i jednej dla punktu końcowego o nazwie `AUTOSUGGEST_ENDPOINT`.


W nowej metodzie asynchronicznemu wystąpienia klienta z punktem końcowym i kluczem. Utwórz [obiekt ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.apikeyserviceclientcredentials?view=azure-dotnet) za pomocą klucza i użyj go z punktem końcowym, aby utworzyć [obiekt AutosuggestClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclient?view=azure-dotnet)

```csharp
async static Task RunQuickstart()
{
    // Generate the credentials and create the client.
    var credentials = new Microsoft.Azure.CognitiveServices.Search.AutoSuggest.ApiKeyServiceClientCredentials(subscription_key);
    var client = new AutoSuggestClient(credentials, new System.Net.Http.DelegatingHandler[] { })
    {
        Endpoint = endpoint
    };
}
```

## <a name="send-an-autosuggest-request"></a>Wyślij żądanie autosugestii

W tej samej metodzie użyj metody [AutoSuggestMethodAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) klienta, aby wysłać zapytanie do usługi Bing. Następnie iteruj nad [odpowiedzią Sugestie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) i wydrukuj pierwszą sugestię.

```csharp
var result = await client.AutoSuggestMethodAsync("xb");
var groups = result.SuggestionGroups;
if (groups.Count > 0) {
    var group = groups[0];
    Console.Write("First suggestion group: {0}\n", group.Name);
    var suggestions = group.SearchSuggestions;
    if (suggestions.Count > 0)
    {
        Console.WriteLine("First suggestion:");
        Console.WriteLine("Query: {0}", suggestions[0].Query);
        Console.WriteLine("Display text: {0}", suggestions[0].DisplayText);
    }
    else
    {
        Console.WriteLine("No suggestions found in this group.");
    }
}
else
{
    Console.WriteLine("No suggestions found.");
}
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację z katalogu aplikacji `dotnet run` za pomocą polecenia.

```dotnet
dotnet run
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
- [Odwołanie do dotnetu automatycznego przełączania bing](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingautosuggest?view=azure-dotnet)
