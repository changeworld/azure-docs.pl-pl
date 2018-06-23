---
title: Punkty końcowe wyszukiwania wiadomości Bing | Dokumentacja firmy Microsoft
description: Podsumowanie punkt końcowy interfejsu API wyszukiwania wiadomości.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: b8ae99698926a818bf22b0b4027af8610413aaa6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346957"
---
# <a name="news-search-endpoints"></a>Punkty końcowe wyszukiwania wiadomości
**API wyszukiwania wiadomości** zwraca artykuły, stron sieci Web, obrazów, klipów wideo, wiadomości i [jednostek](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Jednostek zawiera podsumowanie informacji o osoby, miejsca lub tematu. 
## <a name="endpoints"></a>Punkty końcowe
Aby uzyskać wyniki wyszukiwania wiadomości przy użyciu interfejsu API Bing, Wyślij `GET` żądania do jednego z następujących punktów końcowych. Nagłówki i parametry adresu URL dalsze Definiowanie specyfikacji.

**Punkt końcowy 1:** zwraca wiadomości na podstawie zapytania wyszukiwania użytkownika. Jeśli zapytanie wyszukiwania jest pusta, wywołanie zwraca artykuły najnowsze wiadomości. Zapytanie `?q=""` opcji można również używać razem `/news` adresu URL. Dostępności, zobacz [obsługiwanych krajach i rynkach](supported-countries-markets.md#supported-markets-for-news-search-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search 
``` 

**Punkt końcowy 2:** zwraca najnowsze wiadomości elementów według kategorii. W szczególności mogą żądać biznesowych, sportowych lub Rozrywka artykułów z wykorzystaniem `category=business`, `category=sports`, lub `category=entertainment`.  `category` Parametru można używać tylko z `/news` adresu URL. Istnieją pewne posiadanie wymagania dotyczące określania kategorii; Zapoznaj się `category` w [parametr zapytania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters) dokumentacji. Dostępności, zobacz [obsługiwanych krajach i rynkach](supported-countries-markets.md#supported-markets-for-news-endpoint). 
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

**Punkt końcowy 3:** zwraca wiadomości tematów, które są aktualnie umożliwia analizę trendów w sieciach społecznościowych. Gdy `/trendingtopics` znajduje się opcja, wyszukiwania usługi Bing ignoruje kilka innych parametrów, takich jak `freshness` i `?q=""`. Dostępności, zobacz [obsługiwanych krajach i rynkach](supported-countries-markets.md#supported-markets-for-news-trending-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics 
``` 

Aby uzyskać więcej informacji dotyczących nagłówków, parametry, kody rynku, obiekty odpowiedzi, błędów itd., zobacz [wyszukiwania wiadomości Bing interfejsu API w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) odwołania.
## <a name="response-json"></a>Odpowiedź JSON
Odpowiedź na żądanie wyszukiwania wiadomości, dołącza wyniki, jako obiektów JSON. Analizowanie wyników wymaga procedury obsługi elementów każdego typu. Zobacz [samouczek](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app) i [kod źródłowy](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app-source) przykłady.

## <a name="next-steps"></a>Kolejne kroki
**Bing** interfejsów API obsługi akcji wyszukiwania, które zwracają wyniki zgodnie z ich typu. Wszystkie punkty końcowe wyszukiwania zwracają wyniki w postaci obiektów JSON odpowiedzi.  Wszystkie punkty końcowe obsługi zapytań, które zwracają określonego języka i/lub lokalizacji geograficznej, szerokość i wyszukiwania usługi radius.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez każdy punkt końcowy zobacz strony podręcznika dla każdego typu.
Przykłady podstawowego żądania za pomocą wyszukiwania wiadomości interfejsu API, zobacz [wyszukiwania wiadomości Bing szybkie — uruchamia](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).