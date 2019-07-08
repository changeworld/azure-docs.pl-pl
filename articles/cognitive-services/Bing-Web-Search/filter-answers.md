---
title: Jak filtrować wyniki wyszukiwania - API wyszukiwania w Internecie Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak filtrować i wyświetlać wyniki wyszukiwania z API wyszukiwania w Internecie Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: scottwhi
ms.openlocfilehash: a89d73b63680415aa8e516926b8e1d6c59ffbbad
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626020"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtrowanie odpowiedzi, które obejmuje odpowiedzi wyszukiwania  

Kiedy wykonujesz zapytanie o sieci web, Wyszukiwarka Bing zwróci wszystkie znalezione dla wyszukiwania odpowiedniej zawartości. Na przykład jeśli zapytanie wyszukiwania jest "dinghies pływających +", odpowiedź może zawierać następujące odpowiedzi:

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

Aby filtrować odpowiedzi zwrócony przez usługę Bing, należy użyć poniższych parametrów zapytania podczas wywoływania interfejsu API.  

### <a name="responsefilter"></a>ResponseFilter

Można filtrować typów pytań, które Bing dołącza do odpowiedzi (na przykład obrazy, wideo i wiadomości) przy użyciu [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) parametr zapytania, który jest rozdzielana przecinkami lista odpowiedzi. Odpowiedzi będą uwzględniane w odpowiedzi, jeśli Bing Znajdowanie odpowiedniej zawartości. 

Aby wykluczyć określone odpowiedzi z odpowiedzi, taką jak obrazy, należy poprzedzić `-` znaków na typ odpowiedzi. Na przykład:

```
&responseFilter=-images,-videos
```

Poniżej pokazano sposób użycia `responseFilter` żądania obrazów, filmów wideo i wiadomości dinghies prowadzenia. Kodowanie ciągu zapytania przecinki zmienia na %2 C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Poniżej przedstawiono odpowiedź na poprzednie zapytanie. Ponieważ Bing nie znaleziono odpowiednich wideo i wiadomości wyniki, odpowiedź nie zawiera ich.

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

Chociaż Bing nie zwrócił wyników wideo i wiadomości w poprzedniej odpowiedzi, nie oznacza to, że zawartości wideo i wiadomości nie istnieje. Oznacza to po prostu strony nie obejmują ich. Jednak jeśli użytkownik [strony](./paging-webpages.md) za pośrednictwem więcej wyników kolejnych stronach będzie prawdopodobnie je uwzględnić. Ponadto jeśli wywołasz [interfejsu API wyszukiwania wideo](../bing-video-search/search-the-web.md) i [interfejsu API wyszukiwania wiadomości](../bing-news-search/search-the-web.md) punktów końcowych w bezpośrednio odpowiedzi prawdopodobnie będzie zawierać wyniki.

Odradza się przy użyciu się `responseFilter` można pobrać wyniki z jednego interfejsu API. Jeśli chcesz, aby zawartość z jednego interfejsu API Bing, bezpośrednio wywołać tego interfejsu API. Na przykład, aby móc odbierać tylko obrazy, Wyślij żądanie do punktu końcowego interfejsu API wyszukiwania obrazów `https://api.cognitive.microsoft.com/bing/v7.0/images/search` lub jednego z innych [obrazów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints) punktów końcowych. Wywoływanie jednego interfejsu API są ważne nie tylko ze względu na wydajność, ale ponieważ interfejsy API specyficzne dla zawartości oferują bogatszy wyników. Na przykład można użyć filtrów, które nie są dostępne do interfejsu API wyszukiwania w sieci Web do filtrowania wyników.  

### <a name="site"></a>Witryny

Aby uzyskać wyniki wyszukiwania z określonej domeny, należy dołączyć `site:` parametr ciągu zapytania zapytania.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> Zależnie od zapytania, jeśli używasz `site:` — operator zapytań, istnieje ryzyko, że odpowiedź może zawierać treści dla dorosłych niezależnie od wartości [bezpieczne wyszukiwanie](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) ustawienie. Operatora `site:` używaj tylko wtedy, gdy znasz zawartość witryny i w swoim scenariuszu uwzględniasz możliwość pojawienia się zawartości dla dorosłych.

### <a name="freshness"></a>Aktualność

Aby ograniczyć wyniki odpowiedzi sieci web do stron sieci Web, które Bing wykryte w określonym czasie, należy ustawić [świeżości](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) parametr do jednego z następujących wartości bez uwzględniania wielkości liter zapytania:

* `Day` — Zwracany stron sieci web Bing odnalezione w ciągu ostatnich 24 godzinach
* `Week` — Zwracany stron sieci Web Bing odnalezione w ciągu ostatnich 7 dni
* `Month` — Zwraca stron sieci Web, która odnalezione w ciągu ostatnich 30 dni

Można również ustawić ten parametr na niestandardowy zakres dat w formularzu `YYYY-MM-DD..YYYY-MM-DD`. 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

Aby ograniczyć wyniki do jednego dnia, należy ustawić parametr świeżości do określonej daty:

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

Wyniki mogą obejmować stron sieci Web, które wykraczają poza określonym przedziale czasu, jeśli liczba stron sieci Web, który Bing pasuje do kryteriów filtru jest mniejsza od liczby stron sieci Web, której szukasz (lub domyślny numer, który Wyszukiwarka Bing zwróci).

## <a name="limiting-the-number-of-answers-in-the-response"></a>Ograniczenie liczby odpowiedzi w odpowiedzi

Bing może zwrócić wiele typów odpowiedzi w odpowiedzi JSON. Na przykład po wykonaniu zapytania *pływających + dinghies*, usługa Bing może zwrócić `webpages`, `images`, `videos`, i `relatedSearches`.

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

Aby ograniczyć liczbę odpowiedzi tego Bing zwraca odpowiedzi dwóch pierwszych stron sieci Web i obrazów, zestaw [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) parametr 2 zapytania.

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

Jeśli dodasz `responseFilter` parametr poprzednie zapytanie i ustaw go na stronach sieci Web i grup dyskusyjnych, odpowiedź zawiera tylko stron sieci Web, ponieważ wiadomości nie jest określany zapytania.

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

## <a name="promoting-answers-that-are-not-ranked"></a>Podwyższanie poziomu odpowiedzi, które nie są klasyfikowane

W przypadku stron sieci Web, obrazów, filmów wideo i relatedSearches górnej pozycjonowane odpowiedzi, które Wyszukiwarka Bing zwróci dla zapytania, odpowiedź będzie zawierać na nie odpowiedzieć. Jeśli ustawisz [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) dwóch (2), Bing zwraca górną dwie odpowiedzi rangi: stron sieci Web i obrazy. W przypadku Bing w celu uwzględnienia obrazów i filmów wideo w odpowiedzi, należy określić [podwyższanie poziomu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) parametr zapytania i ustaw ją na obrazów i filmów wideo.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Poniżej przedstawiono odpowiedź na żądanie powyżej. Bing zwraca górną dwie odpowiedzi, stron sieci Web i obrazy i wspiera filmów wideo do odpowiedzi.

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

Jeśli ustawisz `promote` do wiadomości, odpowiedź nie zawiera odpowiedzi wiadomości, ponieważ nie jest rangi odpowiedzi&mdash;możesz podwyższyć poziom tylko pozycjonowane odpowiedzi.

Nie wliczają odpowiedzi, które chcesz podwyższyć `answerCount` limit. Na przykład, jeśli rangi odpowiedzi są grup dyskusyjnych, obrazów i filmów wideo i ustawiasz `answerCount` 1 i `promote` do wiadomości, odpowiedź zawiera aktualności i obrazy. Lub, w przypadku filmów wideo, obrazów i grup dyskusyjnych rangi odpowiedzi odpowiedzi zawiera materiały wideo i wiadomości.

Możesz użyć `promote` tylko wtedy, gdy należy określić `answerCount` parametr zapytania.
