---
title: Niestandardowe wyszukiwania zestawu SDK C# Szybki Start | Dokumentacja firmy Microsoft
titleSuffix: Cognitive Services
description: Konfiguracja aplikacji konsoli niestandardowe wyszukiwania zestawu SDK C#.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 01/31/2018
ms.author: rosh
ms.openlocfilehash: 59b208b53bec974433c50c0e2304dc96bd9bd09e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346900"
---
# <a name="custom-search-sdk-c-quickstart"></a>Niestandardowe wyszukiwania zestawu SDK C# Szybki Start

Zestaw SDK wyszukiwania usługi Bing niestandardowe zawiera funkcje interfejsu API REST dla encji wyszukiwania i analizowania wyników.

## <a name="application-dependencies"></a>Zależności aplikacji

Aby skonfigurować aplikację konsoli przy użyciu zestawu SDK wyszukiwania usługi Bing niestandardowych, przejdź do `Manage NuGet Packages` opcji z Eksploratora rozwiązań w programie Visual Studio. Dodaj `Microsoft.Azure.CognitiveServices.Search.CustomSearch` pakietu.

Instalowanie [wyszukiwanie niestandardowe NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) pakiet instaluje również zależności, łącznie z następujących zestawów:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="entity-search-client"></a>Jednostka wyszukiwania klienta

Aby utworzyć wystąpienie klienta CustomSearchAPI, Dodaj dyrektyw using:
```
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

```

Wystąpienia klienta wyszukiwania niestandardowego: Zastąp `YOUR-CUSTOM-SEARCH-KEY` i `YOUR-CUSTOM-CONFIG-ID` identyfikator przypisany w konfiguracji punktu końcowego interfejsu API i klucz dostępu do [Moje wystąpień](https://www.customsearch.ai/).
```
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

```
Przy użyciu klienta do wyszukiwania tekstu zapytania:
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;

```
## <a name="parse-the-results"></a>Wyniki analizy

`SearchAsync` Metoda zwraca `WebData` obiekt, który zawiera `WebPages` Jeśli żadnego nie znaleziono dla zapytania. Ten kod znajduje pierwszy wynik i pobiera jego `Name` i `URL`.
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
## <a name="complete-console-application"></a>Aplikacja konsolowa ukończone

Poniższy kod wyszukuje w zapytaniu "Xbox" i drukowania `Name` i `URL` do pierwszego wyniku sieci web.
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

[Usługi kognitywnych przykłady zestawu .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
