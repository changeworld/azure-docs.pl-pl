---
title: 'Szybki start: Interfejs API sprawdzania pisowni Bing'
titlesuffix: Azure Cognitive Services
description: Jak rozpocząć korzystanie z interfejsu API sprawdzania pisowni Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 06/21/2016
ms.author: scottwhi
ms.openlocfilehash: 70aec949dd8f1b27a2a04c28a892b4caa2122844
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867821"
---
# <a name="quickstart-your-first-spell-check-request"></a>Szybki start: Pierwsze żądanie sprawdzania pisowni

Pobierz [klucz dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) w obszarze **Wyszukiwanie**.  Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Aby sprawdzić ciąg tekstowy pod kątem błędów pisowni i gramatyki, należy przesłać żądanie GET do następującego punktu końcowego:  
  
```
https://api.cognitive.microsoft.com/bing/v5.0/spellcheck
```

> [!NOTE]
> Punkt końcowy w wersji 7 (wersja zapoznawcza):
> 
> ```
> https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
> ```  
  
Żądanie musi używać protokołu HTTPS.

Zalecamy, aby wszystkie żądania pochodziły z serwera. Dystrybuowanie klucza w ramach aplikacji klienckiej dostarcza więcej okazji do przejęcia go przez złośliwy kod innych firm. Ponadto wykonywanie wywołań z serwera zapewnia jeden punkt uaktualniania dla przyszłych wersji interfejsu API.

Żądanie musi określać parametr zapytania [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#text), który zawiera ciąg tekstowy do sprawdzenia. Chociaż jest to opcjonalne, żądanie powinno również określać parametr zapytania [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#mkt), identyfikujący rynek, z którego mają pochodzić wyniki. Aby uzyskać listę opcjonalnych parametrów zapytania, takich jak `mode`, zobacz [Parametry zapytania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#query-parameters). Wszystkie wartości parametrów zapytania muszą być zakodowane w adresie URL.  
  
Żądanie musi określać nagłówek [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#subscriptionkey). Mimo że jest to opcjonalne, zachęcamy, aby określić również następujące nagłówki:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#location)  

Aby uzyskać listę wszystkich nagłówków żądań i odpowiedzi, zobacz [Nagłówki](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#headers).

## <a name="the-request"></a>Żądanie

Poniżej przedstawiono żądanie, które zawiera wszystkie sugerowane parametry zapytania i nagłówki. Jeśli jest to Twoje pierwsze wywoływanie dowolnego z interfejsów API Bing, nie dołączaj nagłówka identyfikatora klienta. Identyfikator klienta należy uwzględnić tylko wtedy, gdy interfejs API Bing został już wywołany i usługa Bing zwróciła identyfikator klienta dla kombinacji użytkownika i urządzenia. 
  
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
> Żądanie w wersji 7 (wersja zapoznawcza):

> ```  
> GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE  
> X-MSEdge-ClientIP: 999.999.999.999  
> X-Search-Location: lat:47.60357;long:-122.3295;re:100  
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
> Host: api.cognitive.microsoft.com  
> ```  

Poniżej przedstawiono odpowiedź na poprzednie żądanie. W przykładzie pokazano również nagłówki odpowiedzi specyficzne dla usługi Bing.

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


## <a name="next-steps"></a>Następne kroki

Wypróbuj interfejs API. Przejdź do [konsoli testowania interfejsu API sprawdzania pisowni](https://dev.cognitive.microsoft.com/docs/services/56e73033cf5ff80c2008c679/operations/57855119bca1df1c647bc358). 

Aby uzyskać szczegółowe informacje dotyczące korzystania z obiektów odpowiedzi, zobacz [Ciągi tekstowe do sprawdzania pisowni](./proof-text.md).

