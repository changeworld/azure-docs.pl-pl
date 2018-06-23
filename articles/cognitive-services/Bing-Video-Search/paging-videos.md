---
title: Jak przeglądanie dostępnych wideo | Dokumentacja firmy Microsoft
description: Przedstawia sposób przeglądania wszystkie pliki wideo, które może zwracać Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 910A485F-BCF3-42B9-958D-DD48BDEDA965
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 00476825eb3fc1008c3f2172b591d8b7a2f35884
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346905"
---
# <a name="paging-videos"></a>Pliki stronicowania wideo

Po wywołaniu interfejsu API wideo wyszukiwania usługi Bing zwraca listę wyników. Lista jest podzbiorem całkowita liczba wyników, które mają zastosowanie do zapytania. Aby uzyskać szacunkową liczba dostępnych wyników, należy uzyskać dostępu do obiektu odpowiedzi [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) pola.  
  
W poniższym przykładzie przedstawiono `totalEstimatedMatches` pole zawierające odpowiedzi wideo.  
  
```  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
Do przeglądania dostępnych plików wideo, użyj [liczba](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) i [przesunięcie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) parametry zapytania.  
  
`count` Parametr określa liczbę wyników do zwrócenia w odpowiedzi. Maksymalna liczba wyników, których mogą żądać w odpowiedzi jest 105. Wartość domyślna to 35. Rzeczywista liczba dostarczonych może być mniejsza niż żądana.

`offset` Parametr określa liczbę wyników, aby pominąć. `offset` Jest liczony od zera i powinna być mniejsza niż (`totalEstimatedMatches` - `count`).  
  
Jeśli chcesz wyświetlić 20 wideo na każdej stronie, należy ustawić `count` do 20 i `offset` na 0, aby pobrać pierwszej strony wyników. Na kolejnych stronach, możesz zwiększyć `offset` 20 (na przykład, 20, 40).  

Poniżej przedstawiono przykład 20 plików wideo, rozpoczynając od przesunięcia 40 żądań.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Jeśli domyślna `count` wartość działa implementacji, należy określić `offset` parametr zapytania.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Zwykle, jeśli strony 35 wideo w czasie należy ustawić `offset` parametr 0 na pierwsze żądanie zapytania, a następnie zwiększyć `offset` przez 35 na kolejnych żądań. Jednak niektóre wyniki w kolejnych odpowiedzi może być duplikatów poprzedniej odpowiedzi. Na przykład pierwszych dwóch plików wideo w odpowiedzi może być identyczny z ostatnich dwóch filmy wideo z poprzedniej odpowiedzi.

Aby usunąć zduplikowane wyniki, należy użyć [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) pole `Videos` obiektu.

Na przykład, jeśli chcesz strony 30 filmów wideo w czasie, należy ustawić `count` do 30 i `offset` na 0 w pierwszego żądania. W następnym żądaniu, należy ustawić `offset` parametr do zapytania `nextOffset` wartość.


> [!NOTE]
> Stronicowania ma zastosowanie tylko do wyszukiwania plików wideo (/ wideo/wyszukiwania), a nie informacjami wideo (/ wideo/szczegóły) lub trendów wideo (/ wideo/umożliwia analizę trendów).