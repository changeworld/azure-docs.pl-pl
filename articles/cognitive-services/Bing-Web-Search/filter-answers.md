---
title: Filtrowanie odpowiedzi sieci web, które zwraca Bing | Dokumentacja firmy Microsoft
description: Pokazuje, jak używać responseFilter do filtrowania odpowiedzi, które zwraca interfejs API wyszukiwania usługi Bing sieci Web.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 01/12/2017
ms.author: scottwhi
ms.openlocfilehash: a5ee6241630ee24a05c2c4b932453bd7946a7508
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347884"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtrowanie odpowiedzi, które obejmuje odpowiedzi wyszukiwania  

Kwerendę sieci web Bing zwraca całej zawartości, które uważa ma zastosowanie do wyszukiwania. Na przykład jeśli zapytanie wyszukiwania jest "dinghies wypływających +", odpowiedzi może zawierać następujące odpowiedzi:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```

Jeśli interesuje Cię określonych typów zawartości, np. obrazów, klipów wideo i wiadomości, można zażądać tylko tych odpowiedzi przy użyciu [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#responsefilter) parametr zapytania. Jeśli Bing umożliwia znalezienie odpowiedniej zawartości dla określonej odpowiedzi, Bing, zwraca go. Filtr odpowiedzi jest rozdzielana przecinkami lista odpowiedzi. Poniżej pokazano, jak używać `responseFilter` żądania obrazów, klipów wideo i wiadomości dinghies prowadzenia. Kodowanie ciągu zapytania, przecinki zmienia na %2, C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Poniżej przedstawiono odpowiedzi na poprzednie zapytanie. Jak widać Bing nie znaleziono odpowiednich wyników wiadomości i wideo, aby odpowiedź nie zawiera ich.

```json
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            }]
        }
    }
}
```

Chociaż Bing nie zwrócił wyników wiadomości i wideo w poprzedniej odpowiedzi, nie oznacza to, że zawartości wideo i wiadomości nie istnieje. To po prostu strony nie włączono je. Jednak jeśli użytkownik [strony](./paging-webpages.md) za pośrednictwem więcej wyników kolejnych stronach prawdopodobnie obejmuje je. Ponadto można wywołać [interfejsu API Search wideo](../bing-video-search/search-the-web.md) i [API wyszukiwania wiadomości](../bing-news-search/search-the-web.md) punktów końcowych w bezpośrednio, odpowiedzi prawdopodobnie będzie zawierał wyniki. 

Jesteś odradzane ze przy użyciu `responseFilter` można pobrać wyników z jednego interfejsu API. Jeśli chcesz zawartości z jednego interfejsu API Bing bezpośrednio wywołać tego interfejsu API. Na przykład, aby otrzymywać tylko obrazy, Wyślij żądanie do punktu końcowego interfejsu API Search obrazu `https://api.cognitive.microsoft.com/bing/v7.0/images/search` lub jednego z innych [obrazów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#endpoints) punktów końcowych. Wywoływanie jednego interfejsu API jest ważny nie tylko ze względu na wydajność, ale ponieważ interfejsy API specyficzne dla zawartości oferuje rozbudowane wyników. Na przykład można użyć filtrów, które nie są dostępne w sieci Web API wyszukiwania do filtrowania wyników.  
  
Aby uzyskać wyniki wyszukiwania z określonej domeny, obejmują `site:` — operator zapytań w ciągu zapytania.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE] 
> W zależności od tego zapytania, jeśli używasz `site:` — operator zapytań, istnieje ryzyko, że odpowiedź może zawierać zawartość dla dorosłych niezależnie od tego [bezpieczne wyszukiwanie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#safesearch) ustawienie. Należy używać `site:` tylko wtedy, gdy masz świadomość zawartości w witrynie i scenariusz obsługuje możliwości zawartość dla dorosłych. 
  
## <a name="limiting-the-number-of-answers-in-the-response"></a>Ograniczenie liczby odpowiedzi w odpowiedzi

Bing zawiera odpowiedzi w odpowiedzi oparte na klasyfikacji. Na przykład, jeśli kwerenda *wypływających + dinghies*, zwraca Bing `webpages`, `images`, `videos`, i `relatedSearches`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Aby ograniczyć liczbę odpowiedzi tego Bing zwraca odpowiedzi dwóch pierwszych stron sieci Web i obrazów, zestaw [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) parametr 2 zapytania. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Odpowiedź zawiera tylko `webPages` i `images`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

Jeśli dodasz `responseFilter` parametr poprzednie zapytanie i zestaw stron sieci Web i grup dyskusyjnych, odpowiedzi zawiera tylko stron sieci Web ponieważ wiadomości nie jest wyznaczona ranga zapytania.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>Wspieranie odpowiedzi, które nie są klasyfikowane

W przypadku stron sieci Web, obrazów, klipów wideo i relatedSearches górnej uszeregowane odpowiedzi, które zwraca Bing dla zapytania odpowiedź będzie zawierać tych odpowiedzi. Jeśli ustawisz [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) do dwóch (2), Bing zwraca górny dwie odpowiedzi uporządkowanej według rangi: stron sieci Web i obrazów. Jeśli Bing do uwzględnienia w odpowiedzi obrazów i plików wideo, należy określić [podwyższyć poziom](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#promote) parametr zapytania i ustaw ją na obrazy i wideo. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Poniżej przedstawiono odpowiedzi na żądanie powyżej. Bing zwraca górny dwie odpowiedzi, stron sieci Web i obrazy i zamienia wideo do odpowiedzi.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Jeśli ustawisz `promote` do wiadomości, odpowiedź nie zawiera wiadomości odpowiedzi, ponieważ nie jest uporządkowanej według rangi odpowiedzi&mdash;możesz podwyższyć poziom tylko uszeregowane odpowiedzi.

Nie są uwzględniane odpowiedzi, które chcesz podwyższyć `answerCount` limit. Na przykład, jeśli uporządkowanej według rangi odpowiedzi są wiadomości, obrazy i klipów wideo i ustawisz `answerCount` 1 i `promote` wiadomości odpowiedzi zawiera wiadomości i obrazów. Lub, w przypadku plików wideo, obrazy i wiadomości odpowiedzi uporządkowanej według rangi odpowiedzi zawiera pliki wideo i wiadomości.

Możesz użyć `promote` tylko w przypadku określenia `answerCount` parametr zapytania.
