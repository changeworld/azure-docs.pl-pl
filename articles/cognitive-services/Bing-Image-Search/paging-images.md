---
title: Strony za pomocą obrazów zwracane przez interfejs API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Przechodzenie przez różne strony obrazów zwracane przez interfejs API wyszukiwania obrazów Bing.
services: cognitive-services
author: swhite-msft
manager: cgonlun
ms.assetid: 3C8423F8-41E0-4F89-86B6-697E840610A7
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 38b2244d68de25f53d59dd4eb0a6beba03f0e51d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60916686"
---
# <a name="page-through-the-images-results"></a>Przejrzyj wyniki obrazów

Po wywołaniu interfejsu API wyszukiwania obrazów Bing zwraca listę wyników. Lista jest podzestawem całkowitej liczby wyników odpowiednich dla zapytania. Aby uzyskać szacowana liczba dostępnych wyników, dostęp do obiektu odpowiedzi [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#totalestimatedmatches) pola.  

W poniższym przykładzie przedstawiono `totalEstimatedMatches` pola, który zawiera odpowiedź obrazów.  

```json
{
    "_type" : "Images",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8...",
    "totalEstimatedMatches" : 838,
    "value" : [...]  
}  
```  

Na stronie przy użyciu dostępnych obrazów, użyj [liczba](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#count) i [przesunięcie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) parametry zapytania.  

`count` Parametr określa liczbę wyników do zwrócenia w odpowiedzi. Maksymalna liczba wyników, których może żądać w odpowiedzi wynosi 150. Wartością domyślną jest 35. Rzeczywista liczba dostarczonych może być mniejsza niż żądana.

`offset` Parametr określa liczbę wyników do pominięcia. `offset` Jest liczony od zera i powinna być mniejsza niż (`totalEstimatedMatches` - `count`).  

Jeśli chcesz wyświetlić 20 obrazów na każdej stronie, należy ustawić `count` do 20 i `offset` na 0, aby pobrać pierwszej strony wyników. Poniżej przedstawiono przykład, który żąda 20 obrazów, rozpoczynając od przesunięcia 40.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Jeśli wartość domyślna `count` wartość działa w przypadku implementacji, należy określić `offset` parametr zapytania.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Można by oczekiwać, że jeśli stronie 35 obrazów w danym momencie należy ustawić `offset` parametr na wartość 0 na pierwszego żądania zapytania, a następnie zwiększenie `offset` przez 35 na kolejnych żądań. Jednak niektóre wyniki w kolejnej odpowiedzi może być duplikaty poprzedniej odpowiedzi. Na przykład pierwsze dwa obrazy w odpowiedzi może być taka sama jak ostatnie dwa obrazy z poprzedniej odpowiedzi.

Aby wyeliminować zduplikowane wyniki, należy użyć [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#nextoffset) pole `Images` obiektu. `nextOffset` Pola informujący o tym `offset` do użycia dla następnego żądania. Na przykład, jeśli chcesz stronie 30 obrazów w czasie, należy ustawić `count` do 30 i `offset` na 0 w pierwszego żądania. W kolejnym żądaniu, należy ustawić `count` do 30 i `offset` wartość poprzedniej odpowiedzi `nextOffset`. Na stronie Wstecz, zalecamy utrzymanie stosu poprzedniego przesunięcia i wyświetlanie najnowszych, ostatnio.

> [!NOTE]
> Stronicowanie ma zastosowanie tylko do wyszukiwania obrazów (/ obrazy/wyszukiwania), a nie szczegółowe informacje o obrazach lub popularnych obrazów (/ Obrazy/analizy trendów).

> [!NOTE]
> `TotalEstimatedAnswers` Pole jest szacunkową liczbę całkowitą liczbę wyników wyszukiwania, możesz pobrać dla bieżącego zapytania.  Po ustawieniu `count` i `offset` parametrów, `TotalEstimatedAnswers` liczba mogą ulec zmianie. 
