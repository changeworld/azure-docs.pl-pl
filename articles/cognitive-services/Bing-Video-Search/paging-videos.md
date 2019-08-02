---
title: Jak uzyskać dostęp do strony za pomocą dostępnych filmów wideo — wyszukiwanie wideo Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak na stronie wszystkie filmy wideo zwrócone przez interfejs API wyszukiwania wideo Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: c3570d8772734595c6707ca8103006867a8eb47a
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500719"
---
# <a name="paging-through-video-search-results"></a>Stronicowanie za poorednictwem wyników wyszukiwania wideo

Interfejs API wyszukiwania wideo Bing zwraca podzestaw wszystkich wyników wyszukiwania znalezionych dla zapytania. Stronicowając te wyniki przy użyciu kolejnych wywołań interfejsu API, możesz uzyskać i wyświetlić je w swojej aplikacji.

> [!NOTE]
> Stronicowanie dotyczy tylko wyszukiwania wideo (/videos/Search), a nie wideo Insights (/videos/Details) ani trendów wideo (/videos/Trending).

## <a name="total-estimated-matches"></a>Łączna Szacowana liczba dopasowań

Aby uzyskać szacowaną liczbę znalezionych wyników wyszukiwania, użyj pola [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-totalestimatedmatches) w odpowiedzi JSON.   
  
```json  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
## <a name="paging-through-videos"></a>Stronicowanie za poorednictwem wideo

Aby uzyskać dostęp do strony za pomocą dostępnych filmów wideo, użyj parametrów zapytania [Count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count) i [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset) podczas wysyłania żądania.  
  

|Parametr  |Opis  |
|---------|---------|
|`count`     | Określa liczbę wyników do zwrócenia w odpowiedzi. Maksymalna liczba wyników, które można zażądać w odpowiedzi to 100. Wartość domyślna to 10. Rzeczywista liczba podana może być mniejsza niż żądana.        |
|`offset`     | Określa liczbę wyników do pominięcia. Jest równa zero i powinna być mniejsza niż - (`count``totalEstimatedMatches`). `offset`          |

Jeśli na przykład chcesz wyświetlić 20 artykułów na stronę, należy ustawić wartość `count` 20 i `offset` na 0, aby uzyskać pierwszą stronę wyników. Dla każdej kolejnej strony można zwiększyć `offset` wartość o 20 (na przykład 20, 40).  
  
Poniższy przykład żąda 20 filmów wideo, zaczynając od przesunięcia 40.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

W przypadku użycia wartości domyślnej dla [liczby](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count)należy określić `offset` parametr zapytania, jak w poniższym przykładzie.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Jeśli strona zostanie przeszukana przez 35 wideo w danym momencie, należy `offset` ustawić parametr zapytania na 0 przy pierwszym żądaniu, a następnie zwiększyć `offset` wartość o 35 dla każdego kolejnego żądania. Jednak niektóre wyniki w następnej odpowiedzi mogą zawierać zduplikowane wyniki wideo z poprzedniej odpowiedzi. Na przykład pierwsze dwa filmy wideo w odpowiedzi mogą być takie same jak ostatnie dwa filmy wideo z poprzedniej odpowiedzi.

Aby wyeliminować zduplikowane wyniki, użyj [](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-nextoffset) pola `Videos` nextOffset obiektu.

Na przykład jeśli chcesz wyświetlać jednocześnie 30 filmów wideo, możesz ustawić wartość `count` 30 i `offset` na 0 w pierwszym żądaniu. W następnym żądaniu ustawisz `offset` parametr zapytania `nextOffset` na wartość.

> [!NOTE]
> `TotalEstimatedAnswers` Pole to oszacowanie łącznej liczby wyników wyszukiwania, które można pobrać dla bieżącego zapytania.  Po ustawieniu `count` i `offset` parametrze liczba może `TotalEstimatedAnswers` się zmienić. 
  
## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Pobieranie szczegółowych informacji o wideo](video-insights.md)
