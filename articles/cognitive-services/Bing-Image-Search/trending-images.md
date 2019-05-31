---
title: Pobierz popularne obrazów za pomocą interfejsu API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Wyszukiwania obecnie popularnych obrazów z sieci web za pomocą interfejsu API wyszukiwania obrazów Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 2936b94d7ba791b1a4e5a9b95aca3ca3ecdb5904
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383436"
---
# <a name="get-trending-images-from-the-web"></a>Pobierz popularnych obrazów z sieci web

Aby uzyskać bieżącą popularnych obrazów, Wyślij następujące żądanie GET:  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Interfejs API obrazów na popularności obecnie obsługuje tylko następujące rynki:  

- EN US (angielski, Stany Zjednoczone)  
- EN-CA (angielski, Kanada)  
- EN-AU (angielski, Australia)  
- nazwy zh-CN (chińskim, Chiny)

Odpowiedź zawiera [TrendingImages](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#trendingimages) obiekt, który wyświetla listę obrazów, według kategorii. Kategoria `title` do grupowania obrazów w środowiska użytkownika. Kategorie może się zmieniać codziennie.  

```json
{
    "_type" : "TrendingImages",  
    "categories" : [{  
        "title" : "Popular people searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Smith",  
                "displayText" : "Mr. Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=smith&FORM=..."
            },  
            "image" : {  
                "id" : "C3C60AE779A054D5CD80D3CACF0F3",  
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OIP.M2532...",  
                "contentUrl" : "http:\/\/www.contoso.com.au\/assets\/Uploads\/smith-SH01.jpg",  
                "thumbnail" : {  
                    "width" : 288,  
                    "height" : 300  
                }  
            }  
        },  
        . . .  
        ]  
    },  
    . . .  
    {  
        "title" : "Popular Halloween searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Halloween costumes for adults",  
                "displayText" : "Halloween costumes for adults",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Halloween+costumes..."
            },  
            "image" : {  
                "id" : "0F3395F2983003F89DCEE711B55D7FA53E4",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP.Me429c...",  
                "contentUrl" : "http:\/\/images.domain.com\/products\/8179\/1-1\/adult-squirrel...",  
                "thumbnail" : {  
                    "width" : 336,  
                    "height" : 480  
                }  
            }  
        }]  
    }]  
}  
```  

Każdy Kafelek zawiera obraz i opcje uzyskania powiązane obrazów. Aby uzyskać powiązane obrazów, można użyć zapytania `text` do wywołania [interfejsu API wyszukiwania obrazów](./search-the-web.md) i obrazy będą wyświetlane powiązane samodzielnie. Alternatywnie można użyć adresu URL w `webSearchUrl` do wykonania użytkownika do strony wyników wyszukiwania obrazów Bing, który zawiera powiązane obrazów.

Ustaw wywołanie interfejsu API wyszukiwania obrazów, aby uzyskać powiązane obrazy [identyfikator](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#id) parametr do Identyfikatora w zapytania `id` pola. Określając identyfikator zapewnia, że odpowiedź zawiera obraz (jest to pierwszy obraz w odpowiedzi) i jego obrazy powiązane. Ponadto należy ustawić [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) parametr w tekście zapytania `query` obiektu `text` pola.

Poniższy przykład pokazuje, jak korzystać identyfikator obrazu odwzorowań Pan Kowalski w poprzedniej odpowiedzi na popularności API obrazów.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  
