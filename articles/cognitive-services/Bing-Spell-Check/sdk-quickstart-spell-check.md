---
title: 'Szybki start: Sprawdzanie pisowni za pomocą zestawu SDK sprawdzania pisowni Bing dla języka C#'
titleSuffix: Azure Cognitive Services
description: Rozpocznij korzystanie z interfejsu API REST sprawdzania pisowni Bing, aby sprawdzać pisownię i poprawność gramatyczną.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: d98d00275cbd89702e4bae0c93aa262805617e59
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500780"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Szybki start: Sprawdzanie pisowni za pomocą zestawu SDK sprawdzania pisowni Bing dla języka C#

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

3. Pobierz pierwszą sugerowaną poprawkę, jeśli istnieje. Wyświetl wskaźnik sugestii i sugerowany wyraz. 

    ```csharp
            var suggestions = firstspellCheckResult.Suggestions;

            if (suggestions?.Count > 0)
            {
                var firstSuggestion = suggestions.FirstOrDefault();
                Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
                Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
            }
   }

## Next steps

> [!div class="nextstepaction"]
> [Create a single page web-app](tutorials/spellcheck.md)

- [What is the Bing Spell Check API?](overview.md)
- [Bing Spell Check C# SDK reference guide](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)