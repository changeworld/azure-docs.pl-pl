---
title: 'Szybki start: Zestaw Custom Search SDK, C#'
titleSuffix: Azure Cognitive Services
description: Skonfiguruj aplikację konsoli zestawu Custom Search SDK w języku C#.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 09/06/2018
ms.author: scottwhi
ms.openlocfilehash: 5abf1027059bed9c685e0eb44f17ab41dfabf655
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816773"
---
# <a name="quickstart-using-the-bing-custom-search-sdk-with-c"></a>Szybki start: Korzystanie z zestawu Bing Custom Search SDK w języku C#

Zestaw SDK wyszukiwania niestandardowego Bing udostępnia łatwiejszy model programowania niż interfejs API REST wyszukiwania niestandardowego Bing. W tej sekcji przedstawiono proces tworzenia pierwszych wywołań usługi wyszukiwania niestandardowego przy użyciu zestawu SDK języka C#.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

- Gotowe do użycia wystąpienie usługi wyszukiwania niestandardowego. Zobacz [Tworzenie pierwszego wystąpienia usługi wyszukiwania niestandardowego Bing](quick-start.md).  
  
- Klucz subskrypcji. Klucz subskrypcji możesz uzyskać, aktywując [bezpłatną wersję próbną](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), lub użyć klucza płatnej subskrypcji z pulpitu nawigacyjnego platformy Azure (zobacz [Konto interfejsu Cognitive Services API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).  
  
- Zainstalowany program Visual Studio 2017. Jeśli jeszcze go nie masz, możesz pobrać **bezpłatny** program [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).  
  
- Zainstalowany pakiet [NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0). W Eksploratorze rozwiązań w programie Visual Studio kliknij prawym przyciskiem myszy swój projekt i wybierz polecenie `Manage NuGet Packages` z menu. Zainstaluj pakiet `Microsoft.Azure.CognitiveServices.Search.CustomSearch`.

Zainstalowanie pakietu NuGet Custom Search powoduje również zainstalowanie następujących zestawów:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json



## <a name="run-the-code"></a>Uruchamianie kodu

Aby uruchomić ten przykład, wykonaj następujące kroki:

1. Otwórz program Visual Studio 2017.
  
2. Kliknij menu **Plik**, kliknij pozycje **Nowy** i **Projekt**, a następnie kliknij szablon **Aplikacja konsoli w języku Visual C#**.
  
3. Nazwij rozwiązanie CustomSearchSolution i przejdź do folderu, w którym chcesz je umieścić.
  
4. Kliknij przycisk OK, aby utworzyć rozwiązanie.  
  
4. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt (ma taką samą nazwę jak rozwiązanie) i wybierz polecenie `Manage NuGet Packages`. Kliknij przycisk **Przeglądaj** w Menedżerze pakietów NuGet. Wpisz Microsoft.Azure.CognitiveServices.Search.CustomSearch w polu wyszukiwania i naciśnij klawisz Enter. Wybierz pakiet, a następnie kliknij przycisk Instaluj.  
  
4. Skopiuj poniższy kod do pliku Program.cs. Zastąp wartości **YOUR-SUBSCRIPTION-KEY** i **YOUR-CUSTOM-CONFIG-ID** odpowiednio kluczem subskrypcji i identyfikatorem konfiguracji.  
  
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
  
5. Skompiluj i uruchom rozwiązanie (z debugowaniem). 




## <a name="breaking-it-down"></a>Instrukcja krok po kroku

Po zainstalowaniu pakietu NuGet Custom Search dodaj dla niego dyrektywę using.

```csharp
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
```

Następnie utwórz wystąpienie klienta wyszukiwania niestandardowego, który służy do wykonywania żądań wyszukiwania. Zastąp wartość `YOUR-SUBSCRIPTION-KEY` swoim kluczem.

```csharp
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));
```

Teraz można wysyłać żądania wyszukiwania przy użyciu klient. Zastąp wartość `YOUR-CUSTOM-CONFIG-ID` identyfikatorem konfiguracji wystąpienia (identyfikator można znaleźć w [portalu usługi wyszukiwania niestandardowego](https://www.customsearch.ai/)). W tym przykładzie wyszukiwany jest termin Xbox. Należy zaktualizować go zgodnie z potrzebami.

```csharp
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
```

Metoda `SearchAsync` zwraca obiekt `WebData`. Użyj obiektu `WebData`, aby iterować po wszystkich znalezionych obiektach `WebPages`. Ten kod znajduje pierwszy wynik w postaci strony internetowej i wyświetla wartości elementów `Name` oraz `URL` strony internetowej.

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


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z przykładami dotyczącymi zestawów SDK. Przykłady dotyczące zestawów SDK zawierają plik ReadMe ze szczegółowymi informacjami na temat wymagań wstępnych oraz instalowania i uruchamiania przykładów.

* Rozpocznij pracę, korzystając z [przykładów dla platformy .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) 
    * [Pakiet NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)
    * Zobacz też [biblioteki platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingCustomSearch) zawierające definicje i zależności.
* Rozpocznij pracę, korzystając z [przykładów dla środowiska NodeJS](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples). 
    * Zobacz też [biblioteki środowiska NodeJS](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/customSearch) zawierające definicje i zależności.
* Rozpocznij pracę, korzystając z [przykładów dla języka Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Zobacz też [biblioteki języka Java](https://github.com/Azure/azure-sdk-for-java/tree/master/cognitiveservices/azure-customsearch) zawierające definicje i zależności.
* Rozpocznij pracę, korzystając z [przykładów dla języka Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Zobacz też [biblioteki języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-customsearch) zawierające definicje i zależności.

