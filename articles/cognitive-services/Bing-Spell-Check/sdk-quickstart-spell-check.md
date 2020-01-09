---
title: 'Szybki Start: sprawdzanie pisowni za pomocą zestawu SDK sprawdzanie pisowni Bing dlaC#'
titleSuffix: Azure Cognitive Services
description: Rozpocznij korzystanie z interfejsu API REST sprawdzania pisowni Bing, aby sprawdzać pisownię i poprawność gramatyczną.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: b314944f1ae8847470f218ca23a11beed7731f67
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448408"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Szybki Start: sprawdzanie pisowni za pomocą zestawu SDK sprawdzanie pisowni Bing dlaC#

Ten przewodnik Szybki start umożliwia rozpoczęcie sprawdzania pisowni za pomocą zestawu SDK sprawdzania pisowni Bing dla języka C#. Mimo że funkcja sprawdzania pisowni Bing ma interfejs API REST zgodny z większością języków programowania, zestaw SDK umożliwia łatwe zintegrowanie tej usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck).

## <a name="application-dependencies"></a>Zależności aplikacji

* Dowolna wersja programu [Visual Studio 2017 lub nowszego](https://visualstudio.microsoft.com/downloads/).
* [Pakiet NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck) sprawdzania pisowni Bing

Aby dodać zestaw sprawdzanie pisowni Bing SDK do projektu, wybierz pozycję **Zarządzaj pakietami NuGet** z **Eksplorator rozwiązań** w programie Visual Studio. Dodaj pakiet `Microsoft.Azure.CognitiveServices.Language.SpellCheck`. Ten pakiet instaluje również następujące zależności:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowe rozwiązanie konsolowe dla języka C# w programie Visual Studio. Następnie dodaj poniższą instrukcję `using`.
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. Utwórz nową klasę. Następnie utwórz funkcję asynchroniczną o nazwie `SpellCheckCorrection()`, która pobiera klucz subskrypcji i wysyła żądanie sprawdzania pisowni.

3. Utwórz wystąpienie klienta przez utworzenie nowego obiektu `ApiKeyServiceClientCredentials`. 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>Wysyłanie żądania i odczytywanie odpowiedzi

1. W ramach funkcji utworzonej powyżej wykonaj następujące czynności. Wyślij żądanie sprawdzania pisowni za pomocą klienta. Do parametru `text` dodaj tekst, który ma być sprawdzany, i ustaw tryb `proof`.  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. Pobierz pierwszy wynik sprawdzania pisowni, jeśli istnieje. Wyświetl pierwszy zwrócony wyraz (token) z błędem pisowni, typ tokenu i liczbę sugestii.

    ```csharp
    if (firstspellCheckResult != null){
        var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. Pobierz pierwszą sugerowaną poprawkę, jeśli istnieje. Wydrukuj ocenę sugestii i sugerowany wyraz. 

    ```csharp
    var suggestions = firstspellCheckResult.Suggestions;

    if (suggestions?.Count > 0)
    {
        var firstSuggestion = suggestions.FirstOrDefault();
        Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
        Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
    }
    ```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Skompiluj i Uruchom projekt. Jeśli używasz programu Visual Studio, naciśnij klawisz **F5** , aby debugować plik.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](tutorials/spellcheck.md)

- [Czym jest interfejs API sprawdzania pisowni Bing?](overview.md)
- [Przewodnik C# referencyjny sprawdzanie pisowni Bing SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)