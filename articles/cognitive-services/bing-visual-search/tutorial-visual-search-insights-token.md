---
title: Znajdowanie podobnych obrazów z poprzednich wyszukiwań przy użyciu funkcji ImageInsightsToken — wyszukiwanie wizualne Bing
titleSuffix: Azure Cognitive Services
description: Użyj sdk wyszukiwania wizualnego Bing, aby uzyskać adresy URL obrazów określonych przez ImageInsightsToken.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: aahi
ms.openlocfilehash: d005800ed317ff21389f18e4440858ea11042e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370094"
---
# <a name="tutorial-find-similar-images-from-previous-searches-using-an-image-insights-token"></a>Samouczek: Znajdowanie podobnych obrazów z poprzednich wyszukiwań przy użyciu tokenu wglądu w obrazy

Visual Search SDK umożliwia znajdowanie obrazów online z `ImageInsightsToken`poprzednich wyszukiwań, które zwracają . Ta aplikacja `ImageInsightsToken` pobiera i używa tokenu w kolejnym wyszukiwaniu. Następnie wysyła `ImageInsightsToken` do usługi Bing i zwraca wyniki, które obejmują adresy URL wyszukiwania Bing i adresy URL podobnych obrazów znalezionych w Internecie.

Pełny kod źródłowy dla tego samouczka można znaleźć z dodatkową obsługą błędów i adnotacjami na [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs).

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna edycja [programu Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Jeśli używasz Linuksa /MacOS, możesz uruchomić tę aplikację za pomocą [Mono](https://www.mono-project.com/).
* Pakiety wyszukiwania wizualnego NuGet i wyszukiwania obrazów.
    - W Eksploratorze rozwiązań w programie Visual Studio kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami NuGet** z menu. Zainstaluj `Microsoft.Azure.CognitiveServices.Search.CustomSearch` pakiet i `Microsoft.Azure.CognitiveServices.Search.ImageSearch` pakiet. Zainstalowanie pakietu NuGet powoduje także zainstalowanie następujących elementów:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>Pobierz ImageInsightsToken z SDK wyszukiwania obrazów Bing

Ta aplikacja używa `ImageInsightsToken` uzyskanego za pośrednictwem [sdk wyszukiwania obrazów Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart). W nowej aplikacji konsoli języka C# utwórz `ImageSearchClient()`klienta, aby wywołać interfejs API przy użyciu programu . Następnie `SearchAsync()` użyj z zapytaniem:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Zapisz pierwszy wynik `imageResults.Value.First()`wyszukiwania za pomocą , a `ImageInsightsToken`następnie zapisz wgląd obrazu .

```csharp
String insightTok = "None";
if (imageResults.Value.Count > 0)
{
    var firstImageResult = imageResults.Value.First();
    insightTok = firstImageResult.ImageInsightsToken;
}
else
{
    insightTok = "None found";
    Console.WriteLine("Couldn't find image results!");
}
```

Jest `ImageInsightsToken` to wysyłane do wyszukiwania wizualnego Bing w żądaniu.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Dodawanie żądania ImageInsightsToken do wyszukiwania wizualnego

Określ `ImageInsightsToken` dla żądania wyszukiwania wizualnego, tworząc obiekt `ImageInfo` z `ImageInsightsToken` zawartych w odpowiedziach z wyszukiwania wizualnego Bing.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Znajdowanie obrazów z obrazów ObrazówDoświadczenie za pomocą funkcji wyszukiwania wizualnego Bing

Obiekt `VisualSearchRequest` zawiera informacje o `ImageInfo` obrazie, który ma zostać przeszukany. Metoda `VisualSearchMethodAsync()` pobiera wyniki. Nie trzeba podać binarny obrazu, jak obraz jest reprezentowany przez token.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Iteruje wyniki wyszukiwania wizualnego

Wyniki wyszukiwania wizualnego to obiekty `ImageTag`. Każdy tag zawiera listę obiektów `ImageAction`. Każdy `ImageAction` zawiera `Data` pole, które jest listą wartości, które zależą od typu akcji. Można na przykład iterować przez `ImageTag` obiekty w `visualSearchResults.Tags` `ImageAction` programie , i uzyskać w nim znacznik. Poniższy przykład drukuje `PagesIncluding` szczegóły działań:

```csharp
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
```

### <a name="pagesincluding-actiontypes"></a>PagesIncluding ActionTypes

Uzyskanie rzeczywistych adresów URL obrazu z typów `ActionType` akcji `ImageModuleAction`wymaga rzutu, który odczytuje jako , który zawiera `Data` element z listą wartości. Każda wartość to adres URL obrazu.  Następujące rzuty `PagesIncluding` typu akcji `ImageModuleAction` i odczytuje wartości:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

Aby uzyskać więcej informacji o tych typach danych, zobacz [Images - Visual Search (Obrazy — wyszukiwanie wizualne)](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).

## <a name="returned-urls"></a>Zwrócone adresy URL

Pełna aplikacja zwraca następujące adresy URL:

|ActionType  |Adres URL  | |
|---------|---------|---------|
|MoreSizes -> WebSearchUrl     |         |
|VisualSearch -> WebSearchUrl     |         |
|ImageById -> WebSearchUrl    |         |
|RelatedSearches -> WebSearchUrl:    |         |
|DocumentLevelSuggestions -> WebSearchUrl:     |         |
|Wyniki tematów -> WebSearchUrl    | https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|Wyniki obrazów -> WebSearchUrl    |  https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |

Jak pokazano `TopicResults` powyżej, `ImageResults` i typy zawierają zapytania dotyczące powiązanych obrazów. Adresy URL łączą się z wynikami wyszukiwania Bing.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji sieci Web wyszukiwania wizualnego](tutorial-bing-visual-search-single-page-app.md)
