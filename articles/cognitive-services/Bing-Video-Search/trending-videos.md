---
title: Wyszukiwanie w sieci web dla trendów wideo | Dokumentacja firmy Microsoft
description: Przedstawia sposób użycia interfejsu API wyszukiwania usługi Bing wideo, aby wyszukać w sieci web trendów wideo.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 897A28A3-0980-484E-814F-FFE1D5C885E6
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 8db7fcf77042631260b4b165bd3d44053827f3ce
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346896"
---
# <a name="get-trending-videos"></a>Pobierz trendów wideo  

Aby uzyskać współczesnych trendów wideo, Wyślij następujące żądania GET:  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

Następujące rynkach obsługuje trendów wideo.  
 
-   EN-AU (angielski, Australia)  
-   EN-CA (angielski, Kanada)  
-   en-GB (angielski, Polska)  
-   EN-ID (angielski, Indonezji)  
-   EN-IE (w języku angielskim, Irlandii)  
-   EN-IN (angielski, Indie)  
-   NZ en (angielski, Nowa Zelandia)  
-   CIĄG en (angielski, Filipiny)  
-   EN-SG (angielski, Singapuru)  
-   EN US (angielski, Stany Zjednoczone)  
-   EN TT (angielski cały świat agregacji kodu)  
-   EN-ZA-(angielski, Republika Południowej Afryki)  
-   zh-CN (chińskim, Chiny)

  
W poniższym przykładzie przedstawiono odpowiedzi, która zawiera trendów wideo.  

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
Odpowiedź zawiera listę plików wideo według kategorii i podkategorii. Na przykład jeden z jej podkategorie jest górnej kategorii Muzyka, filmy zawarte na liście kategorii, można utworzyć kategorii Top muzyka wideo w środowiska użytkownika. Następnie można użyć `thumbnailUrl`, `displayText`, i `webSearchUrl` pola, aby utworzyć aktywne kafelka w każdej kategorii (na przykład pierwszych muzyka wideo). Po kliknięciu kafelka one jest przekierowanie do usługi Bing w przeglądarce wideo gdzie odtwarzania wideo.

Odpowiedź zawiera również transparent wideo, które są najbardziej popularnych wideo trendów. Filmy wideo transparent mogą pochodzić z jednego lub więcej kategorii.  
  
