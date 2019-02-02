---
title: Wyszukiwanie w sieci web dla popularnych klipów wideo przy użyciu interfejsu API wyszukiwania wideo Bing
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API wyszukiwania wideo Bing do wyszukiwania popularnych klipów wideo w sieci web.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 55f29a31a9574c7c32e6708565e961950520593f
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2019
ms.locfileid: "55566104"
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
[Uzyskaj szczegółowe dane wideo](video-insights.md)