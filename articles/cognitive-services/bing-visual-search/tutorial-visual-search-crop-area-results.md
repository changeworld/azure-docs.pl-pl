---
title: Obszar kadrowania Visual SDK wyszukiwania usługi Bing powoduje samouczek | Dokumentacja firmy Microsoft
description: Jak używać Visual SDK wyszukiwania usługi Bing można uzyskać podobną do Przytnij obszaru załadowanego obrazu adresy URL obrazów.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/20/2018
ms.author: rosh
ms.openlocfilehash: fed9bb396d72f140235a2743c1447076606bb87c
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939464"
---
# <a name="tutorial-bing-visual-search-sdk-image-crop-area-and-results"></a>Samouczek: Obszar przycięcia obrazu Visual SDK wyszukiwania usługi Bing i wyniki
Visual SDK wyszukiwania zawiera opcję Zaznacz obszar obrazu i znalezienie online obrazów, które są podobne do obszaru przycięcia większy obraz.  W tym przykładzie określa obszar kadrowania przedstawiający jedna osoba z obrazu, który zawiera kilka osób.  Kod wysyła obszaru przycięcia i adres URL obrazu większych i zwraca wyniki, które obejmują adresy URL wyszukiwania usługi Bing i adresy URL obrazów podobne znalezione w trybie online.

## <a name="prerequisites"></a>Wymagania wstępne

Konieczne będzie [programu Visual Studio 2017](https://www.visualstudio.com/downloads/) aby otrzymać ten kod z systemem Windows. (Bezpłatna wersja Community będzie działać.)

Musi mieć [kognitywnych interfejsu API usług konta](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z interfejsy API wyszukiwania usługi Bing. [Bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) jest wystarczająca dla tego przewodnika Szybki Start. Należy klucz dostępu podany przy wywołaniu metody aktywacji bezpłatną wersję próbną lub może używać klucza płatnej subskrypcji z pulpitu nawigacyjnego platformy Azure.

## <a name="application-dependencies"></a>Zależności aplikacji
Aby skonfigurować aplikację konsoli przy użyciu zestawu SDK wyszukiwania usługi Bing sieci Web, przejdź do opcji zarządzania pakietami NuGet z Eksploratora rozwiązań w programie Visual Studio. Dodaj pakiet Microsoft.Azure.CognitiveServices.Search.VisualSearch.

Instalowanie pakietu NuGet Web wyszukiwania SDK instaluje również zależności, w tym:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="image-and-crop-area"></a>Obszar obrazu i przycięcia
Na poniższej ilustracji przedstawiono kadry kierowniczej wyższych firmy Microsoft.  Za pomocą Visual SDK wyszukiwania, możemy przekazać przycięcia części obrazu i znaleźć inne obrazy i strony sieci Web, które zawierają jednostki w wybranym obszarze większy obraz.  W takim przypadku jednostka jest osoby.

![Kadry kierowniczej wyższych firmy Microsoft](./media/MS_SrLeaders.jpg)

## <a name="specify-the-crop-area-as-imageinfo-in-visualsearchrequest"></a>Określ obszar przycięcia jako ImageInfo w VisualSearchRequest
W tym przykładzie użyto obszar przycięcia poprzedniego obrazu, który określa lewy górny i dolny prawy współrzędne według wartości procentowej całego obrazu.  Poniższy kod tworzy `ImageInfo` obiektu z obszaru przycięcia i obciążeń `ImageInfo` obiekt do `VisualSearchRequest`.  `ImageInfo` Obiektu zawiera również adres URL obrazu, w trybie online.

```
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.25, left: (float)0.01, right: (float)0.2);
string imageURL = "http://windowsgeek.lk/wp-content/uploads/2016/04/cxc.png";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```
## <a name="search-for-images-similar-to-crop-area"></a>Wyszukaj podobne do obszaru przycinania obrazów
`VisualSearchRequest` Omówiono przycięcia obszaru obrazu i jego adres URL.  `VisualSearchMethodAsync` Metoda pobiera wyniki.
```
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Pobierz dane adres URL z ImageModuleAction
Visual wyniki wyszukiwania są `ImageTag` obiektów.  Każdy znacznik zawiera listę `ImageAction` obiektów.  Każdy `ImageAction` zawiera `Data` pola, które znajduje się lista wartości, które są zależne od typu akcji:

Możesz uzyskać różne typy z następującym kodem:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
Kompletna aplikacja zwraca:

* ActionType: PagesIncluding WebSearchURL:
* ActionType: MoreSizes WebSearchURL:
* ActionType: VisualSearch WebSearchURL:
* ActionType: ImageById WebSearchURL: 
* ActionType: RelatedSearches WebSearchURL:
* ActionType: -> jednostki WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx, 5380.1
* ActionType: -> TopicResults WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx, 5382.1
* ActionType: -> ImageResults WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx, 5384.1

Jak pokazano w powyższej listy `Entity` `ActionType` zawiera zapytanie wyszukiwania usługi Bing, która zwraca informacje na temat rozpoznawalnych osoby, miejsca lub operacją.  `TopicResults` i `ImageResults` typy zawierać zapytania dotyczące powiązanych obrazy. Adresy URL na liście łącze do wyników wyszukiwania usługi Bing.


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

## <a name="complete-code"></a>Kompletny kod

Poniższy kod działa w poprzednich przykładach. Wysyła obraz binarny w treści żądania post, wraz z obiektem cropArea i drukuje Bing wyszukiwania adresów URL dla każdego obiektu ActionType. Jeśli ActionType PagesIncluding, kod pobiera elementy ImageObject ImageObject danych.  Dane zawierają listę wartości, które są adresy URL obrazów na stronach sieci Web.  Skopiuj i Wklej wynikowy Visual wyszukiwania adresów URL do przeglądarki, aby wyświetlić wyniki. Skopiuj i Wklej elementy ContentUrl do przeglądarki, aby wyświetlić obrazy.

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
                CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.25, left: (float)0.01, right: (float)0.2);
                
                // The ImageInfo struct specifies the crop area in the image and the URL of the larger image. 
                string imageURL = "http://windowsgeek.lk/wp-content/uploads/2016/04/cxc.png";
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
[Visual odpowiedzi wyszukiwania](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/overview#the-response)