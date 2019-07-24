---
title: Strona za poorednictwem dostępnych stron sieci Web — wyszukiwanie niestandardowe Bing
titleSuffix: Azure Cognitive Services
description: Pokazuje, w jaki sposób można wyświetlić strony ze wszystkich stron sieci Web, które wyszukiwanie niestandardowe Bing mogą zwrócić.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 13b4cef624c636b8935897338badf3349f27c7f5
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405043"
---
# <a name="paging-webpages"></a>Stronicowanie stron sieci Web 

Po wywołaniu interfejs API wyszukiwania niestandardowego Bing zwraca listę wyników. Lista jest podzbiorem całkowitej liczby wyników, które mogą być odpowiednie dla zapytania. Aby uzyskać szacowaną łączną liczbę dostępnych wyników, uzyskaj dostęp do pola [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#totalestimatedmatches) obiektu odpowiedzi.  
  
Poniższy przykład pokazuje `totalEstimatedMatches` pole, które zawiera odpowiedź sieci Web.  
  
```  
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```  
  
Aby uzyskać dostęp do strony w dostępnych stronach sieci Web, użyj parametrów zapytania [Count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#count) i [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#offset) .  
  
`count` Parametr określa liczbę wyników do zwrócenia w odpowiedzi. Maksymalna liczba wyników, które można zażądać w odpowiedzi to 50. Wartość domyślna to 10. Rzeczywista liczba podana może być mniejsza niż żądana.

`offset` Parametr określa liczbę wyników do pominięcia. Jest równa zero i powinna być mniejsza niż - (`count``totalEstimatedMatches`). `offset`  
  
Aby wyświetlić 15 stron sieci Web na stronie, należy ustawić wartość `count` 15 i `offset` na 0 w celu uzyskania pierwszej strony wyników. Dla każdej kolejnej strony można zwiększyć `offset` wartość o 15 (na przykład 15, 30).  
  
Poniżej przedstawiono przykład, który żąda 15 stron sieci Web, zaczynając od przesunięcia 45.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>
Host: api.cognitive.microsoft.com  
```  

Jeśli wartość domyślna `count` działa dla implementacji, wystarczy `offset` określić parametr zapytania.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> `TotalEstimatedAnswers` Pole to oszacowanie łącznej liczby wyników wyszukiwania, które można pobrać dla bieżącego zapytania.  Po ustawieniu `count` i `offset` parametrze liczba może `TotalEstimatedAnswers` się zmienić. 

