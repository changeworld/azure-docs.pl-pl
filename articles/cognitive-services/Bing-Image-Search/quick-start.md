---
title: Obrazy interfejsu API Search szybki start | Dokumentacja firmy Microsoft
description: Pokazano, jak rozpocząć korzystanie z interfejsu API wyszukiwania usługi Bing obrazów.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: BE2B2F8C-20B5-4E0B-AEC8-EAD505BA4C85
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 9e211cf5acd17ab80948d0b7161bdd2a9220c4a6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347032"
---
# <a name="your-first-images-search-query"></a>Zapytania wyszukiwania pierwszy obrazów

Przed skonfigurowaniem pierwszego połączenia, należy uzyskać klucz subskrypcji usług kognitywnych wyszukiwania usługi Bing. Aby uzyskać klucz, zobacz [spróbuj kognitywnych usług](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api).

Aby uzyskać wyniki wyszukiwania obrazu, może wysłać żądania GET do następujący punkt końcowy:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search
```
  
Żądanie musi używać protokołu HTTPS.

Zaleca się, że wszystkie żądania pochodzi z serwera. Dystrybucja klucza w ramach aplikacji klienta zawiera więcej możliwości złośliwego innej do niego dostęp. Ponadto nawiązywanie połączeń z serwerem zapewnia jeden punkt uaktualniania wersji interfejsu API.

Żądanie musi określać [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) parametru zapytania, który zawiera użytkownika terminu wyszukiwania. Chociaż jest to opcjonalne, powinny również określać żądanie [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#mkt) parametru zapytania, który identyfikuje rynku miejscu wyniki pochodzą z. Aby uzyskać listę opcjonalne parametry zapytania takie jak `freshness` i `size`, zobacz [parametry zapytania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query-parameters). Wszystkie wartości parametrów zapytania musi być zakodowane w adresie URL.  
  
Żądanie musi określać [Ocp-Apim-subskrypcji — klucz](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#subscriptionkey) nagłówka. Mimo że jest to opcjonalne, zachęca się także określić następujące nagłówki:  
  
-   [Agent użytkownika](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#clientid)  
-   [ClientIP-X-wyszukiwania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#clientip)  
-   [X wyszukiwania lokalizacji](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#location)  

Nagłówki adresów IP i lokalizacji klienta są ważne dla zwracania zawartości pamiętać lokalizacji.  

Aby uzyskać listę wszystkich nagłówków żądań i odpowiedzi, zobacz [nagłówki](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#headers).

## <a name="the-request"></a>Żądanie

Poniżej przedstawiono żądania wyszukiwania, który zawiera wszystkie parametry sugerowane zapytania i nagłówków. Jeśli jest to Twoja pierwsza czas wywoływania żadnego z interfejsów API Bing, nie dołączaj nagłówka Identyfikatora klienta. Identyfikator klienta należy uwzględnić tylko, jeśli został wcześniej wywołuje interfejs API Bing i Bing zwrócony identyfikator klienta dla użytkownika lub kombinacji urządzeń. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Poniżej przedstawiono odpowiedź na wcześniejsze żądanie.

```json
{
    "_type" : "Images",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "totalEstimatedMatches" : 838,
    "value" : [
        {
            "name" : "File:Rich Passage Minto Sailing Dinghy.jpg - Wikipedia",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
            "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
            "datePublished" : "2011-10-29T11:26:00",
            "contentUrl" : "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
            "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
            "contentSize" : "79239 B",
            "encodingFormat" : "jpeg",
            "hostPageDisplayUrl" : "en.wikipedia.org\/wiki\/File:Rich_Passage...",
            "width" : 526,
            "height" : 688,
            "thumbnail" : {
                "width" : 229,
                "height" : 300
            },
            "imageInsightsToken" : "ccid_GNarK7ma*mid_CCF85447ADA6...",
            "insightsSourcesSummary" : {
                "shoppingSourcesCount" : 0,
                "recipeSourcesCount" : 0
            },
            "imageId" : "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
            "accentColor" : "376094"
        },
        . . .
    ],
    "queryExpansions" : [
        {
            "text" : "Small Sailing Dinghies",
            "displayText" : "Small",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8B4...",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=..."
            }
        },
        . . .
    ],
    "nextOffset" : 10,
    "pivotSuggestions" : [
        {
            "pivot" : "sailing",
            "suggestions" : []
        },
        {
            "pivot" : "dinghies",
            "suggestions" : [
                {
                    "text" : "Sailing Tender",
                    "displayText" : "Tender",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=..."
                    }
                },
                . . .
            ]
        }
    ],
    "displayShoppingSourcesBadges" : false,
    "displayRecipeSourcesBadges" : true
}
```

## <a name="next-steps"></a>Kolejne kroki

Wypróbuj interfejsu API. Przejdź do [obrazu konsoli testowania wyszukiwania interfejsu API](https://dev.cognitive.microsoft.com/docs/services/8336afba49a84475ba401758c0dbf749/operations/571fab09dbe2d933e891028f). 

Aby uzyskać więcej informacji o korzystanie z obiektów odpowiedzi, zobacz [wyszukiwanie w sieci Web](./search-the-web.md).

Aby uzyskać informacje na temat uzyskiwania informacji na temat obrazów, takich jak strony sieci web, które obejmują obrazu lub osoby, które zostały rozpoznane w obrazie, zobacz [Insights obrazu](./image-insights.md).  
  
Aby uzyskać więcej informacji o obrazach, które są umożliwia analizę trendów na mediów społecznościowych, zobacz [umożliwia analizę trendów obrazów](./trending-images.md).  
