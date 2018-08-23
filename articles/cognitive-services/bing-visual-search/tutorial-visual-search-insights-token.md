---
title: Samouczek ImageInsightsToken SDK wyszukiwania wizualnego Bing | Dokumentacja firmy Microsoft
description: Jak uzyskiwanie adresów URL określony przez ImageInsightsToken obrazów za pomocą zestawu SDK usługi Bing Visual Search.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: 8f6e7f7e88ae78fe7e8a9be4adefd689dd26d0f9
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2018
ms.locfileid: "41988797"
---
# <a name="tutorial-bing-visual-search-sdk-imageinsightstoken-and-results"></a>Samouczek: ImageInsightsToken SDK wyszukiwania wizualnego Bing i wyniki
Zestaw SDK programu Visual Search zawiera opcję, aby odnaleźć obrazów w tryb online, korzystając z poprzedniego wyszukiwania, która zwraca `ImageInsightsToken`.  W tym przykładzie pobiera `ImageInsightsToken` tokenu i używa go do kolejnych wyszukiwania.  Kod wysyła `ImageInsightsToken` do usługi Bing i zwraca wyniki, które obejmują adresy URL wyszukiwania Bing i adresy URL podobnych obrazów znalezione w trybie online.

## <a name="prerequisites"></a>Wymagania wstępne
Program Visual Studio 2017. Jeśli to konieczne, możesz pobrać wersję bezpłatna wersja community w tym miejscu: https://www.visualstudio.com/vs/community/.
Klucz interfejsu API usług cognitive services jest wymagany do uwierzytelniania wywołań zestawu SDK. Załóż konto bezpłatnej wersji próbnej klucza. Klucz wersji próbnej trwa przez siedem dni, za pomocą jednego wywołania na sekundę. Na potrzeby scenariuszy produkcyjnych należy kupić klucz dostępu. Zobacz też informacje o cenach.
Możliwość uruchamiania programu .NET core SDK, aplikacji programu .net core 1.1. Można uzyskać CORE, Framework i środowiska uruchomieniowego, które znajdują się w tym miejscu: https://www.microsoft.com/net/download/.

##<a name="application-dependencies"></a>Zależności aplikacji
Aby skonfigurować aplikację konsoli przy użyciu zestawu SDK wyszukiwania w sieci Web Bing, przejdź do opcji Zarządzaj pakietami NuGet z poziomu Eksploratora rozwiązań w programie Visual Studio. Dodaj:
* Microsoft.Azure.CognitiveServices.Search.VisualSearch
* Pakiety Microsoft.Azure.CognitiveServices.Search.ImageSearchpackage.

Instalowanie pakietu NuGet zestawu SDK z wyszukiwania w sieci Web instaluje również zależności, w tym:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="get-the-imageinsightstoken-from-image-search"></a>Uzyskiwanie ImageInsightsToken wyszukiwania obrazów
W tym przykładzie użyto `ImageInsightsToken` uzyskanych przez następującą metodę.  Aby uzyskać więcej informacji na temat tego wywołania, zobacz [obraz wyszukiwania zestawu SDK języka C# szybkiego startu](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

Kod wyszukuje wyników kwerendy dla "Kanadyjski Rockies" i pobiera ImageInsightsToken. Wyświetla pierwszy obraz insights tokenu, miniatury adres url i adres url zawartości obrazu.  Metoda ta zwraca `ImageInsightsToken`do użycia w kolejnych żądań wyszukiwania wizualnego.

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

## <a name="specify-the-imageinsightstoken-for-visual-search-request"></a>Określ ImageInsightsToken dla żądania wyszukiwania wizualnego
W tym przykładzie użyto zwracany przez metodę poprzedniego token szczegółowych informacji. Poniższy kod tworzy `ImageInfo` obiektu z `ImageInsightsToken` i ładuje obiektu ImageInfo do `VisualSearchRequest`. Określ `ImageInsightsToken` w `ImageInfo` dla `VisualSearchRequest`

```
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```
## <a name="use-visual-search-to-find-images-from-an-imageinsightstoken"></a>Umożliwia znajdowanie obrazów z ImageInsightsToken wyszukiwania wizualnego
`VisualSearchRequest` Zawiera informacje o obrazie do wyszukania w `ImageInfo` obiektu.  `VisualSearchMethodAsync` Metoda pobiera wyniki.
```
// An image binary is not necessary here, as the image is specified by insights token.
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
Console.WriteLine("\r\nVisual search request with knowledgeRequest");

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Pobieranie danych adresu URL z ImageModuleAction
Wizualne wyniki wyszukiwania są `ImageTag` obiektów.  Każdy znacznik zawiera listę `ImageAction` obiektów.  Każdy `ImageAction` zawiera `Data` pola, które znajduje się lista wartości, które są zależne od typu akcji:

Różne typy można uzyskać z następującym kodem:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
Zwraca kompletnej aplikacji:

* ActionType: MoreSizes -> WebSearchUrl:
* ActionType: VisualSearch -> WebSearchUrl:
* ActionType: ImageById -> WebSearchUrl:
* ActionType: RelatedSearches -> WebSearchUrl:
* ActionType: DocumentLevelSuggestions -> WebSearchUrl:
* ActionType: -> TopicResults WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx, 5823.1
* ActionType: -> ImageResults WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx, 5831.1

Jak pokazano na powyższej liście `TopicResults` i `ImageResults` typy zawierać zapytania dotyczące powiązanych obrazów. Adresy URL łącza listy wyników wyszukiwania Bing.


## <a name="pagesincluding-actiontype-urls-of-images-found-by-visual-search"></a>Adresy URL ActionType PagesIncluding obrazów znalezione przez wyszukiwanie wizualne

Adresy URL rzeczywisty obraz wymaga rzutowania, która odczytuje `ActionType` jako `ImageModuleAction`, który zawiera `Data` element z listy wartości.  Każda wartość jest adres URL obrazu.  Następujące rzutowania `PagesIncluding` typu akcji `ImageModuleAction` i odczytuje wartości.
```
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```
Aby uzyskać więcej informacji o tych typach danych, zobacz [obrazy — wyszukiwanie wizualne](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).
## <a name="complete-code"></a>Kompletny kod

Poniższy kod działa w poprzednich przykładach. Wysyła `ImageInsightsToken` w żądaniu post. A następnie wyświetla Bing wyszukiwania adresów URL dla każdego obiektu ActionType. Jeśli jest ActionType `PagesIncluding`, ten kod pobiera `ImageObject` elementy w `Data`.  `Data` Zawiera listę wartości, które są adresami URL obrazów na stronach sieci Web.  Skopiuj i Wklej wynikowy Visual wyszukiwania adresów URL do przeglądarki do wyświetlenia wyników. Skopiuj i Wklej elementy ContentUrl do przeglądarki, aby wyświetlić obrazy.

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
## <a name="next-steps"></a>Kolejne kroki
[Wizualne odpowiedzi wyszukiwania](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview#the-response)
