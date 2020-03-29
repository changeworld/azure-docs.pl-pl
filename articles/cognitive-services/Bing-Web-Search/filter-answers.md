---
title: Jak filtrować wyniki wyszukiwania - Interfejs API wyszukiwania w sieci Bing
titleSuffix: Azure Cognitive Services
description: Typy odpowiedzi, które bing zawiera w odpowiedzi (na przykład obrazy, filmy i wiadomości) przy użyciu parametru zapytania "responseFilter".
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: scottwhi
ms.openlocfilehash: 6fa022f181e2061c6a7f3e08d1f2f501ddd9cac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220271"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtrowanie odpowiedzi, które zawiera odpowiedź wyszukiwania  

Podczas kwerendy sieci Web Bing zwraca wszystkie istotne treści, które znajdzie dla wyszukiwania. Na przykład, jeśli zapytanie wyszukiwania jest "żeglarstwo + pontony", odpowiedź może zawierać następujące odpowiedzi:

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

## <a name="query-parameters"></a>Parametry zapytania

Aby filtrować odpowiedzi zwracane przez bing, należy użyć poniższych parametrów zapytania podczas wywoływania interfejsu API.  

### <a name="responsefilter"></a>Filtr odpowiedzi

Można filtrować typy odpowiedzi, które Bing zawiera w odpowiedzi (na przykład obrazy, filmy i wiadomości) przy użyciu [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) query parametr, który jest rozdzielane przecinkami listy odpowiedzi. Odpowiedź zostanie uwzględniona w odpowiedzi, jeśli usługa Bing znajdzie dla niej odpowiednią zawartość. 

Aby wykluczyć określone odpowiedzi z odpowiedzi, takie `-` jak obrazy, należy dołączyć znak do typu odpowiedzi. Przykład:

```
&responseFilter=-images,-videos
```

Poniżej przedstawiono, `responseFilter` jak używać do żądania obrazów, filmów i wiadomości o pontonach żeglarskich. Podczas kodowania ciągu zapytania przecinki zmieniają się na %2C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Poniżej przedstawiono odpowiedź na poprzednie zapytanie. Ponieważ usługa Bing nie znalazła odpowiednich wyników wideo i wiadomości, odpowiedź nie zawiera ich.

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

Chociaż usługa Bing nie zwróciła wyników wideo i wiadomości w poprzedniej odpowiedzi, nie oznacza to, że treści wideo i wiadomości nie istnieją. Oznacza to po prostu, że strona ich nie zawierała. Jeśli jednak [przeglądasz](./paging-webpages.md) więcej wyników, kolejne strony prawdopodobnie je uwzględnią. Ponadto jeśli wywołasz bezpośrednio punkty końcowe [interfejsu API wyszukiwania wideo](../bing-video-search/search-the-web.md) i interfejsu API wyszukiwania [wiadomości,](../bing-news-search/search-the-web.md) odpowiedź prawdopodobnie będzie zawierać wyniki.

Nie można użyć `responseFilter` do uzyskania wyników z jednego interfejsu API. Jeśli chcesz zawartość z jednego interfejsu API Bing, wywołać ten interfejs API bezpośrednio. Na przykład, aby odbierać tylko obrazy, wyślij żądanie `https://api.cognitive.microsoft.com/bing/v7.0/images/search` do punktu końcowego interfejsu API wyszukiwania obrazów lub jednego z innych punktów końcowych [obrazy.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints) Wywoływanie pojedynczego interfejsu API jest ważne nie tylko ze względu na wydajność, ale dlatego, że interfejsy API specyficzne dla zawartości oferują bogatsze wyniki. Na przykład można użyć filtrów, które nie są dostępne dla interfejsu API wyszukiwania w sieci Web, aby filtrować wyniki.  

### <a name="site"></a>Lokacja

Aby uzyskać wyniki wyszukiwania z określonej domeny, należy uwzględnić `site:` parametr zapytania w ciągu zapytania.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> W zależności od kwerendy, `site:` jeśli używasz operatora kwerendy, istnieje prawdopodobieństwo, że odpowiedź może zawierać zawartość dla dorosłych, niezależnie od [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) ustawienie. Operatora `site:` używaj tylko wtedy, gdy znasz zawartość witryny i w swoim scenariuszu uwzględniasz możliwość pojawienia się zawartości dla dorosłych.

### <a name="freshness"></a>Świeżość

Aby ograniczyć wyniki odpowiedzi sieci Web do stron sieci Web wykrytych przez bing w określonym okresie, należy ustawić parametr zapytania [świeżości](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) na jedną z następujących wartości bez uwzględniania wielkości liter:

* `Day`— Zwracanie stron internetowych odkrytych przez Bing w ciągu ostatnich 24 godzin
* `Week`— Zwracanie stron internetowych odkrytych przez Bing w ciągu ostatnich 7 dni
* `Month`— Zwracanie stron internetowych odkrytych w ciągu ostatnich 30 dni

Można również ustawić ten parametr na niestandardowy `YYYY-MM-DD..YYYY-MM-DD`zakres dat w formularzu, . 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

Aby ograniczyć wyniki do jednej daty, ustaw parametr świeżości na określoną datę:

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

Wyniki mogą obejmować strony sieci Web, które nie są w określonym okresie, jeśli liczba stron sieci Web zgodnych z kryteriami filtrowania jest mniejsza niż liczba żądanych stron sieci Web (lub domyślny numer zwracany przez bing).

## <a name="limiting-the-number-of-answers-in-the-response"></a>Ograniczenie liczby odpowiedzi w odpowiedzi

Bing może zwracać wiele typów odpowiedzi w odpowiedzi JSON. Na przykład, jeśli zapytanie *sailing + pontony*, Bing może zwrócić `webpages`, `images`, `videos`i `relatedSearches`.

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

Aby ograniczyć liczbę odpowiedzi zwracanych przez bing do dwóch pierwszych odpowiedzi (stron sieci Web i obrazów), należy ustawić parametr zapytania [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) na 2.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Odpowiedź zawiera `webPages` tylko `images`i .

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

Jeśli dodasz `responseFilter` parametr kwerendy do poprzedniej kwerendy i ustawisz go na stronach sieci Web i wiadomości, odpowiedź zawiera tylko strony sieci Web, ponieważ wiadomości nie są klasyfikowane.

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

## <a name="promoting-answers-that-are-not-ranked"></a>Promowanie odpowiedzi, które nie są klasyfikowane

Jeśli najwyżej sklasyfikowane odpowiedzi zwracane przez bing dla zapytania są strony sieci Web, obrazy, filmy i relatedSearches, odpowiedź będzie zawierać te odpowiedzi. Jeśli ustawisz [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) do dwóch (2), Bing zwraca dwie najlepsze odpowiedzi rankingowe: strony sieci Web i obrazy. Jeśli chcesz, aby usługa Bing uwzględniała obrazy i klipy wideo w odpowiedzi, określ parametr [podwyższania poziomu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) zapytania i ustaw go na obrazy i filmy.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Poniżej znajduje się odpowiedź na powyższe żądanie. Bing zwraca dwie najlepsze odpowiedzi, strony internetowe i obrazy oraz promuje filmy do odpowiedzi.

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

Jeśli ustawisz `promote` się na wiadomości, odpowiedź nie zawiera odpowiedzi na&mdash;wiadomości, ponieważ nie jest to odpowiedź rankingowa, którą możesz promować tylko odpowiedzi rankingowe.

Odpowiedzi, które chcesz promować, nie są `answerCount` wliczane do limitu. Jeśli na przykład odpowiedziami rankingowymi są wiadomości, obrazy i `answerCount` filmy, `promote` a w odpowiedzi są ustawione na 1 i do wiadomości, odpowiedź zawiera wiadomości i obrazy. Lub, jeśli odpowiedzi rankingowe są filmy, obrazy i wiadomości, odpowiedź zawiera filmy i wiadomości.

Można użyć `promote` tylko wtedy, `answerCount` gdy określisz parametr kwerendy.
