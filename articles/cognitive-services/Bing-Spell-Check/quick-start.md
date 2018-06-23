---
title: API sprawdzania pisowni usługi Bing szybki start | Dokumentacja firmy Microsoft
description: Pokazano, jak rozpocząć korzystanie z API sprawdzania pisowni usługi Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: AF8EB1F0-386D-4555-9354-735611435F04
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: article
ms.date: 06/21/2016
ms.author: scottwhi
ms.openlocfilehash: cae8353e5be6e70eca90e5995b29b6774fc7d6a9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346952"
---
# <a name="your-first-spell-check-request"></a>Sprawdzanie pisowni pierwszego żądania

Przed skonfigurowaniem pierwszego połączenia, należy uzyskać klucz subskrypcji usługi kognitywnych. Aby uzyskać klucz, zobacz [spróbuj kognitywnych usług](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api).

Aby sprawdzić ciąg tekstowy pisowni i gramatyki błędów, czy Wyślij żądanie GET do następujący punkt końcowy:  
  
```
https://api.cognitive.microsoft.com/bing/v5.0/spellcheck
```

> [!NOTE]
> Punkt końcowy w wersji 7 w wersji zapoznawczej:
> 
> ```
> https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
> ```  
  
Żądanie musi używać protokołu HTTPS.

Zaleca się, że wszystkie żądania pochodzi z serwera. Dystrybucja klucza w ramach aplikacji klienta zawiera więcej możliwości złośliwego innej do niego dostęp. Ponadto nawiązywanie połączeń z serwerem zapewnia jeden punkt uaktualniania wersji interfejsu API.

Żądanie musi określać [tekst](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#text) parametru zapytania, który zawiera ciąg tekstowy do potwierdzenia. Mimo że jest to opcjonalne, powinny również określać żądanie [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#mkt) parametru zapytania, który identyfikuje rynku miejscu wyniki pochodzą z. Aby uzyskać listę opcjonalne parametry zapytania takie jak `mode`, zobacz [parametry zapytania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#query-parameters). Wszystkie wartości parametrów zapytania musi być zakodowane w adresie URL.  
  
Żądanie musi określać [Ocp-Apim-subskrypcji — klucz](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#subscriptionkey) nagłówka. Mimo że jest to opcjonalne, zachęca się także określić następujące nagłówki:  
  
-   [Agent użytkownika](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientid)  
-   [ClientIP-X-wyszukiwania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientip)  
-   [X wyszukiwania lokalizacji](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#location)  

Aby uzyskać listę wszystkich nagłówków żądań i odpowiedzi, zobacz [nagłówki](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#headers).

## <a name="the-request"></a>Żądanie

Poniżej przedstawiono żądania, który zawiera wszystkie parametry sugerowane zapytania i nagłówków. Jeśli pierwsza wywoływania żadnego z interfejsów API Bing, nie dołączaj nagłówka Identyfikatora klienta. Identyfikator klienta należy uwzględnić tylko, jeśli został wcześniej wywołuje interfejs API Bing i Bing zwrócony identyfikator klienta dla użytkownika lub kombinacji urządzeń. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v5.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> Żądanie w wersji 7 w wersji zapoznawczej:

> ```  
> GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE  
> X-MSEdge-ClientIP: 999.999.999.999  
> X-Search-Location: lat:47.60357;long:-122.3295;re:100  
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
> Host: api.cognitive.microsoft.com  
> ```  

Poniżej przedstawiono odpowiedź na wcześniejsze żądanie. W przykładzie przedstawiono również nagłówki odpowiedzi specyficzne dla usługi Bing.

```
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  


## <a name="next-steps"></a>Kolejne kroki

Wypróbuj interfejsu API. Przejdź do [konsoli testowania interfejsu API sprawdzania pisowni](https://dev.cognitive.microsoft.com/docs/services/56e73033cf5ff80c2008c679/operations/57855119bca1df1c647bc358). 

Aby uzyskać więcej informacji o korzystanie z obiektów odpowiedzi, zobacz [ciągów tekstowych sprawdzania pisowni](./proof-text.md).

