---
title: Znajdź podobne obrazy z poprzednich wyników wyszukiwania za pomocą ImageInsightsToken - wyszukiwania wizualnego Bing
titlesuffix: Azure Cognitive Services
description: Umożliwia uzyskiwanie adresów URL określony przez ImageInsightsToken obrazów Bing wyszukiwania zestawu SDK programu Visual.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: 540c4132789d0d1ab666d452b5d6fb220025d5dc
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55157384"
---
# <a name="find-similar-images-from-previous-searches-using-imageinsightstoken"></a>Znajdź podobne obrazy z poprzednich wyników wyszukiwania za pomocą ImageInsightsToken

Zestaw SDK programu Visual wyszukiwania umożliwia znalezienie obrazów w tryb online, korzystając z poprzednich wyników wyszukiwania, które zwracają `ImageInsightsToken`.  Ta aplikacja pobiera `ImageInsightsToken` tokenu i używa go do kolejnych wyszukiwania. Następnie wysyła `ImageInsightsToken` do usługi Bing i zwraca wyniki, które obejmują adresy URL wyszukiwania Bing i adresy URL podobnych obrazów znalezione w trybie online.

Pełny kod źródłowy dla tego przykładu wraz z dodatkową obsługą błędów i adnotacjami można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInisghtsTokens.cs).

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna wersja programu [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Jeśli używasz systemu Linux/MacOS, możesz uruchomić tę aplikację przy użyciu środowiska [Mono](http://www.mono-project.com/).
* Pakiety NuGet, wyszukiwanie wizualne i wyszukiwania obrazów. 
    - W Eksploratorze rozwiązań w programie Visual Studio kliknij prawym przyciskiem myszy swój projekt i wybierz polecenie `Manage NuGet Packages` z menu. Zainstaluj `Microsoft.Azure.CognitiveServices.Search.CustomSearch` pakietu, a `Microsoft.Azure.CognitiveServices.Search.ImageSearch` pakietu. Zainstalowanie pakietu NuGet powoduje także zainstalowanie następujących elementów:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>Uzyskiwanie ImageInsightsToken wyszukiwania obrazów Bing zestawu SDK

Ta aplikacja używa `ImageInsightsToken` uzyskane za pośrednictwem [zestaw SDK wyszukiwania obrazów Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart). W nowym C# konsoli aplikacji, należy utworzyć klienta do wywołania interfejsu API przy użyciu `ImageSearchAPI()`. Następnie użyj `SearchAsync()` ze swoim zapytaniem.

```csharp
var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Store pierwszego wyszukiwania wynik zastosowania `imageResults.Value.First()`, a następnie zapiszesz insight obrazu `ImageInsightsToken`. 

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

To `ImageInsightsToken` będą wysyłane do wyszukiwania wizualnego Bing w żądaniu.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Dodawanie ImageInsightsToken do żądania wyszukiwania wizualnego

Określ `ImageInsightsToken` żądania wyszukiwania wizualnego, tworząc `ImageInfo` obiektu z `ImageInsightsToken` zawartych w odpowiedzi z wyszukiwania wizualnego Bing. 

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Używaj wyszukiwania wizualnego Bing do znajdowania obrazów z ImageInsightsToken

`VisualSearchRequest` Obiekt zawiera informacje o obrazie w `ImageInfo` ma być przeszukiwany. Metoda `VisualSearchMethodAsync()` pobiera wyniki. Nie należy podać binarnego obrazów, obraz, który jest reprezentowany przez ten token.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Iteracyjnego przeglądania wyników wyszukiwania wizualnego

Wyniki wyszukiwania wizualnego to obiekty `ImageTag`.  Każdy tag zawiera listę obiektów `ImageAction`.  Każdy `ImageAction` zawiera `Data` pola, które znajduje się lista wartości, które są zależne od typu akcji. Można wykonać iterację `ImageTag` obiekty w `visualSearchResults.Tags`dla wystąpienia i get `ImageAction` tag znajdujący się w nim. Poniższy przykład wyświetla szczegółowe informacje o `PagesIncluding` akcji.

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

Uzyskiwanie adresów URL rzeczywisty obraz z typów akcji wymaga rzutowania, która odczytuje `ActionType` jako `ImageModuleAction`, który zawiera `Data` element z listy wartości.  Każda wartość to adres URL obrazu.  Następujący kod rzutuje typ akcji `PagesIncluding` na obiekt `ImageModuleAction` i odczytuje wartości.

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


## <a name="returned-urls"></a>Adresy URL zwrócone

Kompletna aplikacja zwraca następujące adresy URL:

|ActionType  |Adres URL  | |
|---------|---------|---------|
|MoreSizes -> WebSearchUrl     |         |         
|VisualSearch -> WebSearchUrl     |         |         
|ImageById -> WebSearchUrl    |         |         
|RelatedSearches -> WebSearchUrl:    |         |         
|DocumentLevelSuggestions -> WebSearchUrl:     |         |         
|TopicResults -> WebSearchUrl    | https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |         
|ImageResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |         

Jak wspomniano powyżej, `TopicResults` i `ImageResults` typy zawierać zapytania dotyczące powiązanych obrazów. Wyniki wyszukiwania adresów URL linku do usługi Bing.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](tutorial-bing-visual-search-single-page-app.md)
