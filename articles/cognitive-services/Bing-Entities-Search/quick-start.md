---
title: Jednostek wyszukiwania API szybki start | Dokumentacja firmy Microsoft
description: Pokazano, jak rozpocząć korzystanie z interfejsem API Bing jednostek wyszukiwania.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: B206A254-B7E9-49FF-AFD5-87B1E4D6D30B
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 07/06/2017
ms.author: scottwhi
ms.openlocfilehash: 12031d2447920c7e2d6180f35cf4fb29aa1b6150
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346968"
---
# <a name="making-your-first-entities-request"></a>Tworzenie pierwszego jednostki żądania

Interfejsu API Search jednostki wysyła kwerendę wyszukiwania usługi Bing i pobiera wyniki obejmujące jednostki i miejsca. Miejsce wyniki obejmują restauracji, hoteli lub innych firm lokalnego. Dla miejsca zapytanie można określić nazwy lokalnej firmy lub można zadawać listę (na przykład restauracji pobliżu). Jednostki wyniki obejmują osoby, miejsca lub rzeczy. Miejsce w tym kontekście jest atrakcji, stanów, krajach itp. 

## <a name="first-steps"></a>Pierwsze kroki

Przed skonfigurowaniem pierwszego połączenia, należy uzyskać klucz subskrypcji usługi kognitywnych. Aby uzyskać klucz, zobacz [spróbuj kognitywnych usług](https://azure.microsoft.com/try/cognitive-services/?api=bing-entities-search-api). (Interfejs API jednostek wyszukiwania nie jest widoczny u góry, kliknij przycisk **wyszukiwania** karcie, a następnie przewiń w dół, aż zostanie wyświetlony.)

## <a name="the-endpoint"></a>Punkt końcowy

Aby uzyskać jednostki i umieścić wyniki wyszukiwania, Wyślij żądanie GET do następujący punkt końcowy:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Żądanie musi używać protokołu HTTPS.

Zaleca się, że wszystkie żądania pochodzi z serwera. Dystrybucja klucza w ramach aplikacji klienta oferuje więcej możliwości złośliwego innych firm do niego dostęp. Ponadto nawiązywanie połączeń z serwerem zapewnia jeden punkt uaktualniania wersji interfejsu API.

## <a name="specifying-query-parameters-and-headers"></a>Określanie parametrów zapytania i nagłówków

Żądanie musi określać [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query) parametru zapytania, który zawiera użytkownika terminu wyszukiwania. Żądanie musi również określać [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mkt) parametru zapytania, który identyfikuje rynku miejscu wyniki pochodzą z. Listę parametrów opcjonalnych zapytania, zobacz [parametry zapytania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters). Adres URL kodowania wszystkie parametry zapytania.  
  
Żądanie musi określać [Ocp-Apim-subskrypcji — klucz](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#subscriptionkey) nagłówka. Mimo że jest to opcjonalne, zachęca się także określić następujące nagłówki:  
  
-   [Agent użytkownika](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientip)  
-   [X wyszukiwania lokalizacji](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#location)  

Nagłówki adresów IP i lokalizacji klienta są ważne dla zwracania zawartości pamiętać lokalizacji.  

Aby uzyskać listę wszystkich nagłówków żądań i odpowiedzi, zobacz [nagłówki](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers).

## <a name="the-request"></a>Żądanie

Poniżej przedstawiono żądania jednostek, który zawiera wszystkie parametry sugerowane zapytania i nagłówków. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Jeśli pierwsza wywoływania żadnego z interfejsów API Bing, nie dołączaj nagłówka Identyfikatora klienta. Identyfikator klienta należy uwzględnić tylko, jeśli został wcześniej wywołuje interfejs API Bing i Bing zwrócony identyfikator klienta dla użytkownika lub kombinacji urządzeń.

## <a name="the-response"></a>Odpowiedź

Poniżej przedstawiono odpowiedź na wcześniejsze żądanie. W przykładzie przedstawiono również nagłówki odpowiedzi specyficzne dla usługi Bing. Informacje dla obiekt odpowiedzi, zobacz [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse).

```
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "mount rainier"
    },
    "entities" : {
        "queryScenario" : "DominantEntity",
        "value" : [{
            "contractualRules" : [{
                "_type" : "ContractualRules\/LicenseAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "license" : {
                    "name" : "CC-BY-SA",
                    "url" : "http:\/\/creativecommons.org\/licenses\/by-sa\/3.0\/"
                },
                "licenseNotice" : "Text under CC-BY-SA license"
            },
            {
                "_type" : "ContractualRules\/LinkAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "text" : "en.wikipedia.org",
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            },
            {
                "_type" : "ContractualRules\/MediaAttribution",
                "targetPropertyName" : "image",
                "mustBeCloseToContent" : true,
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            }],
            "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Mount%20Rainier...",
            "name" : "Mount Rainier",
            "image" : {
                "name" : "Mount Rainier",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A21890c0e1f...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
                }],
                "hostPageUrl" : "http:\/\/upload.wikimedia.org\/wikipedia...",
                "width" : 110,
                "height" : 110
            },
            "description" : "Mount Rainier, Mount Tacoma, or Mount Tahoma is the highest...",
            "entityPresentationInfo" : {
                "entityScenario" : "DominantEntity",
                "entityTypeHints" : ["Attraction"],
                "entityTypeDisplayHint" : "Mountain"
            },
            "bingId" : "9ae3e6ca-81ea-6fa1-ffa0-42e1d78906"
        }]
    }
}
```



## <a name="next-steps"></a>Kolejne kroki

Wypróbuj interfejsu API. Przejdź do [jednostek wyszukiwania API testowania konsoli](https://dev.cognitive.microsoft.com/docs/services/7a3fb374be374859a823b79fd938cc65/). 

Aby uzyskać więcej informacji o korzystanie z obiektów odpowiedzi, zobacz [wyszukiwanie w sieci Web jednostki i miejsca](./search-the-web.md).

Należy przeczytać [Bing oraz wymagania dotyczące wyświetlania](./use-display-requirements.md) , nie zostanie przerwane dowolne zasady dotyczące korzystania z wyników wyszukiwania.
