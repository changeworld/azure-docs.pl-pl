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
ms.openlocfilehash: 87970e1c5e8487f9afca2acc680bdfeb610dc89f
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2018
ms.locfileid: "41988464"
---
# <a name="call-bing-custom-search-endpoint-c"></a>Wywołanie punktu końcowego usługi Bing Custom Search (C#)

Ten przewodnik Szybki Start pokazano, jak żądanie wyniki wyszukiwania z wystąpienia wyszukiwania niestandardowego przy użyciu języka C# do wywoływania punktu końcowego usługi Bing Custom Search. 

## <a name="prerequisites"></a>Wymagania wstępne

-  Wystąpienie wyszukiwania niestandardowego gotowych do użycia. Zobacz [Tworzenie pierwszego wystąpienia wyszukiwania niestandardowego Bing](quick-start.md).
-  [.Net Core](https://www.microsoft.com/net/download/core) zainstalowane.
- A [konta interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsy API wyszukiwania Bing**. [Bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) jest wystarczająca na potrzeby tego przewodnika Szybki Start. Wymagany jest klucz dostępu podany przy wywołaniu metody Aktywuj bezpłatną wersję próbną lub klucz płatnej subskrypcji może używać z pulpitu nawigacyjnego platformy Azure.  

  >[!NOTE]  
  >Istniejących klientów wyszukiwania niestandardowego Bing, którzy mają klucz w wersji zapoznawczej aprowizowane w lub przed 15 października 2017 r. będzie można korzystać ze swoich kluczy do 30 listopada 2017 r. lub do momentu ich wyczerpano maksymalną liczbę zapytań dozwolone. Później muszą oni migrować do ogólnie dostępnej wersji na platformie Azure. 
 
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

4. Skopiuj następujący kod do pliku Program.cs.
5. Zastąp **YOUR-SUBSCRIPTION-KEY** i **YOUR-CUSTOM-CONFIG-ID** za pomocą identyfikatora klucza i konfiguracji.

    ``` CSharp
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
6. Tworzenie aplikacji przy użyciu następującego polecenia. Zanotuj ścieżkę biblioteki dll, które odwołują się dane wyjściowe polecenia.

    <pre>
    dotnet build 
    </pre>
7. Uruchom aplikację za pomocą polecenia następujących, zastępując **ŚCIEŻKA danych wyjściowych** ze ścieżką odwołuje się krok kompilacji.

    <pre>    
    dotnet **PATH TO OUTPUT**
    </pre>

## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie środowiska obsługiwanego interfejsu użytkownika](./hosted-ui.md)
- [Korzystanie ze znaczników dekoracji, aby wyróżnić tekst](./hit-highlighting.md)
- [Strona stron sieci Web](./page-webpages.md)
