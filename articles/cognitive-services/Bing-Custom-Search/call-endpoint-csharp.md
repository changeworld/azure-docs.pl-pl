---
title: 'Szybki start: wywoływanie punktu końcowego wyszukiwania niestandardowego Bing przy użyciu języka C# | Microsoft Docs'
titlesuffix: Azure Cognitive Services
description: Użyj tego przewodnika Szybki start, aby rozpocząć żądanie wyników z wystąpienia wyszukiwania niestandardowego Bing w języku C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: maheshb
ms.openlocfilehash: a775c1c864a8a5513be546195da5c0891f8bb1f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61066168"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Szybki start: wywoływanie punktu końcowego wyszukiwania niestandardowego Bing przy użyciu języka C# 

Użyj tego przewodnika Szybki start, aby rozpocząć żądanie wyników z wystąpienia wyszukiwania niestandardowego Bing. Chociaż ta aplikacja jest napisana w języku C#, interfejs API wyszukiwania niestandardowego Bing jest usługą internetową RESTful zgodną z większością języków programowania. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs).

## <a name="prerequisites"></a>Wymagania wstępne

- Wystąpienie wyszukiwania niestandardowego Bing. Zobacz [Szybki start: Tworzenie pierwszego wystąpienia usługi wyszukiwania niestandardowego Bing](quick-start.md), aby uzyskać więcej informacji.
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- Dowolna wersja programu [Visual Studio 2017](https://www.visualstudio.com/downloads/)
- Jeśli używasz systemu Linux/MacOS, możesz uruchomić tę aplikację przy użyciu środowiska [Mono](https://www.mono-project.com/).
- Zainstalowany pakiet [NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0). 
    - W Eksploratorze rozwiązań w programie Visual Studio kliknij prawym przyciskiem myszy swój projekt i wybierz polecenie `Manage NuGet Packages` z menu. Zainstaluj pakiet `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Zainstalowanie pakietu NuGet Custom Search powoduje również zainstalowanie następujących zestawów:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nową aplikację konsoli języka C# w programie Visual Studio. Następnie dodaj do projektu poniższe pakiety.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Utwórz poniższe klasy w celu przechowywania wyników wyszukiwania zwróconych przez interfejs API wyszukiwania niestandardowego Bing.

    ```csharp
    public class BingCustomSearchResponse {        
        public string _type{ get; set; }            
        public WebPages webPages { get; set; }
    }

    public class WebPages {
        public string webSearchUrl { get; set; }
        public int totalEstimatedMatches { get; set; }
        public WebPage[] value { get; set; }        
    }

    public class WebPage {
        public string name { get; set; }
        public string url { get; set; }
        public string displayUrl { get; set; }
        public string snippet { get; set; }
        public DateTime dateLastCrawled { get; set; }
        public string cachedPageUrl { get; set; }
    }
    ```

3. W metodzie głównej projektu utwórz zmienne dla klucza subskrypcji interfejsu API wyszukiwania niestandardowego Bing, identyfikatora konfiguracji niestandardowej wystąpienia wyszukiwania oraz terminu wyszukiwania.

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Skonstruuj adres URL żądania, dodając termin wyszukiwania do parametru zapytania `q=` oraz identyfikator konfiguracji niestandardowej wystąpienia wyszukiwania do parametru `customconfig=`. Oddziel parametry za pomocą znaku `&`. 

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Wysyłanie i odbieranie żądania wyszukiwania 

1. Utwórz żądanie klienta i dodaj klucz subskrypcji do nagłówka `Ocp-Apim-Subscription-Key`.

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. Wykonaj żądanie wyszukiwania i pobierz odpowiedź jako obiekt JSON.

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
   ## <a name="process-and-view-the-results"></a>Przetwarzanie i wyświetlanie wyników

1. Przejrzyj obiekt odpowiedzi, aby wyświetlić informacje na temat każdego wyniku wyszukiwania, w tym jego nazwę, adres URL i datę ostatniego przeszukiwania strony internetowej.

    ```csharp
    for(int i = 0; i < response.webPages.value.Length; i++) {                
        var webPage = response.webPages.value[i];
        
        Console.WriteLine("name: " + webPage.name);
        Console.WriteLine("url: " + webPage.url);                
        Console.WriteLine("displayUrl: " + webPage.displayUrl);
        Console.WriteLine("snippet: " + webPage.snippet);
        Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
        Console.WriteLine();
    }
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej z funkcją wyszukiwania niestandardowego](./tutorials/custom-search-web-page.md)
