---
title: Jak na stronie Wyniki wyszukiwania — interfejsy API wyszukiwania Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak na stronie Wyniki wyszukiwania interfejsy API wyszukiwania Bing.
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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481739"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Jak uzyskać stronę za pośrednictwem wyników z interfejsy API wyszukiwania Bing

W przypadku wysyłania wywołania do interfejsów API sieci Web, niestandardowych, obrazów, wiadomości lub wyszukiwanie wideo, Bing zwraca podzestaw łącznej liczby wyników, które mogą być odpowiednie dla zapytania. Aby uzyskać szacowaną łączną liczbę dostępnych wyników, uzyskaj dostęp do pola `totalEstimatedMatches` obiektu odpowiedzi. 

Na przykład: 

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

## <a name="paging-through-search-results"></a>Stronicowanie za poorednictwem wyników wyszukiwania

Aby uzyskać dostęp do strony za pomocą dostępnych wyników, użyj parametrów zapytania `count` i `offset` podczas wysyłania żądania.  

> [!NOTE]
>
> * Stronicowanie za pomocą interfejsów API wideo, obrazów i wiadomości Bing dotyczy tylko ogólnych plików wideo (`/video/search`), wiadomości (`/news/search`) i wyszukiwania obrazów (`/image/search`). Stronicowanie za poorednictwem tematów i kategorii trendów nie jest obsługiwane.  
> * Pole `TotalEstimatedMatches` jest oszacowaniem łącznej liczby wyników wyszukiwania dla bieżącego zapytania. Po ustawieniu parametrów `count` i `offset`, to oszacowanie może ulec zmianie.

| Parametr | Opis                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Określa liczbę wyników do zwrócenia w odpowiedzi. Należy zauważyć, że wartość domyślna `count`i Maksymalna liczba wyników, które może zażądać, zależy od interfejsu API. Te wartości można znaleźć w dokumentacji referencyjnej w sekcji [następne kroki](#next-steps). |
| `offset`  | Określa liczbę wyników do pominięcia. `offset` jest liczony od zera i musi być krótszy niż (`totalEstimatedMatches` - `count`).                                           |

Jeśli na przykład chcesz wyświetlić 15 wyników na stronie, ustaw wartość `count` na 15, a `offset` na 0, aby uzyskać pierwszą stronę wyników. Dla każdego kolejnego wywołania interfejsu API zwiększa się `offset` o 15. Poniższy przykład żąda 15 stron sieci Web zaczynających się od przesunięcia 45.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

W przypadku użycia domyślnej wartości `count` wystarczy określić parametr zapytania `offset` w wywołaniach interfejsu API.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

W przypadku używania obrazów Bing i interfejsów API wideo można użyć wartości `nextOffset`, aby uniknąć zduplikowanych wyników wyszukiwania. Pobierz wartość z obiektów odpowiedzi `Images` lub `Videos` i użyj jej w żądaniach za pomocą parametru `offset`.  

> [!NOTE]
> Interfejs API wyszukiwania w sieci Web Bing zwraca wyniki wyszukiwania, które mogą obejmować strony sieci Web, obrazy, filmy wideo i wiadomości. Podczas przeglądania wyników wyszukiwania w interfejs API wyszukiwania w sieci Web Bing są stronicowane tylko [strony sieci Web](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage), a nie inne typy odpowiedzi, takie jak obrazy lub wiadomości. Wyniki wyszukiwania w obiektach `WebPage` mogą zawierać również wyniki, które są wyświetlane w innych typach odpowiedzi.
>
> Jeśli używasz `responseFilter` parametru zapytania bez określenia żadnych wartości filtru, nie używaj parametrów `count` i `offset`. 

## <a name="next-steps"></a>Następne kroki

* [Jakie są wyszukiwanie w sieci Web Bing interfejsy API?](bing-api-comparison.md)
* [Dokumentacja interfejsu API wyszukiwania w sieci Web Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [interfejs API wyszukiwania niestandardowego Bing odwołanie wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [interfejs API wyszukiwania wiadomości Bing odwołanie wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [interfejs API wyszukiwania wideo Bing odwołanie wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [interfejs API wyszukiwania obrazów Bing odwołanie wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
