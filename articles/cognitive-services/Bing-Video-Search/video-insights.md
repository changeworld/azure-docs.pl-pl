---
title: Uzyskiwanie szczegółowych informacji wideo | Dokumentacja firmy Microsoft
description: Przedstawia sposób użycia interfejsu API Bing wideo wyszukiwania, aby uzyskać więcej informacji na temat wideo.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 30ECF4E2-E4F0-491B-9FA8-971BC96AB7B6
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 4e804f168307ca8f206152b11e59652497678e42
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348953"
---
# <a name="get-insights-about-a-video"></a>Uzyskiwanie szczegółowych informacji o wideo

Każdy plik wideo zawiera identyfikator wideo, którego można użyć, aby uzyskać więcej informacji na temat wideo, takich jak powiązane pliki wideo.  
  
Aby uzyskać szczegółowe informacje o pliku wideo, przechwytywania jego [videoId](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-videoid) tokenu w odpowiedzi. 

```json
    "value" : [
        {
            . . .
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear...",
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjHBZ--g",
            "videoId" : "6DB795E11A6E3CBAAD636DB795E11A6E3CBAAD63",
            . . .
        }
    ],
```

Następnie wysłać następujące żądania GET do punktu końcowego szczegóły wideo. Ustaw [identyfikator](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#id) parametr do zapytania `videoId` tokenu. Aby określić szczegółowych danych, które chcesz pobrać, ustaw [modułów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#modulesrequested) parametr zapytania. Aby uzyskać wszystkie szczegółowe informacje, należy ustawić `modules` do wszystkich. Odpowiedź zawiera wszystkie szczegółowe informacje, które są wymagane, jeśli jest dostępna.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details?q=sailiing+dinghies&id=6DB795E11A6E3CBAAD636DB795E11A6E3CBAAD63&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
``` 

## <a name="getting-related-videos-insights"></a>Uzyskiwanie szczegółowych informacji powiązanych wideo  

Aby pobrać pliki wideo, które są powiązane z określonym wideo, ustaw [modułów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#modulesrequested) parametr do RelatedVideos zapytania.
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details?q=sailiing+dinghies&id=6DB795E11A6E3CBAAD636DB795E11A6E3CBAAD63&modules=RelatedVideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  
  
Poniżej znajduje się odpowiedź na wcześniejsze żądanie. Obiekt najwyższego poziomu jest [VideoDetails](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videodetails) obiekt zamiast [wideo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) obiektu.  
  
```  
{
    "_type" : "Api.VideoDetails.VideoDetails",
    "relatedVideos" : {
        "value" : [
            {
                "name" : "How to sail - Reefing a Sail",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=7284B07...",
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OVP.zt...",
                "datePublished" : "2014-03-04T16:11:09",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=7284B07...",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?...",
                "duration" : "PT4M56S",
                "motionThumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OM...",
                "allowHttpsEmbed" : true,
                "viewCount" : 21756,
                "videoId" : "AC1A157A4DDB571D03D6AC157A4DDB571D03D6",
                "allowMobileEmbed" : false,
                "isSuperfresh" : false
            },
            . . .
        ]
    }
}
```