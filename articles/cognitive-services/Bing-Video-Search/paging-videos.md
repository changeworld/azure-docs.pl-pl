---
title: Jak strony za pomocą dostępnych filmów wideo — wyszukiwania wideo Bing
titlesuffix: Azure Cognitive Services
description: Pokazuje, jak stronicować przez wszystkie pliki wideo, które może zwracać Bing.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 9b030312c562d1c0a6cbacfc7f424289dee2e8de
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225569"
---
# <a name="paging-videos"></a>Pliki stronicowania wideo

Po wywołaniu interfejsu API wyszukiwania wideo Bing zwraca listę wyników. Lista jest podzestawem całkowitej liczby wyników odpowiednich dla zapytania. Aby uzyskać szacowana liczba dostępnych wyników, dostęp do obiektu odpowiedzi [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) pola.  
  
W poniższym przykładzie przedstawiono `totalEstimatedMatches` pola, który zawiera odpowiedź wideo.  
  
```  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
Na stronie przy użyciu dostępnych filmów wideo, użyj [liczba](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) i [przesunięcie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) parametry zapytania.  
  
`count` Parametr określa liczbę wyników do zwrócenia w odpowiedzi. Maksymalna liczba wyników, które mogą zażądać w odpowiedzi wynosi 105. Wartością domyślną jest 35. Rzeczywista liczba dostarczonych może być mniejsza niż żądana.

`offset` Parametr określa liczbę wyników do pominięcia. `offset` Jest liczony od zera i powinna być mniejsza niż (`totalEstimatedMatches` - `count`).  
  
Jeśli chcesz wyświetlić 20 filmów wideo na stronie, należy ustawić `count` do 20 i `offset` na 0, aby pobrać pierwszej strony wyników. Na kolejnych stronach, możesz zwiększyć `offset` 20 (na przykład 20, 40).  

Poniżej przedstawiono przykład, który żąda 20 filmów wideo, rozpoczynając od przesunięcia 40.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Jeśli wartość domyślna `count` wartość działa w przypadku implementacji, należy określić `offset` parametr zapytania.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Zazwyczaj Jeśli stronie 35 filmów wideo w czasie należy ustawić `offset` parametr na wartość 0 na pierwszego żądania zapytania, a następnie zwiększenie `offset` przez 35 na kolejnych żądań. Jednak niektóre wyniki w kolejnej odpowiedzi może być duplikaty poprzedniej odpowiedzi. Na przykład dwa pierwsze filmów wideo w odpowiedzi może być taka sama jak ostatnie dwa filmy wideo z poprzedniej odpowiedzi.

Aby wyeliminować zduplikowane wyniki, należy użyć [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) pole `Videos` obiektu.

Na przykład, jeśli chcesz stronie 30 filmów wideo w czasie, należy ustawić `count` do 30 i `offset` na 0 w pierwszego żądania. W kolejnym żądaniu, należy ustawić `offset` parametr do zapytania `nextOffset` wartość.


> [!NOTE]
> Stronicowanie ma zastosowanie tylko do wyszukiwania filmów wideo (/ filmy/wyszukiwania), a nie informacjom o filmie wideo (/ filmy/szczegóły) lub popularnych klipów wideo (/ filmy/analizy trendów).