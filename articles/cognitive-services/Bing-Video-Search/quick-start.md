---
title: Wideo interfejsu API Search szybki start | Dokumentacja firmy Microsoft
description: Pokazano, jak rozpocząć korzystanie z interfejsu API wyszukiwania usługi Bing wideo.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 7E59692A-83A8-4F4C-B122-1F0EDC8E5C86
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 0bd0f067d64cac3ebac342ebadcfcc010a47af7b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348981"
---
# <a name="your-first-video-search-query"></a>Pierwszego zapytania wyszukiwania wideo

Przed skonfigurowaniem pierwszego połączenia, należy uzyskać klucz subskrypcji usług kognitywnych wyszukiwania usługi Bing. Aby uzyskać klucz, zobacz [spróbuj kognitywnych usług](https://azure.microsoft.com/try/cognitive-services/?api=bing-video-search-api).

Aby uzyskać wyniki wyszukiwania wideo, może wysłać żądania GET do następujący punkt końcowy:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```
   
Żądanie musi używać protokołu HTTPS.

Zaleca się, że wszystkie żądania pochodzi z serwera. Dystrybucja klucza w ramach aplikacji klienta zawiera więcej możliwości złośliwego innej do niego dostęp. Ponadto nawiązywanie połączeń z serwerem zapewnia jeden punkt uaktualniania wersji interfejsu API.

  
Żądanie musi określać [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query) parametru zapytania, który zawiera użytkownika terminu wyszukiwania. Chociaż jest to opcjonalne, powinny również określać żądanie [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#mkt) parametru zapytania, który identyfikuje rynku miejscu wyniki pochodzą z. Aby uzyskać listę opcjonalne parametry zapytania takie jak `pricing`, zobacz [parametry zapytania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query-parameters). Wszystkie wartości parametrów zapytania musi być zakodowane w adresie URL.  
  
Żądanie musi określać [Ocp-Apim-subskrypcji — klucz](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#subscriptionkey) nagłówka. Mimo że jest to opcjonalne, zachęca się także określić następujące nagłówki:  
  
-   [Agent użytkownika](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#clientid)  
-   [ClientIP-X-wyszukiwania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#clientip)  
-   [X wyszukiwania lokalizacji](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#location)  

Nagłówki adresów IP i lokalizacji klienta są ważne dla zwracania zawartości pamiętać lokalizacji.  

Aby uzyskać listę wszystkich nagłówków żądań i odpowiedzi, zobacz [nagłówki](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#headers).


## <a name="the-request"></a>Żądanie

Poniżej przedstawiono żądania wyszukiwania, który zawiera wszystkie parametry sugerowane zapytania i nagłówków. Jeśli pierwsza wywoływania żadnego z interfejsów API Bing, nie dołączaj nagłówka Identyfikatora klienta. Identyfikator klienta należy uwzględnić tylko, jeśli został wcześniej wywołuje interfejs API Bing i Bing zwrócony identyfikator klienta dla użytkownika lub kombinacji urządzeń. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Poniżej przedstawiono odpowiedź na wcześniejsze żądanie. W przykładzie przedstawiono również nagłówki odpowiedzi specyficzne dla usługi Bing.

```
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D5694...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYWCvh...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjHZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56944...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjBZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y6...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E11E3CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        . . .
    ],
    "nextOffset" : 0,
    "pivotSuggestions" : [
        {
            "pivot" : "sailing",
            "suggestions" : []
        },
        {
            "pivot" : "dinghies",
            "suggestions" : [
                {
                    "text" : "Sailing Cruising",
                    "displayText" : "Cruising",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF754...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Sailing..."
                    }
                },
                . . .
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Kolejne kroki

Wypróbuj interfejsu API. Przejdź do [konsoli testowania wyszukiwania wideo interfejsu API](https://dev.cognitive.microsoft.com/docs/services/56b43f3ccf5ff8098cef3809/operations/58113fe5e31dac0a1ce6b0a8). 

Aby uzyskać więcej informacji o korzystanie z obiektów odpowiedzi, zobacz [wyszukiwanie w sieci Web wideo](./search-the-web.md).

Aby uzyskać informacje na temat uzyskiwania informacji na temat wideo, takich jak powiązane wyszukiwania, zobacz [Insights wideo](./video-insights.md).  
  
Aby uzyskać więcej informacji o pliki wideo, które są umożliwia analizę trendów na mediów społecznościowych, zobacz [umożliwia analizę trendów wideo](./trending-videos.md).  
