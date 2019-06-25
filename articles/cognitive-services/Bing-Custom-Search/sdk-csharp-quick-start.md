---
title: 'Szybki start: wywoływanie punktu końcowego wyszukiwania niestandardowego Bing przy użyciu zestawu SDK w języku C# | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Korzystanie z usługi Bing niestandardowe wyszukiwania zestawu SDK dla C# wyszukiwanie wystąpienia wyszukiwania niestandardowego.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: scottwhi
ms.openlocfilehash: 0381df439d0c0904e8741bb1f31b179566c72ec5
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206172"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-c-sdk"></a>Szybki start: wywoływanie punktu końcowego wyszukiwania niestandardowego Bing przy użyciu zestawu SDK w języku C# 

Użyj tego przewodnika Szybki start, aby rozpocząć żądanie wyników z wystąpienia wyszukiwania niestandardowego Bing przy użyciu zestawu SDK w języku C#. Chociaż wyszukiwanie niestandardowe Bing ma interfejs API REST zgodny z większością języków programowania, zestaw SDK wyszukiwania niestandardowego Bing umożliwia łatwe integrowanie usługi z Twoimi aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).

## <a name="prerequisites"></a>Wymagania wstępne

- Wystąpienie wyszukiwania niestandardowego Bing. Zobacz [Szybki start: Tworzenie pierwszego wystąpienia usługi wyszukiwania niestandardowego Bing](quick-start.md), aby uzyskać więcej informacji.
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- Dowolnej wersji programu [programu Visual Studio 2017 r. lub nowszej](https://www.visualstudio.com/downloads/)
- Jeśli używasz systemu Linux/MacOS, możesz uruchomić tę aplikację przy użyciu środowiska [Mono](https://www.mono-project.com/).
- [Usługa Bing Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) pakietu NuGet. 
    - Z **Eksploratora rozwiązań** w programie Visual Studio, kliknij prawym przyciskiem myszy projekt i wybierz **Zarządzaj pakietami NuGet** z menu. Zainstaluj pakiet `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Zainstalowanie pakietu NuGet Custom Search powoduje również zainstalowanie następujących zestawów:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-news-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nową aplikację konsoli języka C# w programie Visual Studio. Następnie dodaj do projektu poniższe pakiety.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. W metodzie głównej aplikacji utwórz wystąpienie klienta wyszukiwania przy użyciu klucza interfejsu API.

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>Wysyłanie żądania wyszukiwania i odbieranie odpowiedzi
    
1. Wyślij zapytanie wyszukiwania przy użyciu metody `SearchAsync()` klienta i zapisz odpowiedź. Zastąp wartość `YOUR-CUSTOM-CONFIG-ID` identyfikatorem konfiguracji wystąpienia (identyfikator można znaleźć w [portalu usługi wyszukiwania niestandardowego Bing](https://www.customsearch.ai/)). W tym przykładzie wyszukiwany jest termin „Xbox”.

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. Metoda `SearchAsync()` zwraca obiekt `WebData`. Użyj obiektu, aby iterować po wszystkich znalezionych obiektach klienta `WebPages`. Ten kod znajduje pierwszy wynik w postaci strony internetowej i wyświetla wartości elementów `Name` oraz `URL` strony internetowej.

    ```csharp
    if (webData?.WebPages?.Value?.Count > 0)
    {
        // find the first web page
        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

        if (firstWebPagesResult != null)
        {
            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
        }
        else
        {
            Console.WriteLine("Couldn't find web results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any Web data..");
    }
    ```csharp

## Next steps

> [!div class="nextstepaction"]
> [Build a Custom Search web app](./tutorials/custom-search-web-page.md)
