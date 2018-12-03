---
title: 'Samouczek: obszar przycinania obrazu i wyniki — wyszukiwanie wizualne Bing'
description: Ten samouczek zawiera opis sposobu użycia zestawu Bing Visual Search SDK do uzyskiwania adresów URL obrazów podobnych do obszaru przycinania przekazanego obrazu.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 06/20/2018
ms.author: rosh
ms.openlocfilehash: 27141c014c9ccdf9d62c9bde5c96bd31abfc025e
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52447099"
---
# <a name="tutorial-bing-visual-search-sdk-image-crop-area-and-results"></a>Samouczek: zestaw Bing Visual Search SDK — obszar przycinania obrazu i wyniki
Zestaw Visual Search SDK udostępnia opcję wyboru obszaru obrazu i wyszukania online obrazów podobnych do obszaru przycinania większego obrazu.  W tym przykładzie określono obszar przycinania zawierający jedną osobę z obrazu zawierającego kilka osób.  Kod wysyła obszar przycinania i adres URL większego obrazu oraz zwraca wyniki obejmujące adresy URL wyszukiwania Bing i adresy URL znalezionych podobnych obrazów online.

## <a name="prerequisites"></a>Wymagania wstępne

Do uruchomienia tego kodu w systemie Windows będzie potrzebny program [Visual Studio 2017](https://www.visualstudio.com/downloads/). (Można korzystać z bezpłatnej wersji Community Edition).

Ten samouczek wymaga rozpoczęcia subskrypcji w warstwie cenowej S9, jak pokazano w temacie [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/). 

Aby rozpocząć subskrypcję w witrynie Azure Portal:
1. Wprowadź tekst „BingSearchV7” w polu tekstowym w górnej części witryny Azure Portal, w którym wyświetlany jest komunikat `Search resources, services, and docs`.  
2. W witrynie Marketplace z listy rozwijanej wybierz pozycję `Bing Search v7`.
3. Wprowadź wartość `Name` dla nowego zasobu.
4. Wybierz subskrypcję `Pay-As-You-Go`.
5. Wybierz warstwę cenową `S9`.
6. Kliknij pozycję `Enable`, aby rozpocząć subskrypcję.

## <a name="application-dependencies"></a>Zależności aplikacji
Aby skonfigurować aplikację konsoli przy użyciu zestawu Bing Web Search SDK, przejdź do opcji Zarządzaj pakietami NuGet w Eksploratorze rozwiązań programu Visual Studio. Dodaj pakiet Microsoft.Azure.CognitiveServices.Search.VisualSearch.

Zainstalowanie pakietu NuGet Web Search SDK spowoduje również zainstalowanie zależności, w tym:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="image-and-crop-area"></a>Obraz i obszar przycinania
Następujący obraz przedstawia zespół liderów wyższego szczebla firmy Microsoft.  Przy użyciu zestawu Visual Search SDK możemy przekazać obszar przycinania obrazu i znaleźć inne obrazy i strony internetowe, które zawierają obiekt znajdujący w wybranym obszarze większego obrazu.  W tym przypadku obiekt jest osobą.

![Zespół liderów wyższego szczebla firmy Microsoft](./media/MS_SrLeaders.jpg)

## <a name="specify-the-crop-area-as-imageinfo-in-visualsearchrequest"></a>Określenie obszaru przycinania jako elementu ImageInfo w żądaniu VisualSearchRequest
W tym przykładzie jest używany obszar przycinania poprzedniego obrazu, który określa współrzędne lewego górnego i prawego dolnego rogu za pomocą procentowej części całego obrazu.  Poniższy kod tworzy obiekt `ImageInfo` z obszaru przycinania i ładuje obiekt `ImageInfo` do żądania `VisualSearchRequest`.  Obiekt `ImageInfo` zawiera także adres URL obrazu online.

```
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg;
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```
## <a name="search-for-images-similar-to-crop-area"></a>Wyszukiwanie obrazów podobnych do obszaru przycinania
Żądanie `VisualSearchRequest` zawiera informacje o obszarze przycinania dotyczące obrazu i jego adresu URL.  Metoda `VisualSearchMethodAsync` pobiera wyniki.
```
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Pobieranie danych adresu URL z elementu ImageModuleAction
Wyniki wyszukiwania wizualnego to obiekty `ImageTag`.  Każdy tag zawiera listę obiektów `ImageAction`.  Każdy obiekt `ImageAction` zawiera pole `Data`, które jest listą wartości zależących od typu akcji:

Różne typy można uzyskać za pomocą następującego kodu:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
Kompletna aplikacja zwraca:

* ActionType: PagesIncluding WebSearchURL:
* ActionType: MoreSizes WebSearchURL:
* ActionType: VisualSearch WebSearchURL:
* ActionType: ImageById WebSearchURL: 
* ActionType: RelatedSearches  WebSearchURL:
* ActionType: Entity -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1
* ActionType: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1
* ActionType: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1

Jak pokazano na powyższej liście, element `Entity` `ActionType` zawiera zapytanie wyszukiwania Bing, które zwraca informacje na temat rozpoznawalnej osoby, miejsca lub rzeczy.  Typy `TopicResults` i `ImageResults` zawierają zapytania dotyczące powiązanych obrazów. Adresy URL na liście to linki do wyników wyszukiwania Bing.


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

## <a name="complete-code"></a>Kompletny kod

Poniższy kod umożliwia uruchomienie poprzednich przykładów. Wysyła on obraz w postaci binarnej w treści żądania POST wraz z obiektem cropArea i wyświetla adresy URL wyszukiwania Bing dla każdego elementu ActionType. Jeśli element ActionType to PagesIncluding, kod pobiera elementy ImageObject z obiektu ImageObject Data.  Element Data zawiera listę wartości, które są adresami URL obrazów na stronach internetowych.  Skopiuj i wklej wynikowe adresy URL wyszukiwania wizualnego w przeglądarce, aby wyświetlić wyniki. Skopiuj i wklej elementy ContentUrl w przeglądarce, aby wyświetlić obrazy.

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
## <a name="next-steps"></a>Następne kroki
[Odpowiedź wyszukiwania wizualnego](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview#the-response)