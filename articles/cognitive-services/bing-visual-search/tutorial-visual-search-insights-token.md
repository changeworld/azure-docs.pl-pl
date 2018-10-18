---
title: 'Samouczek: ImageInsightsToken — wyszukiwanie wizualne Bing'
titlesuffix: Azure Cognitive Services
description: Opis sposobu użycia zestawu Bing Visual Search SDK do uzyskiwania adresów URL obrazów określonych za pomocą tokenu ImageInsightsToken.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: 06d6bc8e53276b5542210c2843d7221d6fd79c09
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386438"
---
# <a name="tutorial-bing-visual-search-sdk-imageinsightstoken-and-results"></a>Samouczek: zestaw Bing Visual Search SDK — token ImageInsightsToken i wyniki
Zestaw Visual Search SDK oferuje opcję wyszukiwania obrazów w trybie online przy użyciu poprzedniego wyszukiwania, które zwróciło token `ImageInsightsToken`.  W tym przykładzie token `ImageInsightsToken` jest używany i używany w kolejnym wyszukiwaniu.  Kod wysyła token `ImageInsightsToken` do usługi Bing i zwraca wyniki, które obejmują adresy URL wyszukiwania Bing i adresy URL podobnych obrazów znalezione w trybie online.

## <a name="prerequisites"></a>Wymagania wstępne
Program Visual Studio 2017. W razie potrzeby możesz pobrać bezpłatną wersję dla społeczności z tego miejsca: https://www.visualstudio.com/vs/community/.
Klucz interfejsu API usług Cognitive Services jest wymagany do uwierzytelniania wywołań zestawu SDK. Utwórz konto, aby otrzymać klucz bezpłatnej wersji próbnej. Klucz wersji próbnej obowiązuje przez siedem dni i oferuje jedno wywołanie na sekundę. W przypadku scenariuszy produkcyjnych kup klucz dostępu. Zobacz również informacje o cenach.
Możliwość uruchamiania zestawu .NET Core SDK i aplikacji platformy .NET Core 1.1. Rozwiązanie CORE, platformę i środowisko uruchomieniowe można pobrać z następującego miejsca: https://www.microsoft.com/net/download/.

## <a name="application-dependencies"></a>Zależności aplikacji
Aby skonfigurować aplikację konsoli przy użyciu zestawu Bing Web Search SDK, przejdź do opcji Zarządzaj pakietami NuGet w Eksploratorze rozwiązań programu Visual Studio. Dodaj:
* Microsoft.Azure.CognitiveServices.Search.VisualSearch
* pakiety Microsoft.Azure.CognitiveServices.Search.ImageSearchpackage.

Zainstalowanie pakietu NuGet Web Search SDK spowoduje również zainstalowanie zależności, w tym:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="get-the-imageinsightstoken-from-image-search"></a>Pobieranie tokenu ImageInsightsToken z wyszukiwania obrazów

W tym przykładzie użyto tokenu `ImageInsightsToken` uzyskanego przy użyciu poniższej metody.  Aby uzyskać więcej informacji na temat tego wywołania, zobacz [Image Search SDK C# quickstart (Szybki start: zestaw Image Search SDK, C#)](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

Kod wyszukuje wyniki zapytania dla ciągu „Canadian Rockies” i pobiera token ImageInsightsToken. Wyświetla on pierwszy token szczegółowych informacji, adres URL miniatury i adres URL zawartości obrazu.  Metoda zwraca token `ImageInsightsToken` do użycia w kolejnych żądaniach wyszukiwania wizualnego.

```
        private static String ImageResults(String subKey)
        {
            String insightTok = "None";
            try
            {
                var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
                var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
                Console.WriteLine("Search images for query \"canadian rockies\"");

                if (imageResults == null)
                {
                    Console.WriteLine("No image result data.");
                }
                else
                {
                    // Image results
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        insightTok = firstImageResult.ImageInsightsToken;
                        Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");  
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
                    }
                    else
                    {
                        insightTok = "None found";
                        Console.WriteLine("Couldn't find image results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }

            return insightTok;
        }
```

## <a name="specify-the-imageinsightstoken-for-visual-search-request"></a>Określanie tokenu ImageInsightsToken na potrzeby żądania wyszukiwania wizualnego

W tym przykładzie jest używany token szczegółowych informacji zwrócony przez poprzednią metodę. Poniższy kod tworzy obiekt `ImageInfo` na podstawie tokenu `ImageInsightsToken` i ładuje obiekt ImageInfo do elementu `VisualSearchRequest`. Określ token `ImageInsightsToken` w elemencie `ImageInfo` na potrzeby żądania `VisualSearchRequest`

```
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-visual-search-to-find-images-from-an-imageinsightstoken"></a>Wyszukiwanie obrazów na podstawie tokenu ImageInsightsToken przy użyciu wyszukiwania wizualnego

Element `VisualSearchRequest` zawiera informacje o obrazie do wyszukania w obiekcie `ImageInfo`.  Metoda `VisualSearchMethodAsync` pobiera wyniki.
```
// An image binary is not necessary here, as the image is specified by insights token.
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
Console.WriteLine("\r\nVisual search request with knowledgeRequest");

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Pobieranie danych adresu URL z elementu ImageModuleAction
Wyniki wyszukiwania wizualnego to obiekty `ImageTag`.  Każdy tag zawiera listę obiektów `ImageAction`.  Każdy obiekt `ImageAction` zawiera pole `Data`, które jest listą wartości zależących od typu akcji:

Różne typy można uzyskać za pomocą następującego kodu:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
Kompletna aplikacja zwraca:

* ActionType: MoreSizes -> WebSearchUrl:
* ActionType: VisualSearch -> WebSearchUrl:
* ActionType: ImageById -> WebSearchUrl:
* ActionType: RelatedSearches -> WebSearchUrl:
* ActionType: DocumentLevelSuggestions -> WebSearchUrl:
* ActionType: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1
* ActionType: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1

Jak widać na poprzedniej liście, typy `TopicResults` i `ImageResults` zawierają zapytania dotyczące powiązanych obrazów. Adresy URL na liście to linki do wyników wyszukiwania Bing.


## <a name="pagesincluding-actiontype-urls-of-images-found-by-visual-search"></a>Adresy URL typu akcji ActionType o wartości PagesIncluding obrazów znalezionych przez wyszukiwanie wizualne

Uzyskanie rzeczywistych adresów URL obrazów wymaga rzutowania, które odczytuje typ akcji `ActionType` jako `ImageModuleAction` zawierający element `Data` z listą wartości.  Każda wartość to adres URL obrazu.  Następujący kod rzutuje typ akcji `PagesIncluding` na obiekt `ImageModuleAction` i odczytuje wartości.
```
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```
Aby uzyskać więcej informacji o tych typach danych, zobacz [Images - Visual Search (Obrazy — wyszukiwanie wizualne)](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).

## <a name="complete-code"></a>Kompletny kod

Poniższy kod umożliwia uruchomienie poprzednich przykładów. Wysyła on token `ImageInsightsToken` w żądaniu wpisu. Następnie są wyświetlane adresy URL wyszukiwania Bing dla każdego elementu ActionType. Jeśli typ ActionType to `PagesIncluding`, ten kod pobiera elementy `ImageObject` w obiekcie `Data`.  Element `Data` zawiera listę wartości, które są adresami URL obrazów na stronach internetowych.  Skopiuj i wklej wynikowe adresy URL wyszukiwania wizualnego w przeglądarce, aby wyświetlić wyniki. Skopiuj i wklej elementy ContentUrl w przeglądarce, aby wyświetlić obrazy.

```
using System;
using System.IO;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;

namespace VisualSearchFeatures
{
    class Program
    {
        static void Main(string[] args)
        {
            String subscriptionKey = "YOUR-ACCESS-KEY";

            insightsToken = ImageResults(subscriptionKey);

            VisualSearchInsightsToken(subscriptionKey, insightsToken);

            Console.WriteLine("Any key to quit...");
            Console.ReadKey();

        }

        // Searches for results on query (Canadian Rockies) and gets an ImageInsightsToken.
        // Also prints first image insights token, thumbnail url, and image content url.
        private static String ImageResults(String subKey)
        {
            String insightTok = "None";
            try
            {
                var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
                var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
                Console.WriteLine("Search images for query \"canadian rockies\"");

                if (imageResults == null)
                {
                    Console.WriteLine("No image result data.");
                }
                else
                {
                    // Image results
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        insightTok = firstImageResult.ImageInsightsToken;
                        Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");  
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
                    }
                    else
                    {
                        insightTok = "None found";
                        Console.WriteLine("Couldn't find image results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }

            return insightTok;
        }


        // This method will get Visual Search results from an imageInsightsToken obtained from the return value of the ImageResults method.
        // The method includes imageInsightsToken the in a knowledgeRequest parameter, along with a cropArea object. 
        // It prints out the imageInsightsToken uploaded in the request.
        // Finally the example prints URLs of images found using the imageInsightsToken.

        public static void VisualSearchInsightsToken(string subscriptionKey, string insightsTok)
        {
            var client = new VisualSearchAPI(new Microsoft.Azure.CognitiveServices.Search.VisualSearch.ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                // The image can be specified via an insights token, in the ImageInfo object.
                ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);

                // An image binary is not necessary here, as the image is specified by insights token.
                VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
                Console.WriteLine("\r\nVisual search request with imageInsightsToken and knowledgeRequest");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // Visual Search results
                    if (visualSearchResults.Image?.ImageInsightsToken != null)
                    {
                        Console.WriteLine($"Uploaded image insights token: {insightsTok}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find image insights token!");
                    }

                    if (visualSearchResults.Tags.Count > 0)
                    {
                        // List of tags
                        foreach (ImageTag t in visualSearchResults.Tags)
                        {
                            foreach (ImageAction i in t.Actions)
                            {
                                Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

                                if (i.ActionType == "PagesIncluding")
                                {
                                    foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                                    {
                                        Console.WriteLine("ContentURL: " + o.ContentUrl);
                                    }
                                }
                            }
                        }
                    }

                    else
                    {
                        Console.WriteLine("Couldn't find image tags!");
                    }

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

## <a name="next-steps"></a>Następne kroki
[Odpowiedź wyszukiwania wizualnego](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview#the-response)
