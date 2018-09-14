---
title: Zestaw SDK wyszukiwania wizualnego Bing obszaru kadrowania powoduje samouczek | Dokumentacja firmy Microsoft
description: Jak uzyskać adresy URL obrazów podobnie jak przyciąć obszaru przekazanego obrazu za pomocą usługi Bing wyszukiwania zestawu SDK programu Visual.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/20/2018
ms.author: rosh
ms.openlocfilehash: dd51ed7c710cc51a9fe0e63e55aa0d2c4ea24bee
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574493"
---
# <a name="tutorial-bing-visual-search-sdk-image-crop-area-and-results"></a>Samouczek: Obszar przycinanie obrazu zestaw SDK wyszukiwania wizualnego Bing i wyniki
Zestaw SDK programu Visual Search zawiera opcję, aby wybrać obszar obrazu i Znajdź online obrazy, które są podobne do obszaru przycięcia większy obraz.  W tym przykładzie określa obszar kadrowania przedstawiający jedna osoba z obrazu, który zawiera kilka osób.  Kod wysyła obszaru kadrowania i adres URL obrazu większych i zwraca wyniki, które zawierają adresy URL wyszukiwania Bing i adresów URL podobnych obrazów znalezione w trybie online.

## <a name="prerequisites"></a>Wymagania wstępne

Konieczne będzie [programu Visual Studio 2017](https://www.visualstudio.com/downloads/) aby otrzymać ten kod w systemie Windows. (Można korzystać z bezpłatnej wersji Community Edition).

Konieczne jest posiadanie [konta interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) za pomocą interfejsów API wyszukiwania Bing. [Bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) jest wystarczająca na potrzeby tego przewodnika Szybki Start. Wymagany jest klucz dostępu podany przy wywołaniu metody Aktywuj bezpłatną wersję próbną lub klucz płatnej subskrypcji może używać z pulpitu nawigacyjnego platformy Azure.

## <a name="application-dependencies"></a>Zależności aplikacji
Aby skonfigurować aplikację konsoli przy użyciu zestawu SDK wyszukiwania w sieci Web Bing, przejdź do opcji Zarządzaj pakietami NuGet z poziomu Eksploratora rozwiązań w programie Visual Studio. Dodaj pakiet Microsoft.Azure.CognitiveServices.Search.VisualSearch.

Instalowanie pakietu NuGet zestawu SDK z wyszukiwania w sieci Web instaluje również zależności, w tym:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="image-and-crop-area"></a>Obszar obrazu i przycięcia
Na poniższej ilustracji przedstawiono szczebla zespół firmy Microsoft.  Przy użyciu zestawu SDK programu Visual Search, możemy przekazać obszar przycinanie obrazu i Znajdź inne obrazy i strony sieci Web, które zawierają jednostki w wybranym obszarze większy obraz.  W takim przypadku jednostki jest osobą.

![Zespół szczebla firmy Microsoft](./media/MS_SrLeaders.jpg)

## <a name="specify-the-crop-area-as-imageinfo-in-visualsearchrequest"></a>Określ obszar przycięcia jako ImageInfo w VisualSearchRequest
W tym przykładzie użyto obszar przycięcia w poprzednim obrazie, który określa lewej górnej i zmniejszyć odpowiednie współrzędne według wartości procentowej całego obrazu.  Poniższy kod tworzy `ImageInfo` obiekt z obszaru kadrowania i obciążeniami `ImageInfo` do obiektu `VisualSearchRequest`.  `ImageInfo` Obiektu zawiera również adres URL obrazu w trybie online.

```
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg;
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```
## <a name="search-for-images-similar-to-crop-area"></a>Wyszukuj obrazy, podobne do obszaru przycinania
`VisualSearchRequest` Zawiera przycięcia obszaru informacje dotyczące obrazu i jego adres URL.  `VisualSearchMethodAsync` Metoda pobiera wyniki.
```
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Pobieranie danych adresu URL z ImageModuleAction
Wizualne wyniki wyszukiwania są `ImageTag` obiektów.  Każdy znacznik zawiera listę `ImageAction` obiektów.  Każdy `ImageAction` zawiera `Data` pola, które znajduje się lista wartości, które są zależne od typu akcji:

Różne typy można uzyskać z następującym kodem:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
Zwraca kompletnej aplikacji:

* ActionType: PagesIncluding WebSearchURL:
* ActionType: MoreSizes WebSearchURL:
* ActionType: VisualSearch WebSearchURL:
* ActionType: ImageById WebSearchURL: 
* ActionType: RelatedSearches WebSearchURL:
* ActionType: -> jednostki WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx, 5380.1
* ActionType: -> TopicResults WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx, 5382.1
* ActionType: -> ImageResults WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx, 5384.1

Jak pokazano na powyższej liście `Entity` `ActionType` zawiera zapytanie wyszukiwania Bing, które zwraca informacje na temat rozpoznawalnych osoby, miejsca lub rzeczy.  `TopicResults` i `ImageResults` typy zawierać zapytania dotyczące powiązanych obrazów. Adresy URL łącza listy wyników wyszukiwania Bing.


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

## <a name="complete-code"></a>Kompletny kod

Poniższy kod działa w poprzednich przykładach. Wysyła obrazu binarnego w treści żądania post, wraz z obiektem cropArea i drukuje Bing wyszukiwania adresów URL dla każdego obiektu ActionType. Jeśli ActionType PagesIncluding, ten kod pobiera elementy ImageObject ImageObject danych.  Dane zawierają listę wartości, które są adresami URL obrazów na stronach sieci Web.  Skopiuj i Wklej wynikowy Visual wyszukiwania adresów URL do przeglądarki do wyświetlenia wyników. Skopiuj i Wklej elementy ContentUrl do przeglądarki, aby wyświetlić obrazy.

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

            VisualSearchImageBinaryWithCropArea(subscriptionKey);

            Console.WriteLine("Any key to quit...");
            Console.ReadKey();

        }

        public static void VisualSearchImageBinaryWithCropArea(string subscriptionKey)
        {
            var client = new VisualSearchAPI(new Microsoft.Azure.CognitiveServices.Search.VisualSearch.ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
                
                // The ImageInfo struct specifies the crop area in the image and the URL of the larger image. 
                string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
                ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);
                
                VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);

                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

                Console.WriteLine("\r\nVisual search request with image from URL and crop area combined in knowledgeRequest");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // List of tags
                    if (visualSearchResults.Tags.Count > 0)
                    {

                        foreach(ImageTag t in visualSearchResults.Tags)
                        {
                            foreach (ImageAction i in t.Actions)
                            { 
                                Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

                                if (i.ActionType == "PagesIncluding")
                                {
                                    foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
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