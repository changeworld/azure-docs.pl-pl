---
title: Jak przeglądanie dostępnych obrazów | Dokumentacja firmy Microsoft
description: Przedstawia sposób przeglądania wszystkie obrazy zwracających Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 3C8423F8-41E0-4F89-86B6-697E840610A7
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: a74ee817e84be5bb563c5fdaf25afc1dc14732e5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346876"
---
# <a name="paging-results"></a>Stronicowanie wyników

Po wywołaniu interfejsu API obraz wyszukiwania usługi Bing zwraca listę wyników. Lista jest podzbiorem całkowita liczba wyników, które mają zastosowanie do zapytania. Aby uzyskać szacunkową liczba dostępnych wyników, należy uzyskać dostępu do obiektu odpowiedzi [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#totalestimatedmatches) pola.  
  
W poniższym przykładzie przedstawiono `totalEstimatedMatches` pole zawierające odpowiedzi obrazów.  
  
```json
{
    "_type" : "Images",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8...",
    "totalEstimatedMatches" : 838,
    "value" : [...]  
}  
```  
  
Do przeglądania dostępnych obrazów, użyj [liczba](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#count) i [przesunięcie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) parametry zapytania.  
  
`count` Parametr określa liczbę wyników do zwrócenia w odpowiedzi. Maksymalna liczba wyników, których mogą żądać w odpowiedzi wynosi 150. Wartość domyślna to 35. Rzeczywista liczba dostarczonych może być mniejsza niż żądana.

`offset` Parametr określa liczbę wyników, aby pominąć. `offset` Jest liczony od zera i powinna być mniejsza niż (`totalEstimatedMatches` - `count`).  
  
Jeśli chcesz wyświetlić 20 obrazów na stronie, należy ustawić `count` do 20 i `offset` na 0, aby pobrać pierwszej strony wyników. Poniżej przedstawiono przykład 20 obrazów, rozpoczynając od przesunięcia 40 żądań.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Jeśli domyślna `count` wartość działa implementacji, należy określić `offset` parametr zapytania.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Może spodziewać się, że jeśli stronie 35 obrazów w czasie należy ustawić `offset` parametr 0 na pierwsze żądanie zapytania, a następnie zwiększyć `offset` przez 35 na kolejnych żądań. Jednak niektóre wyniki w kolejnych odpowiedzi może być duplikatów poprzedniej odpowiedzi. Na przykład pierwsze dwa obrazy w odpowiedzi może być taka sama jak ostatnie dwa obrazy z poprzedniej odpowiedzi.

Aby usunąć zduplikowane wyniki, należy użyć [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#nextoffset) pole `Images` obiektu. `nextOffset` Pole wskazuje `offset` do użycia dla następnego żądania. Na przykład, jeśli chcesz strony 30 obrazów w czasie, należy ustawić `count` do 30 i `offset` na 0 w pierwszego żądania. W następnym żądaniu, należy ustawić `count` do 30 i `offset` wartość poprzedniej odpowiedzi `nextOffset`. Stronę do tyłu, zalecamy utrzymanie stosu poprzedniej przesunięcia i wyświetlanie najnowszej.

> [!NOTE]
> Stronicowania ma zastosowanie tylko do wyszukiwania obrazu (/ obrazów/wyszukiwania), a nie informacjami obrazu lub trendów obrazów (/ obrazów/umożliwia analizę trendów).