---
title: 'Wyszukiwanie niestandardowe Bing: Przeglądania dostępnych stron sieci Web | Dokumentacja firmy Microsoft'
description: Przedstawia sposób przeglądania wszystkich stron sieci Web, która może zwrócić Bing.
services: cognitive-services
author: brapel
manager: ehansen
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: f2f545a5a9195fc65515ea716f277723600cbb78
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346892"
---
# <a name="paging-webpages"></a>Stronicowanie stron sieci Web 

Po wywołaniu interfejsu API Search niestandardowe Bing zwraca listę wyników. Lista jest podzbiorem całkowita liczba wyników, które mogą być istotne dla kwerendy. Aby uzyskać szacunkową liczba dostępnych wyników, należy uzyskać dostępu do obiektu odpowiedzi [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#totalestimatedmatches) pola.  
  
W poniższym przykładzie przedstawiono `totalEstimatedMatches` pole zawierające odpowiedzi sieci Web.  
  
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
  
Do przeglądania dostępnych stron sieci Web, użyj [liczba](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#count) i [przesunięcie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#offset) parametry zapytania.  
  
`count` Parametr określa liczbę wyników do zwrócenia w odpowiedzi. Maksymalna liczba wyników, których mogą żądać w odpowiedzi jest 50. Wartość domyślna to 10. Rzeczywista liczba dostarczonych może być mniejsza niż żądana.

`offset` Parametr określa liczbę wyników, aby pominąć. `offset` Jest liczony od zera i powinna być mniejsza niż (`totalEstimatedMatches` - `count`).  
  
Jeśli chcesz wyświetlić 15 stron sieci Web na stronie, należy ustawić `count` do 15 i `offset` na 0, aby pobrać pierwszej strony wyników. Na kolejnych stronach, możesz zwiększyć `offset` 15 (na przykład, 15, 30).  
  
Poniżej przedstawiono przykład żąda 15 stron sieci Web, rozpoczynając od przesunięcia 45.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>
Host: api.cognitive.microsoft.com  
```  

Jeśli domyślna `count` wartość działa implementacji, należy określić `offset` parametr zapytania.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>  
Host: api.cognitive.microsoft.com  
```  

