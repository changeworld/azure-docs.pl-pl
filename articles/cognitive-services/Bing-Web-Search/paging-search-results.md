---
title: Jak przeglądać wyniki wyszukiwania — interfejsy API wyszukiwania Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeglądać wyniki wyszukiwania z interfejsów API wyszukiwania Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: ea883bb294a8769b3c9be1e0eafc2e3e7c811b48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481739"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Jak przeglądać wyniki z interfejsów API wyszukiwania Bing

Podczas wysyłania połączenia do interfejsów API sieci Web, Niestandardowe, Obraz, Wiadomości lub Wyszukiwanie wideo bing zwraca podzbiór całkowitej liczby wyników, które mogą być istotne dla kwerendy. Aby uzyskać szacunkową łączną liczbę dostępnych wyników, `totalEstimatedMatches` należy uzyskać dostęp do pola obiektu odpowiedzi. 

Przykład: 

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

## <a name="paging-through-search-results"></a>Stronicowanie za pośrednictwem wyników wyszukiwania

Aby przeglądać dostępne wyniki, `count` `offset` należy użyć parametrów i zapytań podczas wysyłania żądania.  

> [!NOTE]
>
> * Stronicowanie za pomocą interfejsów API wideo, obrazów i wiadomości`/video/search`Bing`/news/search`ma zastosowanie`/image/search`tylko do ogólnych wyszukiwań wideo ( ), wiadomości ( ) i obrazów ( ). Stronicowanie popularnych tematów i kategorii nie jest obsługiwane.  
> * To `TotalEstimatedMatches` pole jest szacowaną całkowitą liczbą wyników wyszukiwania dla bieżącej kwerendy. Po ustawieniu `count` `offset` parametrów i, oszacowanie to może ulec zmianie.

| Parametr | Opis                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Określa liczbę wyników do zwrócenia w odpowiedzi. Należy zauważyć, że `count`wartość domyślna programu , a maksymalna liczba wyników, które można zażądać, zależy od interfejsu API. Wartości te można znaleźć w dokumentacji referencyjnej w obszarze [Następne kroki](#next-steps). |
| `offset`  | Określa liczbę wyników do pominięcia. Jest `offset` zerowy i powinien być`totalEstimatedMatches` - `count`mniejszy niż ( ).                                           |

Na przykład, jeśli chcesz wyświetlić 15 wyników na `count` stronie, należy `offset` ustawić na 15 i 0, aby uzyskać pierwszą stronę wyników. Dla każdego kolejnego wywołania interfejsu `offset` API należy przyrost o 15. W poniższym przykładzie żąda 15 stron sieci Web, począwszy od przesunięcia 45.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Jeśli używasz `count` wartości domyślnej, wystarczy `offset` określić parametr kwerendy w wywołaniach interfejsu API.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Korzystając z interfejsów API obrazu bing i `nextOffset` wideo, można użyć tej wartości, aby uniknąć zduplikowanych wyników wyszukiwania. Pobierz wartość z `Images` `Videos` obiektów lub odpowiedzi i używać go `offset` w żądaniach z parametrem.  

> [!NOTE]
> Interfejs API wyszukiwania w sieci Web Bing zwraca wyniki wyszukiwania, które mogą zawierać strony internetowe, obrazy, klipy wideo i wiadomości. Podczas tworzenia strony wyników wyszukiwania z interfejsu API wyszukiwania w sieci Web Bing są stronicowania tylko strony sieci Web , a nie inne [typy](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)odpowiedzi, takie jak obrazy lub wiadomości. Wyniki wyszukiwania `WebPage` w obiektach mogą zawierać wyniki, które pojawiają się również w innych typach odpowiedzi.
>
> Jeśli używasz `responseFilter` parametru kwerendy bez określania żadnych `count` wartości `offset` filtru, nie należy używać parametrów i parametrów. 

## <a name="next-steps"></a>Następne kroki

* [Co to są interfejsy API wyszukiwania w sieci Web usługi Bing?](bing-api-comparison.md)
* [Dokumentacja interfejsu API wyszukiwania w sieci Web Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Odwołanie do interfejsu API wyszukiwania niestandardowego bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Odwołanie do interfejsu API wyszukiwania wiadomości Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Odwołanie do interfejsu API wyszukiwania wideo bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Odwołanie do interfejsu API wyszukiwania obrazów Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
