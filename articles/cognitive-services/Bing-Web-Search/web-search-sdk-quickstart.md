---
title: 'Szybki Start: Użyj wyszukiwania w Internecie Bing zestawu SDK dla języka C#'
description: Ustawienia wyszukiwania w Internecie aplikację konsolową zestawu SDK C#.
titleSuffix: Azure cognitive services Web search SDK C# quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 08/16/2018
ms.author: v-gedod, erhopf
ms.openlocfilehash: ef54487a1df7303fa92a78e4f3219f40f558da2b
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887323"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-c"></a>Szybki Start: Użyj wyszukiwania w Internecie Bing zestawu SDK dla języka C#

Zestaw SDK wyszukiwania w sieci Web Bing zawiera funkcje interfejsu API REST dla żądań sieci web i wyniki analizy.

[Źródła kodu dla języka C# Bing wyszukiwania zestawu SDK sieci Web przykładów](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/BingSearchv7/BingWebSearch/WebSearchSamples.cs) jest dostępna w witrynie GitHub.

## <a name="application-dependencies"></a>Zależności aplikacji

Aby skonfigurować aplikację konsoli przy użyciu zestawu SDK wyszukiwania w sieci Web Bing, przejdź do `Manage NuGet Packages` opcji z poziomu Eksploratora rozwiązań w programie Visual Studio.  Dodaj `Microsoft.Azure.CognitiveServices.Search.WebSearch` pakietu.

Instalowanie [pakietu NuGet zestawu SDK z wyszukiwania w sieci Web](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0) instaluje również zależności, w tym:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="web-search-client"></a>Klient wyszukiwania w sieci Web
Aby utworzyć wystąpienie `WebSearchAPI` klienta, dodawanie dyrektyw using:
```
using Microsoft.Azure.CognitiveServices.Search.WebSearch;
using Microsoft.Azure.CognitiveServices.Search.WebSearch.Models;

```
Następnie utwórz wystąpienie klienta:
```
var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));


```
Użyj klienta, aby wyszukać tekst zapytania:
```
// Search for "Yosemite National Park"
var webData = client.Web.Search(query: "Yosemite National Park");
Console.WriteLine("Searched for Query# \" Yosemite National Park \"");

```
Analizowanie stron sieci web, w wynikach poprzednie zapytanie:
```
//WebPages
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results #{0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Couldn't find web results!)";
    }
}
else
{
    Console.WriteLine("Didn't see any Web data..");
}

```
## <a name="complete-console-application"></a>Aplikacja konsolowa ukończone

Następująca aplikacja konsoli wykonuje zapytanie uprzednio zdefiniowany i analizuje stron sieci web, obrazów, wiadomości i wideo zawarte w wynikach:
```
using System;
using System.Collections.Generic;
using Microsoft.Azure.CognitiveServices.Search.WebSearch;
using Microsoft.Azure.CognitiveServices.Search.WebSearch.Models;
using System.Linq;

namespace WebSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {
            var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));

            WebResults(client);
            // Include the following calls to use queries defined under following headings.
            //WebResultsWithCountAndOffset(client);  
            //WebSearchWithResponseFilter(client);
            //WebSearchWithAnswerCountPromoteAndSafeSearch(client);

            Console.WriteLine("Any key to exit... ");
            Console.ReadKey();
        }

        public static void WebResults(WebSearchAPI client)
        {
            try
            {
                // Search for (Yosemite National Park"), print the number of results and print out name and url of first result.
                var webData = client.Web.Search(query: "Yosemite National Park");
                Console.WriteLine("Searched for Query# \" Yosemite National Park \"");

                //WebPages
                if (webData?.WebPages?.Value?.Count > 0)
                {
                    // find the first web page
                    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                    if (firstWebPagesResult != null)
                    {
                        Console.WriteLine("Webpage Results #{0}", webData.WebPages.Value.Count);
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

                //Images
                if (webData?.Images?.Value?.Count > 0)
                {
                    // find the first image result
                    var firstImageResult = webData.Images.Value.FirstOrDefault();

                    if (firstImageResult != null)
                    {
                        Console.WriteLine("Image Results #{0}", webData.Images.Value.Count);
                        Console.WriteLine("First Image result name: {0} ", firstImageResult.Name);
                        Console.WriteLine("First Image result URL: {0} ", firstImageResult.ContentUrl);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find first image results!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any image data..");
                }

                //News
                if (webData?.News?.Value?.Count > 0)
                {
                    // find the first news result
                    var firstNewsResult = webData.News.Value.FirstOrDefault();

                    if (firstNewsResult != null)
                    {
                        Console.WriteLine("\r\nNews Results #{0}", webData.News.Value.Count);
                        Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
                        Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find any News results!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see first news data..");
                }

                //Videos
                if (webData?.Videos?.Value?.Count > 0)
                {
                    // find the first video result
                    var firstVideoResult = webData.Videos.Value.FirstOrDefault();

                    if (firstVideoResult != null)
                    {
                        Console.WriteLine("\r\nVideo Results #{0}", webData.Videos.Value.Count);
                        Console.WriteLine("First Video result name: {0} ", firstVideoResult.Name);
                        Console.WriteLine("First Video result URL: {0} ", firstVideoResult.ContentUrl);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find first video results!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any video data..");
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

        }
    }
}

```

Przykłady wyszukiwania Bing pokazują różne funkcje zestawu SDK.  Dodaj następujące funkcje do uprzednio zdefiniowany `WebSrchSDK` klasy.

## <a name="count-and-offset-parameters"></a>Przesunięcie i liczba parametrów

Poniższy kod wyszukuje "Najlepsze restauracje w Seattle", sprawdza liczbę wyników i wyświetla nazwę i adres URL pierwszego wyniku.

```
       public static void WebResultsWithCountAndOffset(WebSearchAPI client)
        {
            try
            {
                var webData = client.Web.SearchAsync(query: "Best restaurants in Seattle", offset: 10, count: 20).Result;
                Console.WriteLine("\r\nSearched for Query# \" Best restaurants in Seattle \"");

                if (webData?.WebPages?.Value?.Count > 0)
                {
                    // find the first web page
                    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                    if (firstWebPagesResult != null)
                    {
                        Console.WriteLine("Web Results #{0}", webData.WebPages.Value.Count);
                        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find first web result!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any Web data..");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```
## <a name="response-filter"></a>Filtr odpowiedzi

Następujące zapytanie wyszukuje wystąpienia terminu "Microsoft", przy użyciu filtru odpowiedzi równa `news` i następnie drukuje szczegóły wyników.
```
        public static void WebSearchWithResponseFilter(WebSearchAPI client)
        {
            //var client = new WebSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                IList<string> responseFilterstrings = new List<string>() { "news" };
                var webData = client.Web.SearchAsync(query: "Microsoft", responseFilter: responseFilterstrings).Result;
                Console.WriteLine("\r\nSearched for Query# \" Microsoft \" with response filters \"news\"");

                //News
                if (webData?.News?.Value?.Count > 0)
                {
                    // find the first news result
                    var firstNewsResult = webData.News.Value.FirstOrDefault();

                    if (firstNewsResult != null)
                    {
                        Console.WriteLine("News Results #{0}", webData.News.Value.Count);
                        Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
                        Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find first News results!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any News data..");
                }

            }
            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```
## <a name="query-parameters---count-promotion-safe-search"></a>Parametry - count, promocji, bezpieczne wyszukiwanie zapytania

To zapytanie "Matki Gaga", wyszukiwania, za pomocą `answerCount` i `promote` parametry następnie drukuje szczegóły wyników.

```
        public static void WebSearchWithAnswerCountPromoteAndSafeSearch(WebSearchAPI client)
        {
            //var client = new WebSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                IList<string> promoteAnswertypeStrings = new List<string>() { "videos" };
                var webData = client.Web.SearchAsync(query: "Lady Gaga", answerCount: 2, promote: promoteAnswertypeStrings, safeSearch: SafeSearch.Strict).Result;
                Console.WriteLine("\r\nSearched for Query# \" Lady Gaga \"");

                if (webData?.Videos?.Value?.Count > 0)
                {
                    var firstVideosResult = webData.Videos.Value.FirstOrDefault();

                    if (firstVideosResult != null)
                    {
                        Console.WriteLine("Video Results #{0}", webData.Videos.Value.Count);
                        Console.WriteLine("First Video result name: {0} ", firstVideosResult.Name);
                        Console.WriteLine("First Video result URL: {0} ", firstVideosResult.ContentUrl);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find videos results!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any data..");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }
    }
```

## <a name="next-steps"></a>Kolejne kroki

[Przykłady zestawu SDK platformy .NET usług cognitive services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7).
