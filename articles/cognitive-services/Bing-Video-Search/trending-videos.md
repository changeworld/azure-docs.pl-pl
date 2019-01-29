---
title: Wyszukiwanie w sieci web dla popularnych klipów wideo — wyszukiwania wideo Bing
titlesuffix: Azure Cognitive Services
description: Pokazuje, jak używać interfejsu API wyszukiwania wideo Bing do wyszukiwania popularnych klipów wideo w sieci web.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 71dd888199e9e630835c4916d35f6308431bab62
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203559"
---
# <a name="get-trending-videos"></a>Pobieranie popularnych wideo  

Aby uzyskać bieżącą popularnych klipów wideo, Wyślij następujące żądanie GET:  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

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

  
Poniższy przykład przedstawia odpowiedź, która zawiera popularnych klipów wideo.  

```  
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
Odpowiedź zawiera listę wideo według kategorii i podkategorii. Na przykład jeśli jeden z jej podkategorie był górnej kategorii Muzyka, filmy wideo znajdujących się na liście kategorii, można utworzyć kategorii górnej muzyczne, filmy wideo w środowiska użytkownika. Następnie można użyć `thumbnailUrl`, `displayText`, i `webSearchUrl` pól w celu utworzenia możesz klikać kafelka w każdej kategorii (na przykład pierwszych muzyczne filmy wideo). Po kliknięciu kafelka one nastąpi przekierowanie do przeglądarki wideo Bing gdzie jest odtwarzany film wideo.

Odpowiedź zawiera również Baner wideo, które są najbardziej popularne popularnych klipów wideo. Transparent filmy wideo mogą pochodzić z jednego lub więcej kategorii.  
  
