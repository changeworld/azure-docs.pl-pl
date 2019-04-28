---
title: Wyszukiwanie w sieci web dla popularnych klipów wideo przy użyciu interfejsu API wyszukiwania wideo Bing
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API wyszukiwania wideo Bing do wyszukiwania popularnych klipów wideo w sieci web.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 486cf2e3bcf851f23011bb2fb8d91691d6190698
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61431926"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Pobierz popularne wideo za pomocą interfejsu API wyszukiwania wideo Bing 

Interfejs API wyszukiwania wideo Bing umożliwia znalezienie współczesnych popularnych klipów wideo z, w sieci Web i w różnych kategoriach. 

## <a name="get-request"></a>Pobieranie żądania

Aby uzyskać bieżącą popularnych klipów wideo z interfejsu API wyszukiwania wideo Bing, wysyłanie następujące żądanie GET:  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>Obsługa obrotu

Następujące rynki obsługuje popularne wideo.  
 
-   EN-AU (angielski, Australia)  
-   EN-CA (angielski, Kanada)  
-   en-GB (angielski, Wielka Brytania)  
-   EN-ID (angielski, Indonezja)  
-   EN-IE (angielski, Irlandia)  
-   EN-IN (angielski, Indie)  
-   NZ en (angielski, Nowa Zelandia)  
-   PH en (angielski, Filipiny)  
-   SG en (angielski, Singapur)  
-   EN US (angielski, Stany Zjednoczone)  
-   EN-WW (angielski, cały świat agregacji kodu)  
-   ZA en (angielski, Republika Południowej Afryki)  
-   nazwy zh-CN (chińskim, Chiny)

## <a name="example-json-response"></a>Przykładowa odpowiedź JSON  

Poniższy przykład przedstawia odpowiedź interfejsu API, który zawiera popularnych klipów wideo, które są wymienione według kategorii i podkategorii. Odpowiedź zawiera również Baner wideo, które są najbardziej popularne popularnych klipów wideo i mogą pochodzić z jednej lub więcej kategorii.  

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

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Pobieranie szczegółowych informacji o wideo](video-insights.md)