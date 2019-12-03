---
title: 'Samouczek: Kadrowanie obrazu za pomocą zestawu SDK wyszukiwanie wizualne Bing'
description: Użyj zestawu SDK wyszukiwanie wizualne Bing, aby uzyskać szczegółowe informacje z określonych arach na obrazie.
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: 7adca44f1710431ad1095cbd0da897d4c7c7f325
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689350"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Samouczek: Kadrowanie obrazu za pomocą zestawu SDK wyszukiwanie wizualne Bing dlaC#

Zestaw SDK wyszukiwanie wizualne Bing umożliwia Przycinanie obrazu przed znalezieniem podobnych obrazów online. Ta aplikacja przycina pojedynczą osobę z obrazu zawierającego kilka osób, a następnie zwraca wyniki wyszukiwania zawierające podobne obrazy Znalezione w trybie online.

Pełny kod źródłowy tej aplikacji jest dostępny z dodatkową obsługą błędów i adnotacjami w serwisie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs).

W tym samouczku pokazano, jak:

> [!div class="checklist"]
> * Wysyłanie żądania przy użyciu zestawu SDK wyszukiwanie wizualne Bing
> * Przytnij obszar obrazu do wyszukiwania za pomocą wyszukiwanie wizualne Bing
> * Odbieranie i obsługa odpowiedzi
> * Znajdowanie adresów URL elementów akcji w odpowiedzi

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna wersja programu [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Jeśli używasz systemu Linux/MacOS, możesz uruchomić tę aplikację przy użyciu środowiska [Mono](https://www.mono-project.com/).
* Zainstalowany pakiet [NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0).
    - Na Eksplorator rozwiązań w programie Visual Studio kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami NuGet** z menu. Zainstaluj pakiet `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Zainstalowanie pakietu NuGet Custom Search powoduje również zainstalowanie następujących zestawów:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Określ obszar przycinania obrazu

Ta aplikacja przycina obszar tego obrazu zespołu kierownictwa firmy Microsoft. Ten obszar kadrowania jest definiowany przy użyciu współrzędnych górnego i dolnego, reprezentowanego jako procent całego obrazu:  

![Zespół liderów wyższego szczebla firmy Microsoft](./media/MS_SrLeaders.jpg)

Obraz jest przycięty przez utworzenie obiektu `ImageInfo` z obszaru kadrowania i załadowanie obiektu `ImageInfo` do `VisualSearchRequest`. Obiekt `ImageInfo` również zawiera adres URL obrazu:

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>Wyszukaj obrazy podobne do obszaru kadrowania

Zmienna `VisualSearchRequest` zawiera informacje o obszarze przycinania obrazu i jego adresie URL. Metoda `VisualSearchMethodAsync()` pobiera wyniki:

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Pobierz dane adresu URL z `ImageModuleAction`

Wyniki wyszukiwanie wizualne Bing są obiektami `ImageTag`. Każdy tag zawiera listę obiektów `ImageAction`. Każdy `ImageAction` zawiera pole `Data`, które jest listą wartości, które są zależne od typu akcji.

Różne typy można wydrukować przy użyciu następującego kodu:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

Kompletna aplikacja zwraca:

|Obiektu ActionType  |Adres URL  | |
|---------|---------|---------|
|PagesIncluding WebSearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelatedSearches WebSearchURL     |         |
|WebSearchUrl jednostki — >     | https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |
|TopicResults — > WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |
|ImageResults — > WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |

Jak pokazano powyżej, `Entity` ActionType zawiera zapytanie wyszukiwania Bing, które zwraca informacje dotyczące rozpoznawalnej osoby, miejsca lub rzeczy. Typy `TopicResults` i `ImageResults` zawierają zapytania dotyczące powiązanych obrazów. Adresy URL na liście to linki do wyników wyszukiwania Bing.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>Pobierz adresy URL dla obrazów `ActionType` `PagesIncluding`

Uzyskanie rzeczywistych adresów URL obrazów wymaga rzutowania, które odczytuje typ akcji `ActionType` jako `ImageModuleAction` zawierający element `Data` z listą wartości. Każda wartość to adres URL obrazu. Poniższe rzutowanie typu akcji `PagesIncluding` na `ImageModuleAction` i odczytuje wartości:

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
> [Tworzenie wyszukiwanie wizualne jednostronicowej aplikacji sieci Web](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>Zobacz także
> [Co to jest interfejs API wyszukiwania wizualnego Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview)
