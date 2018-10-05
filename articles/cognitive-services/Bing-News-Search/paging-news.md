---
title: Jak stronicować za pośrednictwem artykuły z wiadomościami dostępne — wyszukiwanie wiadomości Bing
titlesuffix: Azure Cognitive Services
description: Pokazuje, jak stronicować przez wszystkie artykuły z wiadomościami, zwracających nowego wyszukiwania Bing.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: fff1da15df2e690cd0b37bb82654a4d30159325a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803396"
---
# <a name="paging-news"></a>Stronicowanie wiadomości

Po wywołaniu interfejsu API wyszukiwania wiadomości Bing zwraca listę wyników. Lista jest podzbiorem całkowita liczba wyników, które mogą dotyczyć zapytania. Aby uzyskać szacowana liczba dostępnych wyników, dostęp do obiektu odpowiedzi [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-totalmatches) pola.  
  
W poniższym przykładzie przedstawiono `totalEstimatedMatches` pola, który zawiera odpowiedź wiadomości.  
  
```  
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Na stronie przy użyciu dostępnych artykułów, użyj [liczba](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#count) i [przesunięcie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#offset) parametry zapytania.  
  
`count` Parametr określa liczbę wyników do zwrócenia w odpowiedzi. Maksymalna liczba wyników, których może żądać w odpowiedzi to 100. Wartość domyślna wynosi 10. Rzeczywista liczba dostarczonych może być mniejsza niż żądana.

`offset` Parametr określa liczbę wyników do pominięcia. `offset` Jest liczony od zera i powinna być mniejsza niż (`totalEstimatedMatches` - `count`).  

Jeśli chcesz wyświetlić 20 artykułów na stronę, należy ustawić `count` do 20 i `offset` na 0, aby pobrać pierwszej strony wyników. Na kolejnych stronach, możesz zwiększyć `offset` 20 (na przykład 20, 40).  
  
Poniżej przedstawiono przykład, który żąda 20 nowych artykułów, rozpoczynając od przesunięcia 40.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Jeśli wartość domyślna `count` wartość działa w przypadku implementacji, należy określić tylko `offset` parametr zapytania, jak pokazano w poniższym przykładzie:  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
> [!NOTE]
> Stronicowanie ma zastosowanie tylko do wyszukiwania wiadomości (/ wiadomości/wyszukiwania), a nie popularne tematy (/ wiadomości/trendingtopics) lub kategorii wiadomości (/ grup dyskusyjnych).