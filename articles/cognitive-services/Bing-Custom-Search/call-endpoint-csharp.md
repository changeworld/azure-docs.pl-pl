---
title: Punkt końcowy wywołanie za pomocą języka C# — wyszukiwanie niestandardowe Bing - Microsoft kognitywnych usług
description: Ta opcja szybkiego startu pokazano, jak żądania wyniki wyszukiwania z wystąpienia niestandardowego wyszukiwania przy użyciu języka C# do wywołania punktu końcowego niestandardowe wyszukiwania usługi Bing.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: be4cc79d16b9a22124f16878b11ca04a916f98ae
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347856"
---
# <a name="call-bing-custom-search-endpoint-c"></a>Wywołanie wyszukiwania usługi Bing niestandardowe punktu końcowego (C#)

Ta opcja szybkiego startu pokazano, jak żądania wyniki wyszukiwania z wystąpienia niestandardowego wyszukiwania przy użyciu języka C# do wywołania punktu końcowego niestandardowe wyszukiwania usługi Bing. 

## <a name="prerequisites"></a>Wymagania wstępne

-  Wystąpienie wyszukiwania niestandardowego gotowe do użycia. Zobacz [Tworzenie pierwszego wystąpienia wyszukiwania usługi Bing niestandardowe](quick-start.md).
-  [.NET core](https://www.microsoft.com/net/download/core) zainstalowane.
- A [kognitywnych interfejsu API usług konta](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsy API wyszukiwania usługi Bing**. [Bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) jest wystarczająca dla tego przewodnika Szybki Start. Należy klucz dostępu podany przy wywołaniu metody aktywacji bezpłatną wersję próbną lub może używać klucza płatnej subskrypcji z pulpitu nawigacyjnego platformy Azure.  

  >[!NOTE]  
  >Istniejące wyszukiwania usługi Bing niestandardowe klientów z kluczem Podgląd zainicjowano obsługę administracyjną w tym miejscu lub przed 15 października 2017 będzie można używać ich kluczy do 30 listopada 2017 lub do momentu wyczerpać maksymalną liczbę dozwolone kwerendy. Później muszą one migracja do wersji ogólnie dostępna na platformie Azure. 
 
## <a name="run-the-code"></a>Uruchamianie kodu

Aby uruchomić ten przykład, wykonaj następujące kroki:

1. Utwórz folder dla kodu.
2. Z wiersza polecenia lub terminalu przejdź do folderu utworzonego.
3. Uruchom następujące polecenia:
    ```
    dotnet new console -o BingCustomSearch
    cd BingCustomSearch
    dotnet add package Newtonsoft.Json
    dotnet restore
   ```

4. Skopiuj następujący kod do pliku Program.cs.
5. Zastąp **YOUR SUBSKRYPCJI klucza** i **YOUR-niestandardowe-CONFIG-ID** z identyfikatorem klucza i konfiguracji.

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
6. Tworzenie aplikacji, za pomocą następującego polecenia. Zanotuj ścieżkę biblioteki dll odwołują się dane wyjściowe polecenia.
    <pre>
    dotnet build 
    </pre>
7. Uruchom aplikację za pomocą zastąpienia następujące polecenia **ŚCIEŻKA danych wyjściowych** ze ścieżką odwołuje się kroku kompilacji.
    <pre>    
    dotnet **PATH TO OUTPUT**
    </pre>

## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie środowiska hostowanej interfejsu użytkownika](./hosted-ui.md)
- [Umożliwia znaczników decoration wyróżnianie tekstu](./hit-highlighting.md)
- [Strona stron sieci Web](./page-webpages.md)
