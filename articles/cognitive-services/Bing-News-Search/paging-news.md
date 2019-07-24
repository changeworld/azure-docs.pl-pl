---
title: Jak uzyskać stronę za wyszukiwanie wiadomości Bing wyników
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przechodzić do strony z wiadomościami zwracanymi przez interfejs API wyszukiwania wiadomości Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: e5f8dce1a03e44758eea737ad2da419fa67c36a2
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423750"
---
# <a name="how-to-page-through-news-search-results"></a>Jak na stronie Wyniki wyszukiwania wiadomości

Po wywołaniu interfejsu API wyszukiwanie wiadomości Bing zwraca listę wyników, które są istotne dla zapytania. Aby uzyskać szacowaną łączną liczbę dostępnych wyników, uzyskaj dostęp do pola [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news-totalmatches) obiektu odpowiedzi.  
  
Poniższy przykład pokazuje `totalEstimatedMatches` pole, które zawiera odpowiedź na wiadomości.  

```json
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Aby uzyskać dostęp do strony w dostępnych artykułach, użyj parametrów zapytania [Count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#count) i [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#offset) .  
 

|Parametr  |Opis  |
|---------|---------|
|`count`     | Określa liczbę wyników do zwrócenia w odpowiedzi. Maksymalna liczba wyników, które można zażądać w odpowiedzi to 100. Wartość domyślna to 10. Rzeczywista liczba podana może być mniejsza niż żądana.        |
|`offset`     | Określa liczbę wyników do pominięcia. Jest równa zero i powinna być mniejsza niż - (`count``totalEstimatedMatches`). `offset`          |

Jeśli na przykład chcesz wyświetlić 20 artykułów na stronę, należy ustawić wartość `count` 20 i `offset` na 0, aby uzyskać pierwszą stronę wyników. Dla każdej kolejnej strony można zwiększyć `offset` wartość o 20 (na przykład 20, 40).  
  
Poniższy przykład żąda 20 artykułów informacyjnych zaczynających się od przesunięcia 40.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Jeśli wartość domyślna `count` działa dla implementacji, należy określić `offset` tylko parametr zapytania, jak pokazano w następującym przykładzie:  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> Stronicowanie dotyczy tylko wyszukiwania wiadomości (/News/Search), a nie do trendowania tematów (/News/Trendingtopics) lub kategorii wiadomości (/News).

> [!NOTE]
> `TotalEstimatedAnswers` Pole to oszacowanie łącznej liczby wyników wyszukiwania, które można pobrać dla bieżącego zapytania.  Po ustawieniu `count` i `offset` parametrze liczba może `TotalEstimatedAnswers` się zmienić. 
