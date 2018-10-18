---
title: 'Szybki start: wywoływanie punktu końcowego za pomocą języka C# — wyszukiwanie niestandardowe Bing'
titlesuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start pokazano, jak wysłać żądanie wyników wyszukiwania z poziomu wystąpienia usługi wyszukiwania niestandardowego, wywołując punkt końcowy wyszukiwania niestandardowego Bing za pomocą języka C#.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 1c3b1031c2d08b1f346216b54d351c99f01db933
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167311"
---
# <a name="quickstart-call-bing-custom-search-endpoint-c"></a>Szybki start: wywoływanie punktu końcowego wyszukiwania niestandardowego Bing (C#)

W tym przewodniku Szybki start pokazano, jak wysłać żądanie wyników wyszukiwania z poziomu wystąpienia usługi wyszukiwania niestandardowego, wywołując punkt końcowy wyszukiwania niestandardowego Bing za pomocą języka C#. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

- Gotowe do użycia wystąpienie usługi wyszukiwania niestandardowego. Zobacz [Tworzenie pierwszego wystąpienia usługi wyszukiwania niestandardowego Bing](quick-start.md).
- Zainstalowane środowisko [.Net Core](https://www.microsoft.com/net/download/core).
- Klucz subskrypcji. Klucz subskrypcji możesz uzyskać, aktywując [bezpłatną wersję próbną](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), lub użyć klucza płatnej subskrypcji z pulpitu nawigacyjnego platformy Azure (zobacz [Konto interfejsu Cognitive Services API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    


## <a name="run-the-code"></a>Uruchamianie kodu

Aby uruchomić ten przykład, wykonaj następujące kroki:

1. Utwórz folder do przechowywania kodu.  
  
2. W wierszu polecenia lub terminalu przejdź do właśnie utworzonego folderu.  
  
3. Uruchom następujące polecenia:
    ```
    dotnet new console -o BingCustomSearch
    cd BingCustomSearch
    dotnet add package Newtonsoft.Json
    dotnet restore
    ```
  
4. Skopiuj następujący kod do pliku Program.cs. Zastąp wartości **YOUR-SUBSCRIPTION-KEY** i **YOUR-CUSTOM-CONFIG-ID** odpowiednio kluczem subskrypcji i identyfikatorem konfiguracji.

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
6. Skompiluj aplikację przy użyciu następującego polecenia. Zanotuj ścieżkę biblioteki DLL, do której odwołują się dane wyjściowe polecenia.

    <pre>
    dotnet build 
    </pre>
    
7. Uruchom aplikację za pomocą następującego polecenia, zastępując wartość **PATH TO OUTPUT** ścieżką biblioteki DLL z kroku 6.

    <pre>    
    dotnet **PATH TO OUTPUT**
    </pre>

## <a name="next-steps"></a>Następne kroki
- [Konfigurowanie środowiska hostowanego interfejsu użytkownika](./hosted-ui.md)
- [Wyróżnianie tekstu za pomocą znaczników dekoracji](./hit-highlighting.md)
- [Dzielenie na strony wyników wyszukiwania stron internetowych](./page-webpages.md)
