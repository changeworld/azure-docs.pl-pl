---
title: Samouczek Visual ImageInsightsToken SDK wyszukiwania usługi Bing | Dokumentacja firmy Microsoft
description: Jak używać Visual SDK wyszukiwania usługi Bing można pobrać adresy URL określone przez ImageInsightsToken obrazów.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: 578fa90f504920030b488d2b8fa3a2d0232cccce
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753662"
---
# <a name="tutorial-bing-visual-search-sdk-imageinsightstoken-and-results"></a>Samouczek: Visual ImageInsightsToken SDK wyszukiwania usługi Bing i wyniki
Visual wyszukiwania zestaw SDK zawiera opcję, aby odnaleźć obrazów w trybie online poprzedniego wyszukiwania, która zwraca `ImageInsightsToken`.  W tym przykładzie pobiera `ImageInsightsToken` i używa tokenu w kolejnych wyszukiwania.  Wysyła kod `ImageInsightsToken` do usługi Bing i zwraca wyniki, które obejmują adresy URL wyszukiwania usługi Bing i adresy URL obrazów podobne znalezione w trybie online.

## <a name="prerequisites"></a>Wymagania wstępne
Program Visual Studio 2017. Jeśli to konieczne, możesz pobrać społeczności bezpłatnej wersji w tym miejscu: https://www.visualstudio.com/vs/community/.
Klucz interfejsu API usługi kognitywnych jest wymagany do uwierzytelnienia wywołania SDK. Utwórz konto bezpłatnej wersji próbnej klucza. Klucz wersji próbnej jest prawidłowa na siedem dni z jednego wywołania na sekundę. W przypadku scenariuszy produkcji kupić klucz dostępu. Zobacz także informacje o cenach.
Możliwość uruchamiania .NET core SDK .net core 1.1 aplikacji. W tym miejscu można uzyskać CORE Framework i środowiska wykonawczego: https://www.microsoft.com/net/download/.

##<a name="application-dependencies"></a>Zależności aplikacji
Aby skonfigurować aplikację konsoli przy użyciu zestawu SDK wyszukiwania usługi Bing sieci Web, przejdź do opcji zarządzania pakietami NuGet z Eksploratora rozwiązań w programie Visual Studio. Dodaj:
* Microsoft.Azure.CognitiveServices.Search.VisualSearch
* Microsoft.Azure.CognitiveServices.Search.ImageSearchpackage pakietów.

Instalowanie pakietu NuGet Web wyszukiwania SDK instaluje również zależności, w tym:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="get-the-imageinsightstoken-from-image-search"></a>Pobierz ImageInsightsToken z obrazu wyszukiwania
W tym przykładzie użyto `ImageInsightsToken` uzyskać następującymi metodami.  Aby uzyskać więcej informacji na temat tego wywołania, zobacz [obraz wyszukiwania zestawu SDK C# — Szybki Start](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

Kod szuka wyników kwerendy "Kanadyjskich Rockies" i pobiera ImageInsightsToken. Wyświetla szczegółowe informacje pierwszy obraz url tokenu, miniatur i zawartości adres url obrazu.  Metoda zwraca `ImageInsightsToken`do użycia w kolejnych żądania wyszukiwania Visual.

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

## <a name="specify-the-imageinsightstoken-for-visual-search-request"></a>Określ ImageInsightsToken dla żądania wyszukiwania Visual
W tym przykładzie używa tokenu insights zwracany przez metodę poprzedniej. Poniższy kod tworzy `ImageInfo` obiekt z `ImageInsightsToken` i ładuje obiektu ImageInfo do `VisualSearchRequest`. Określ `ImageInsightsToken` w `ImageInfo` dla `VisualSearchRequest`

```
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```
## <a name="use-visual-search-to-find-images-from-an-imageinsightstoken"></a>Użyj Visual wyszukiwania, aby znaleźć obrazy z ImageInsightsToken
`VisualSearchRequest` Zawiera informacje o obrazie do wyszukiwania w `ImageInfo` obiektu.  `VisualSearchMethodAsync` Metoda pobiera wyniki.
```
// An image binary is not necessary here, as the image is specified by insights token.
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
Console.WriteLine("\r\nVisual search request with knowledgeRequest");

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Pobierz dane adres URL z ImageModuleAction
Visual wyniki wyszukiwania są `ImageTag` obiektów.  Każdy znacznik zawiera listę `ImageAction` obiektów.  Każdy `ImageAction` zawiera `Data` pola, które znajduje się lista wartości, które są zależne od typu akcji:

Możesz uzyskać różne typy z następującym kodem:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
Kompletna aplikacja zwraca:

* ActionType: MoreSizes -> WebSearchUrl:
* ActionType: VisualSearch -> WebSearchUrl:
* ActionType: ImageById -> WebSearchUrl:
* ActionType: RelatedSearches -> WebSearchUrl:
* ActionType: DocumentLevelSuggestions -> WebSearchUrl:
* ActionType: -> TopicResults WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx, 5823.1
* ActionType: -> ImageResults WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx, 5831.1

Jak pokazano w powyższej listy `TopicResults` i `ImageResults` typy zawierać zapytania dotyczące powiązanych obrazy. Adresy URL na liście łącze do wyników wyszukiwania usługi Bing.


## <a name="pagesincluding-actiontype-urls-of-images-found-by-visual-search"></a>Adresy URL ActionType PagesIncluding znalezione przez wyszukiwanie Visual obrazów

Uzyskiwanie adresów URL rzeczywisty obraz wymaga rzutowanie odczytujący `ActionType` jako `ImageModuleAction`, który zawiera `Data` element z listy wartości.  Każda wartość jest adres URL obrazu.  Następujące rzutowania `PagesIncluding` typu akcji `ImageModuleAction` i odczytuje wartości.
```
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```
Aby uzyskać więcej informacji dotyczących tych typów danych, zobacz [obrazów — wyszukiwanie Visual](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).
## <a name="complete-code"></a>Kompletny kod

Poniższy kod działa w poprzednich przykładach. Wysyła `ImageInsightsToken` w żądaniu post. A następnie wyświetla Bing wyszukiwania adresów URL dla każdego obiektu ActionType. Jeśli jest ActionType `PagesIncluding`, kod pobiera `ImageObject` elementy w `Data`.  `Data` Zawiera listę wartości, które są adresy URL obrazów na stronach sieci Web.  Skopiuj i Wklej wynikowy Visual wyszukiwania adresów URL do przeglądarki, aby wyświetlić wyniki. Skopiuj i Wklej elementy ContentUrl do przeglądarki, aby wyświetlić obrazy.

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
[Visual odpowiedzi wyszukiwania](https://review.docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/overview?branch=pr-en-us-44614#the-response)