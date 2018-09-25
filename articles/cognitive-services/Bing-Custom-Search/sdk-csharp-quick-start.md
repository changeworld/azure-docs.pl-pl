---
title: Niestandardowe wyszukiwanie zestawu SDK języka C# Szybki Start | Dokumentacja firmy Microsoft
titleSuffix: Cognitive Services
description: Skonfiguruj aplikację konsolową niestandardowego wyszukiwania zestawu SDK C#.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/06/2018
ms.author: scottwhi
ms.openlocfilehash: 6c9917e3a63515f36b386e444edcc53de07799fc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949931"
---
# <a name="c-sdk-quickstart"></a>Przewodnik Szybki Start zestawu SDK języka C#

Zestaw SDK wyszukiwania niestandardowego Bing udostępnia model programowania łatwiej niż interfejsu API REST usługi Bing Custom Search. Ta sekcja przeprowadzi Cię przez wywołań Twojego pierwszego wyszukiwania niestandardowego za pomocą zestawu SDK języka C#.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

- Wystąpienie wyszukiwania niestandardowego gotowych do użycia. Zobacz [Tworzenie pierwszego wystąpienia wyszukiwania niestandardowego Bing](quick-start.md).  
  
- Klucz subskrypcji. Klucz subskrypcji możesz uzyskać po aktywowaniu usługi [bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), lub za pomocą klucza płatnej subskrypcji w pulpicie nawigacyjnym platformy Azure (zobacz [konta interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).  
  
- Zainstalowanego programu Visual Studio 2017. Jeśli nie masz jeszcze ją, możesz pobrać **bezpłatne** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).  
  
- [NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) zainstalowany pakiet. Z poziomu Eksploratora rozwiązań w programie Visual Studio kliknij prawym przyciskiem myszy projekt i wybierz `Manage NuGet Packages` z menu. Zainstaluj pakiet `Microsoft.Azure.CognitiveServices.Search.CustomSearch`.

Instalowanie pakietu NuGet Custom Search instaluje następujące zestawy:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json



## <a name="run-the-code"></a>Uruchamianie kodu

Aby uruchomić ten przykład, wykonaj następujące kroki:

1. Otwórz program Visual Studio 2017.
  
2. Kliknij przycisk **pliku** menu, kliknij przycisk **New**, **projektu**, a następnie **Visual C# Console Application** szablonu.
  
3. Nadaj nazwę rozwiązania CustomSearchSolution i przejdź do folderu, do jakich go umieszczono.
  
4. Kliknij przycisk OK, aby utworzyć rozwiązanie.  
  
4. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt (ma taką samą nazwę jak rozwiązanie), a następnie wybierz pozycję `Manage NuGet Packages`. Kliknij przycisk **Przeglądaj** w Menedżerze pakietów NuGet. Wprowadź Microsoft.Azure.CognitiveServices.Search.CustomSearch w polu wyszukiwania, a następnie naciśnij klawisz enter. Wybierz pakiet, a następnie kliknij przycisk Instaluj.  
  
4. Skopiuj następujący kod do pliku Program.cs. Zastąp **YOUR-SUBSCRIPTION-KEY** i **YOUR-CUSTOM-CONFIG-ID** klucz subskrypcji i konfiguracji identyfikatora.  
  
    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

    namespace CustomSrchSDK
    {
        class Program
        {
            static void Main(string[] args)
            {

                var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));

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
                }
                catch (Exception ex)
                {
                    Console.WriteLine("Encountered exception. " + ex.Message);
                }

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

        }
    }
    ```  
  
5. Kompilowanie i uruchamianie rozwiązania (debugowanie). 




## <a name="breaking-it-down"></a>Podzielenie go

Po zainstalowaniu pakietu NuGet Custom Search, musisz dodać, przy użyciu dyrektywy dla niego.

```csharp
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
```

Następnie utwórz wystąpienie klienta wyszukiwania niestandardowego, który służy do przygotowywania realizowania żądań wyszukiwania. Koniecznie Zastąp `YOUR-SUBSCRIPTION-KEY` kluczem.

```csharp
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));
```

Teraz można użyć klienta do wysyłania żądania wyszukiwania. Pamiętaj zastąpić swoje `YOUR-CUSTOM-CONFIG-ID` przy użyciu Identyfikatora konfiguracji wystąpienia usługi (można znaleźć Identyfikatora w [portalu wyszukiwania niestandardowego](https://www.customsearch.ai/)). W tym przykładzie wyszukuje dla konsoli Xbox. Aktualizacja zgodnie z potrzebami.

```csharp
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
```

`SearchAsync` Metoda zwraca `WebData` obiektu. Użyj `WebData` do iteracji przez dowolny `WebPages` , znaleziono. Ten kod umożliwia znalezienie pierwszego wyniku strony sieci Web i drukuje witryny sieci Web `Name` i `URL`.

```csharp
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


## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z przykładowych zestawach SDK. Każdy przykład zawiera plik ReadMe ze szczegółowymi informacjami o wymaganiach wstępnych oraz instalowanie/uruchamiania przykładów.

* Rozpoczynanie pracy z usługą [przykłady dla platformy .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) 
    * [Pakiet NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)
    * Zobacz też [bibliotek .NET](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingCustomSearch) dla definicji i zależności.
* Rozpoczynanie pracy z usługą [przykłady NodeJS](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Zobacz też [bibliotek NodeJS](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/customSearch) dla definicji i zależności.
* Rozpoczynanie pracy z usługą [przykładów w języku Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Zobacz też [biblioteki Java](https://github.com/Azure/azure-sdk-for-java/tree/master/cognitiveservices/azure-customsearch) dla definicji i zależności.
* Rozpoczynanie pracy z usługą [przykłady w języku Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Zobacz też [biblioteki języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-customsearch) dla definicji i zależności.

