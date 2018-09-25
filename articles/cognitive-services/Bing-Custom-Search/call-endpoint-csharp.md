---
title: Wywoływanie punktu końcowego za pomocą języka C# — wyszukiwanie niestandardowe Bing — Microsoft Cognitive Services
description: Ten przewodnik Szybki Start pokazano, jak żądanie wyniki wyszukiwania z wystąpienia wyszukiwania niestandardowego przy użyciu języka C# do wywoływania punktu końcowego usługi Bing Custom Search.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: ed00b75fa956d0197d3672d84b097f99ec3c35ec
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956390"
---
# <a name="call-bing-custom-search-endpoint-c"></a>Wywołanie punktu końcowego usługi Bing Custom Search (C#)

Ten przewodnik Szybki Start pokazano, jak żądanie wyniki wyszukiwania z wystąpienia wyszukiwania niestandardowego za pomocą języka C# wywołać punkt końcowy wyszukiwania niestandardowego Bing. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

- Wystąpienie wyszukiwania niestandardowego gotowych do użycia. Zobacz [Tworzenie pierwszego wystąpienia wyszukiwania niestandardowego Bing](quick-start.md).
- [.Net Core](https://www.microsoft.com/net/download/core) zainstalowane.
- Klucz subskrypcji. Klucz subskrypcji możesz uzyskać po aktywowaniu usługi [bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), lub za pomocą klucza płatnej subskrypcji w pulpicie nawigacyjnym platformy Azure (zobacz [konta interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    


## <a name="run-the-code"></a>Uruchamianie kodu

Aby uruchomić ten przykład, wykonaj następujące kroki:

1. Utwórz folder dla kodu.  
  
2. W wierszu polecenia lub terminalu przejdź do folderu, który został utworzony.  
  
3. Uruchom następujące polecenia:
    ```
    dotnet new console -o BingCustomSearch
    cd BingCustomSearch
    dotnet add package Newtonsoft.Json
    dotnet restore
    ```
  
4. Skopiuj następujący kod do pliku Program.cs. Zastąp **YOUR-SUBSCRIPTION-KEY** i **YOUR-CUSTOM-CONFIG-ID** klucz subskrypcji i konfiguracji identyfikatora.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    
    namespace bing_custom_search_example_dotnet
    {
        class Program
        {
            static void Main(string[] args)
            {
                var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
                var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
                var searchTerm = args.Length > 0 ? args[0]: "microsoft";            
    
                var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                    "q=" + searchTerm +
                    "&customconfig=" + customConfigId;
    
                var client = new HttpClient();
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                var httpResponseMessage = client.GetAsync(url).Result;
                var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
                BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
                
                for(int i = 0; i < response.webPages.value.Length; i++)
                {                
                    var webPage = response.webPages.value[i];
                    
                    Console.WriteLine("name: " + webPage.name);
                    Console.WriteLine("url: " + webPage.url);                
                    Console.WriteLine("displayUrl: " + webPage.displayUrl);
                    Console.WriteLine("snippet: " + webPage.snippet);
                    Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
                    Console.WriteLine();
                }            
            }
        }
    
        public class BingCustomSearchResponse
        {        
            public string _type{ get; set; }            
            public WebPages webPages { get; set; }
        }
    
        public class WebPages
        {
            public string webSearchUrl { get; set; }
            public int totalEstimatedMatches { get; set; }
            public WebPage[] value { get; set; }        
        }
    
        public class WebPage
        {
            public string name { get; set; }
            public string url { get; set; }
            public string displayUrl { get; set; }
            public string snippet { get; set; }
            public DateTime dateLastCrawled { get; set; }
            public string cachedPageUrl { get; set; }
            public OpenGraphImage openGraphImage { get; set; }        
        }
        
        public class OpenGraphImage
        {
            public string contentUrl { get; set; }
            public int width { get; set; }
            public int height { get; set; }
        }
    }
    ```
6. Tworzenie aplikacji przy użyciu następującego polecenia. Zanotuj ścieżkę biblioteki DLL, które odwołują się dane wyjściowe polecenia.

    <pre>
    dotnet build 
    </pre>
    
7. Uruchom aplikację za pomocą polecenia następujących, zastępując **ŚCIEŻKA danych wyjściowych** z ścieżka biblioteki DLL, do którego odwołuje się krok 6.

    <pre>    
    dotnet **PATH TO OUTPUT**
    </pre>

## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie środowiska obsługiwanego interfejsu użytkownika](./hosted-ui.md)
- [Korzystanie ze znaczników dekoracji, aby wyróżnić tekst](./hit-highlighting.md)
- [Strona stron sieci Web](./page-webpages.md)
