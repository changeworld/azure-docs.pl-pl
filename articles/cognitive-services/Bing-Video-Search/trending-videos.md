---
title: Wyszukiwanie w internecie popularnych klipów wideo za pomocą interfejsu API wyszukiwania wideo usługi Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API wyszukiwania wideo Bing do wyszukiwania w internecie popularnych klipów wideo.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: ea6f79bf6c305ecc07b3e684ede15ad439039e7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68500614"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Uzyskaj popularne filmy za pomocą interfejsu API wyszukiwania wideo usługi Bing 

Interfejs API wyszukiwania wideo Bing umożliwia znajdowanie popularnych klipów wideo z całej sieci i w różnych kategoriach. 

## <a name="get-request"></a>Wniosek GET

Aby uzyskać dzisiejsze popularne filmy z interfejsu API wyszukiwania wideo Bing, wyślij następujące żądanie GET:  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>Wsparcie rynku

Poniższe rynki obsługują popularne filmy.  
 
-   en-AU (angielski, Australia)  
-   en-CA (angielski, Kanada)  
-   pl-PL (angielski, Wielka Brytania)  
-   en-ID (angielski, Indonezja)  
-   en-IE (angielski, Irlandia)  
-   pl-IN (angielski, Indie)  
-   pl-NZ (angielski, Nowa Zelandia)  
-   en-PH (angielski, Filipiny)  
-   en-SG (angielski, Singapur)  
-   pl-US (angielski, Stany Zjednoczone)  
-   en-WW (angielski, światowy kod zbiorczy)  
-   en-ZA (angielski, Republika Południowej Afryki)  
-   zh-CN (chiński, Chiny)

## <a name="example-json-response"></a>Przykładowa odpowiedź JSON  

W poniższym przykładzie przedstawiono odpowiedź interfejsu API zawierającą popularne filmy wideo, które są wyświetlane według kategorii i podkategorii. Odpowiedź zawiera również banery wideo, które są najpopularniejszymi popularnymi filmami i mogą pochodzić z jednej lub więcej kategorii.  

```json
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Pobieranie szczegółowych informacji o wideo](video-insights.md)