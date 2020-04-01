---
title: 'Samouczek: Przycinanie obrazu za pomocą zestawu Bing Visual Search SDK'
description: Użyj SDK wyszukiwania wizualnego Bing, aby uzyskać szczegółowe informacje z określonych ares na obrazie.
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2019
ms.author: aahi
ms.openlocfilehash: 4778a4089c7374c1ac6a9312064dcfb1e0325b63
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478498"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Samouczek: Przycinanie obrazu za pomocą zestawu Bing Visual Search SDK dla języka C #

Bing Visual Search SDK umożliwia przyciącienie obrazu przed znalezieniem podobnych obrazów online. Ta aplikacja uprawy jednej osoby z obrazu zawierającego kilka osób, a następnie zwraca wyniki wyszukiwania zawierające podobne obrazy znalezione w Internecie.

Pełny kod źródłowy dla tej aplikacji jest dostępny z dodatkową obsługą błędów i adnotacjami na [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs).

Ten samouczek ilustruje, jak:

> [!div class="checklist"]
> * Wysyłanie żądania przy użyciu sdk wyszukiwania wizualnego Bing
> * Przycinanie obszaru obrazu do wyszukiwania za pomocą wyszukiwania wizualnego Bing
> * Odbieranie i obsługę odpowiedzi
> * Znajdowanie adresów URL elementów akcji w odpowiedzi

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna edycja [programu Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Jeśli używasz systemu Linux/MacOS, możesz uruchomić tę aplikację przy użyciu środowiska [Mono](https://www.mono-project.com/).
* Zainstalowany pakiet [NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0).
    - W Eksploratorze rozwiązań w programie Visual Studio kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami NuGet** z menu. Zainstaluj pakiet `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Zainstalowanie pakietu NuGet Custom Search powoduje również zainstalowanie następujących zestawów:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Określanie obszaru kadrowanie obrazu

Ta aplikacja przycina obszar tego obrazu zespołu wyższego kierownictwa firmy Microsoft. Ten obszar kadrowanie jest definiowany przy użyciu współrzędnych lewego i prawego dolnego dolnego, reprezentowanych jako procent całego obrazu:  

![Zespół liderów wyższego szczebla firmy Microsoft](./media/MS_SrLeaders.jpg)

Ten obraz jest przycięty `ImageInfo` przez utworzenie obiektu z obszaru `ImageInfo` kadrowania `VisualSearchRequest`i załadowanie obiektu do pliku . Obiekt `ImageInfo` zawiera również adres URL obrazu:

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>Wyszukiwanie obrazów podobnych do obszaru kadru

Zmienna `VisualSearchRequest` zawiera informacje o obszarze kadru obrazu i jego adresie URL. Metoda `VisualSearchMethodAsync()` pobiera wyniki:

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Pobierz dane adresu URL z`ImageModuleAction`

Wyniki wyszukiwania wizualnego `ImageTag` Bing są obiektami. Każdy tag zawiera listę obiektów `ImageAction`. Każdy `ImageAction` zawiera `Data` pole, które jest listą wartości, które zależą od typu akcji.

Można wydrukować różne typy za pomocą następującego kodu:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

Kompletna aplikacja zwraca:

|ActionType  |Adres URL  | |
|---------|---------|---------|
|StronyW tym WebSearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelatedSearches WebSearchURL     |         |
|Jednostka -> WebSearchUrl     | https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=11&r=https%3a%2f%2fww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |
|Wyniki tematów -> WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sP kUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |
|Wyniki obrazów -> WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgO&v=1&r=https%3a%2f%2f%2fww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |

Jak pokazano powyżej, `Entity` ActionType zawiera zapytanie wyszukiwania Bing, który zwraca informacje o rozpoznawalnej osoby, miejsce lub rzeczy. Typy `TopicResults` i `ImageResults` zawierają zapytania dotyczące powiązanych obrazów. Adresy URL na liście to linki do wyników wyszukiwania Bing.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>Pobierz adresy `PagesIncluding` `ActionType` URL obrazów

Uzyskanie rzeczywistych adresów URL obrazów wymaga rzutowania, które odczytuje typ akcji `ActionType` jako `ImageModuleAction` zawierający element `Data` z listą wartości. Każda wartość to adres URL obrazu. Następujące rzuty `PagesIncluding` typu akcji `ImageModuleAction` i odczytuje wartości:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji sieci Web wyszukiwania wizualnego](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>Zobacz też
> [Co to jest interfejs API wyszukiwania wizualnego Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview)
