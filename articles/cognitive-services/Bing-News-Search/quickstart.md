---
title: Wiadomości interfejsu API Search szybki start | Dokumentacja firmy Microsoft
description: Pokazano, jak rozpocząć korzystanie z interfejsu API wyszukiwania usługi Bing wiadomości.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 9CF6EAF3-42D8-4321-983C-4AC3896E8E03
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 2aa1b3de07bd61eebfc1d410cda76c32fc7c958e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349196"
---
# <a name="your-first-news-search-query"></a>Pierwszego zapytania wyszukiwania wiadomości

Przed skonfigurowaniem pierwszego połączenia, należy uzyskać klucz subskrypcji usługi kognitywnych. Aby uzyskać klucz, zobacz [spróbuj kognitywnych usług](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api).

Aby uzyskać wyniki wyszukiwania tylko do grup dyskusyjnych, spowoduje wysłanie żądania GET do następujący punkt końcowy:

```http
https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Żądanie musi używać protokołu HTTPS.

Zaleca się, że wszystkie żądania pochodzi z serwera. Dystrybucja klucza w ramach aplikacji klienta oferuje więcej możliwości złośliwego innych firm do niego dostęp. Ponadto nawiązywanie połączeń z serwerem zapewnia jeden punkt uaktualniania wersji interfejsu API.

Żądanie musi określać [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query) parametru zapytania, który zawiera użytkownika terminu wyszukiwania. Chociaż jest to opcjonalne, powinny również określać żądanie [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#mkt) parametru zapytania, który identyfikuje rynku miejscu wyniki pochodzą z. Aby uzyskać listę opcjonalne parametry zapytania takie jak `freshness` i `textDecorations`, zobacz [parametry zapytania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters). Wszystkie wartości parametrów zapytania musi być zakodowane w adresie URL.

Żądanie musi określać [Ocp-Apim-subskrypcji — klucz](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#subscriptionkey) nagłówka. Mimo że jest to opcjonalne, zachęca się także określić następujące nagłówki:

- [Agent użytkownika](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#clientid)
- [ClientIP-X-wyszukiwania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#clientip)
- [X wyszukiwania lokalizacji](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#location)

Nagłówki adresów IP i lokalizacji klienta są ważne dla zwracania zawartości pamiętać lokalizacji.

Aby uzyskać listę wszystkich nagłówków żądań i odpowiedzi, zobacz [nagłówki](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#headers).

## <a name="the-request"></a>Żądanie

Poniżej przedstawiono żądania wiadomości, który zawiera wszystkie parametry sugerowane zapytania i nagłówków. Jeśli pierwsza wywoływania żadnego z interfejsów API Bing, nie dołączaj nagłówka Identyfikatora klienta. Identyfikator klienta należy uwzględnić tylko, jeśli został wcześniej wywołuje interfejs API Bing i Bing zwrócony identyfikator klienta dla użytkownika lub kombinacji urządzeń.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="the-response"></a>Odpowiedź

Poniżej przedstawiono odpowiedź na wcześniejsze żądanie. W przykładzie przedstawiono również nagłówki odpowiedzi specyficzne dla usługi Bing.

```http
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "Sailing College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },
    {
        "name" : "Reunion at Fabrikam Lakes Sailing Club, celebrates 50 years...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.38210...",
                "width" : 650,
                "height" : 366
            }
        },
        "description" : "The reunion on April 29, at Fabrikam Lakes Sailing...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T13:08:00"
    },
    {
        "name" : "Sailing Club to host Dinghy Sailing World...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.364AB41...",
                "width" : 448,
                "height" : 300
            }
        },
        "description" : "The sailing club that trained Olympian Ben...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-04T11:02:00",
        "category" : "Sports"
    },
    {
        "name" : "A 24-Carat Dinghy",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.6CC2...",
                "width" : 700,
                "height" : 466
            }
        },
        "description" : "“Hard dinghies are for purists, the kind of people who...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-03T12:14:00",
        "category" : "Politics"
    }]
}
```

## <a name="next-steps"></a>Kolejne kroki

Wypróbuj interfejsu API. Przejdź do [konsoli testowania wyszukiwania interfejsu API wiadomości](https://dev.cognitive.microsoft.com/docs/services/56b43f72cf5ff8098cef380a/operations/56f02400dbe2d91900c68553).

Aby uzyskać więcej informacji o korzystanie z obiektów odpowiedzi, zobacz [co to jest wyszukiwania wiadomości Bing?](./search-the-web.md). Więcej informacji na temat następujące typowe akcje się tam również:

- [Pobieranie bieżącej najnowsze wiadomości](./search-the-web.md#getting-todays-top-news)
- [Pobieranie wiadomości według kategorii](./search-the-web.md#getting-news-by-category)
- [Pobieranie wiadomości trendów](./search-the-web.md#getting-trending-news)
