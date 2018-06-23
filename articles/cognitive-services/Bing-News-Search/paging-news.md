---
title: Jak przeglądanie dostępnych artykuły | Dokumentacja firmy Microsoft
description: Przedstawia sposób przeglądania wszystkie artykuły, które może zwracać Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: EA388F72-FA43-493B-967C-9560B3243C62
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 2c90d468536f0864d7deac073667e29e9a54692f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346928"
---
# <a name="paging-news"></a>Wiadomości stronicowania

Po wywołaniu interfejsu API wyszukiwania wiadomości Bing zwraca listę wyników. Lista jest podzbiorem całkowita liczba wyników, które mogą być istotne dla kwerendy. Aby uzyskać szacunkową liczba dostępnych wyników, należy uzyskać dostępu do obiektu odpowiedzi [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-totalmatches) pola.  
  
W poniższym przykładzie przedstawiono `totalEstimatedMatches` pola, które zawiera wiadomości odpowiedzi.  
  
```  
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Do przeglądania dostępnych artykułów, użyj [liczba](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#count) i [przesunięcie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#offset) parametry zapytania.  
  
`count` Parametr określa liczbę wyników do zwrócenia w odpowiedzi. Maksymalna liczba wyników, których mogą żądać w odpowiedzi to 100. Wartość domyślna to 10. Rzeczywista liczba dostarczonych może być mniejsza niż żądana.

`offset` Parametr określa liczbę wyników, aby pominąć. `offset` Jest liczony od zera i powinna być mniejsza niż (`totalEstimatedMatches` - `count`).  

Jeśli chcesz wyświetlić artykuły 20 na każdej stronie, należy ustawić `count` do 20 i `offset` na 0, aby pobrać pierwszej strony wyników. Na kolejnych stronach, możesz zwiększyć `offset` 20 (na przykład, 20, 40).  
  
Poniżej przedstawiono przykład 20 artykuły, rozpoczynając od przesunięcia 40 żądań.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Jeśli domyślna `count` wartość działa w przypadku implementacji, należy określić tylko `offset` parametr zapytania, jak pokazano w poniższym przykładzie:  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
> [!NOTE]
> Stronicowania ma zastosowanie tylko do wyszukiwania wiadomości (/ wiadomości/wyszukiwania), a nie trendów tematy (/ wiadomości/trendingtopics) lub kategorie wiadomości (/ wiadomości).