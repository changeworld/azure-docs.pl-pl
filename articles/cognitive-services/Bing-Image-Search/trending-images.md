---
title: Wyszukiwanie w sieci web dla obrazów trendów | Dokumentacja firmy Microsoft
description: Przedstawia sposób użycia interfejsu API wyszukiwania usługi Bing obrazy, aby wyszukać w sieci web trendów obrazów.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: b12524cd4c1896501820209b3a45746b8f38b210
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346933"
---
# <a name="get-trending-images"></a>Pobierz trendów obrazów  

Aby uzyskać współczesnych trendów obrazów, Wyślij następujące żądania GET:  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Interfejs API umożliwia analizę trendów obrazy aktualnie obsługuje tylko następujące rynkach:  

- EN US (angielski, Stany Zjednoczone)  
- EN-CA (angielski, Kanada)  
- EN-AU (angielski, Australia)  
- zh-CN (chińskim, Chiny)

Odpowiedź zawiera [TrendingImages](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#trendingimages) obiekt, który zawiera obrazy według kategorii. Kategoria `title` do grupowania obrazy środowiska użytkownika. Kategorie mogą ulec zmianie codziennie.  
  
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
  
Każdego kafelka zawiera obraz i opcje uzyskania powiązanych obrazy. Aby uzyskać powiązanych obrazy, można użyć zapytania `text` do wywołania [obraz wyszukiwania API](./search-the-web.md) i wyświetlanie powiązanych obrazy samodzielnie. Można użyć adresu URL na liście `webSearchUrl` przenieść użytkownika do strony wyników wyszukiwania obrazy Bing firmy, który zawiera powiązanych obrazy. 

Wywołanie interfejsu API Search obrazu można pobrać powiązanych obrazy ustawić [identyfikator](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#id) parametr do Identyfikatora w zapytania `id` pola. Określanie Identyfikatora zapewnia odpowiedź zawiera obraz (jest pierwszy obraz w odpowiedzi) i powiązanych obrazy. Ponadto należy ustawić [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#q) parametr w tekście zapytania `query` obiektu `text` pola.

Poniższy przykład przedstawia użycie identyfikator obrazu można pobrać powiązanych obrazy Smith p. w poprzedniej odpowiedzi API umożliwia analizę trendów obrazów.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  
