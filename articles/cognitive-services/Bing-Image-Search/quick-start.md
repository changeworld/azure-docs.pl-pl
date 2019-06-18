---
title: Przewodnik Szybki start interfejs API wyszukiwania obrazów | Dokumentacja firmy Microsoft
description: Pokazuje, jak rozpocząć korzystanie z interfejsu API wyszukiwania obrazów Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: BE2B2F8C-20B5-4E0B-AEC8-EAD505BA4C85
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 03/04/2019
ms.author: scottwhi
ms.openlocfilehash: 006b611bd2dcc94b1b651de7789e165a5c45a17b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66383776"
---
# <a name="your-first-images-search-query"></a>Zapytanie wyszukiwania dla pierwszego obrazów

Przed wykonaniem pierwszego wywołania należy uzyskać klucz subskrypcji usług Cognitive Services wyszukiwania Bing. Aby uzyskać klucz, zobacz [Wypróbuj usługi Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api).  Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Aby uzyskać wyniki wyszukiwania obrazów, czy Wyślij żądanie Pobierz do następujący punkt końcowy:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search
```
  
Żądanie musi używać protokołu HTTPS.

Zalecamy, aby wszystkie żądania pochodziły z serwera. Dystrybuowanie klucza w ramach aplikacji klienckiej dostarcza więcej okazji do przejęcia go przez złośliwy kod innych firm. Ponadto wykonywanie wywołań z serwera zapewnia jeden punkt uaktualniania dla przyszłych wersji interfejsu API.

Żądanie musi określać parametr zapytania [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query), który zawiera wyszukiwany termin użytkownika. Chociaż jest to opcjonalne, żądanie powinno również określać parametr zapytania [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#mkt), który identyfikuje rynek, z którego mają pochodzić wyniki. Aby uzyskać listę opcjonalnych parametrów zapytania, takich jak `freshness` czy `size`, zobacz [Query Parameters (Parametry zapytania)](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query-parameters). Wszystkie wartości parametrów zapytania muszą być zakodowane w adresie URL.  
  
Żądanie musi określać nagłówek [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#subscriptionkey). Mimo że jest to opcjonalne, zachęcamy, aby określić również następujące nagłówki:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#location)  

Nagłówki adresu IP klienta i lokalizacji są ważne z punktu widzenia zwracania zawartości odpowiedniej dla danej lokalizacji.  

Aby uzyskać listę wszystkich nagłówków żądań i odpowiedzi, zobacz [Nagłówki](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#headers).

## <a name="the-request"></a>Żądanie

Poniżej przedstawiono żądanie wyszukiwania, które zawiera wszystkie sugerowane parametry zapytania i nagłówki. Jeśli jest to pierwsze czas wywoływania dowolnych interfejsów API usługi Bing, nie dołączaj nagłówek identyfikator klienta. Identyfikator klienta należy uwzględnić tylko wtedy, gdy interfejs API Bing został już wywołany i usługa Bing zwróciła identyfikator klienta dla kombinacji użytkownika i urządzenia. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Poniżej przedstawiono odpowiedź na poprzednie żądanie.

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

Wypróbuj interfejs API. Przejdź do [konsoli testowanie interfejsu API wyszukiwania obrazów](https://dev.cognitive.microsoft.com/docs/services/8336afba49a84475ba401758c0dbf749/operations/571fab09dbe2d933e891028f). 

Aby uzyskać szczegółowe informacje dotyczące korzystania z obiektów odpowiedzi, zobacz [Wyszukiwanie w sieci Web](./search-the-web.md).

Aby uzyskać szczegółowe informacje dotyczące uzyskiwania szczegółowych informacji o pliku obrazu, takich jak strony sieci web, które zawierają obraz lub osób, które zostały rozpoznane w obrazie, zobacz [szczegółowe informacje o obrazach](./image-insights.md).  
  
Aby uzyskać szczegółowe informacje o obrazach, które stają się popularne w mediach społecznościowych, zobacz [popularne obrazy](./trending-images.md).  
