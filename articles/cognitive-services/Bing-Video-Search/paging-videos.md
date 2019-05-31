---
title: Jak strony za pomocą dostępnych filmów wideo — wyszukiwania wideo Bing
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak stronicować przez wszystkie filmy wideo, zwracane przez interfejs API wyszukiwania wideo Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 12549bb53a21dd657f51a4a02460ddc82c47bef8
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66386385"
---
# <a name="paging-through-video-search-results"></a>Stronicowanie wyników wyszukiwania klipów wideo

Interfejs API wyszukiwania wideo Bing zwraca podzbiór wszystkie wyniki wyszukiwania, znalezionych dla zapytania. Przez stronicowanie za pośrednictwem tych wyników z kolejnych wywołań interfejsu API, możesz pobrać i wyświetlić je w aplikacji.

> [!NOTE]
> Stronicowanie ma zastosowanie tylko do wyszukiwania filmów wideo (/ filmy/wyszukiwania), a nie informacjom o filmie wideo (/ filmy/szczegóły) lub popularnych klipów wideo (/ filmy/analizy trendów).

## <a name="total-estimated-matches"></a>Łączny szacowany dopasowań

Aby uzyskać szacunkową liczbę wyników wyszukiwania znaleziono, użyj [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-totalestimatedmatches) pola w odpowiedzi JSON.   
  
```json  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
## <a name="paging-through-videos"></a>Stronicowanie filmów wideo

Na stronie przy użyciu dostępnych filmów wideo, użyj [liczba](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count) i [przesunięcie](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset) parametry zapytania podczas wysyłania żądania.  
  

|Parametr  |Opis  |
|---------|---------|
|`count`     | Określa liczbę wyników do zwrócenia w odpowiedzi. Maksymalna liczba wyników, których może żądać w odpowiedzi to 100. Wartość domyślna wynosi 10. Rzeczywista liczba dostarczonych może być mniejsza niż żądana.        |
|`offset`     | Określa liczbę wyników do pominięcia. `offset` Jest liczony od zera i powinna być mniejsza niż (`totalEstimatedMatches` - `count`).          |

Na przykład, jeśli chcesz wyświetlić 20 artykułów na stronę, należy ustawić `count` do 20 i `offset` na 0, aby pobrać pierwszej strony wyników. Na kolejnych stronach, możesz zwiększyć `offset` 20 (na przykład 20, 40).  
  
Poniższy przykład żądania 20 filmów wideo, rozpoczynając od przesunięcia 40.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Jeśli korzystasz z wartością domyślną dla [liczba](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count), należy określić `offset` parametr, jak w poniższym przykładzie zapytania.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Jeśli strony za pośrednictwem 35 filmów wideo w czasie, należy ustawić `offset` parametr na wartość 0 na pierwszego żądania zapytania, a następnie zwiększenie `offset` przez 35 na kolejnych żądań. Jednak niektóre wyniki w następnej odpowiedzi może zawierać zduplikowanych wyniki wideo z poprzedniej odpowiedzi. Na przykład dwa pierwsze filmów wideo w odpowiedzi może być taka sama jak ostatnie dwa filmy wideo z poprzedniej odpowiedzi.

Aby wyeliminować zduplikowane wyniki, należy użyć [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-nextoffset) pole `Videos` obiektu.

Na przykład, jeśli chcesz stronie 30 filmów wideo w czasie, możesz ustawić `count` do 30 i `offset` na 0 w pierwszego żądania. W kolejnym żądaniu, należy ustawić `offset` parametr do zapytania `nextOffset` wartość.

> [!NOTE]
> `TotalEstimatedAnswers` Pole jest szacunkową liczbę całkowitą liczbę wyników wyszukiwania, możesz pobrać dla bieżącego zapytania.  Po ustawieniu `count` i `offset` parametrów, `TotalEstimatedAnswers` liczba mogą ulec zmianie. 
  
## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Pobieranie szczegółowych informacji o wideo](video-insights.md)
