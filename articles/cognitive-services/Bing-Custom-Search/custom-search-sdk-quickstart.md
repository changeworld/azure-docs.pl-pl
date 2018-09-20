---
title: Niestandardowe wyszukiwanie zestawu SDK języka C# Szybki Start | Dokumentacja firmy Microsoft
titleSuffix: Cognitive Services
description: Skonfiguruj aplikację konsolową niestandardowego wyszukiwania zestawu SDK C#.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 01/31/2018
ms.author: rosh
ms.openlocfilehash: 6b41dfbde0c2af776ee2c35220f731e40de334a0
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367852"
---
# <a name="custom-search-sdk-c-quickstart"></a>Zestaw SDK funkcji wyszukiwania niestandardowego w języku C# — Szybki start

Zestaw SDK wyszukiwanie niestandardowe Bing zawiera funkcje interfejsu API REST, wyszukiwanie jednostek i analizowanie wyników.

Kod źródłowy, w tym przykładzie jest dostępny z [Github](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).
## <a name="application-dependencies"></a>Zależności aplikacji

Aby skonfigurować aplikację konsoli przy użyciu zestawu SDK programu Bing Custom Search, przejdź do `Manage NuGet Packages` opcji z poziomu Eksploratora rozwiązań w programie Visual Studio. Dodaj pakiet `Microsoft.Azure.CognitiveServices.Search.CustomSearch`.

Instalowanie [NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) pakiet instaluje również zależności, w tym następujących zestawów:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="entity-search-client"></a>Jednostki wyszukiwania klienta

Aby utworzyć wystąpienia klienta CustomSearchAPI, dodawanie dyrektyw using:
```
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

```

Utwórz wystąpienie klienta wyszukiwania niestandardowego: Zastąp `YOUR-CUSTOM-SEARCH-KEY` i `YOUR-CUSTOM-CONFIG-ID` swoim kluczem dostępu i konfiguracji punktu końcowego interfejsu API, identyfikator przypisany na [Moje wystąpienia](https://www.customsearch.ai/).
```
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

```
Użyj klienta, aby wyszukać tekst zapytania:
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;

```
## <a name="parse-the-results"></a>Analizowanie wyników

`SearchAsync` Metoda zwraca `WebData` obiekt, który zawiera `WebPages` Jeśli jakaś zostanie znaleziona dla zapytania. Ten kod umożliwia znalezienie pierwszego wyniku i pobiera jego `Name` i `URL`.
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
 
Console.WriteLine("Searched for Query# \" Xbox \"");

 //WebPages
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results#{0}", webData.WebPages.Value.Count);
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

```
## <a name="complete-console-application"></a>Kompletna aplikacja konsolowa

Poniższy kod wyszukuje zapytania "Xbox" i wyświetla `Name` i `URL` dla pierwszego wyniku sieci web.
```
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

namespace CustomSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {

            var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

            try
            {
                // This will look up a single query (Xbox).
                var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
                Console.WriteLine("Searched for Query# \" Xbox \"");

                //WebPages
                if (webData?.WebPages?.Value?.Count > 0)
                {
                    // find the first web page
                    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                    if (firstWebPagesResult != null)
                    {
                        Console.WriteLine("Webpage Results#{0}", webData.WebPages.Value.Count);
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
            }
            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

    }
}


```

## <a name="next-steps"></a>Kolejne kroki

[Cognitive services .NET SDK samples (Przykłady zestawów SKD .NET usług Cognitive Services)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
