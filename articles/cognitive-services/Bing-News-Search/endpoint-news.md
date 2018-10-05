---
title: Punkty końcowe funkcji wyszukiwania wiadomości Bing
titlesuffix: Azure Cognitive Services
description: Podsumowanie punktu końcowego interfejsu API wyszukiwania wiadomości.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: 0ed8b9048c04c4aff5214cea697810a0c573559e
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48800549"
---
# <a name="bing-news-search-endpoints"></a>Punkty końcowe funkcji wyszukiwania wiadomości Bing
**Interfejsu API wyszukiwania wiadomości** zwraca artykułów, strony sieci Web, obrazy, wideo, wiadomości i [jednostek](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Jednostki zawierają podsumowanie informacji dotyczących osoby, miejsca lub tematu.
## <a name="endpoints"></a>Punkty końcowe
Aby uzyskać wyniki wyszukiwania wiadomości przy użyciu interfejsu API usługi Bing, Wyślij `GET` żądania do jednego z następujących punktów końcowych. Nagłówki i parametry adresu URL dalsze Definiowanie specyfikacji.

**Punkt końcowy 1:** zwraca elementy wiadomości oparty na zapytaniu przeszukiwania przez użytkownika. Jeśli zapytanie wyszukiwania jest pusta, to wywołanie zwraca artykuły najważniejszych wiadomości. Zapytanie `?q=""` opcji można używać z `/news` adresu URL. Aby zapewnić dostępność, zobacz [obsługiwane kraje i rynków](language-support.md#supported-markets-for-news-search-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

**Punkt końcowy 2:** zwraca elementy z najważniejszych wiadomości według kategorii. Konkretnie poprosić biznesowych Sport i rozrywka artykułów z wykorzystaniem `category=business`, `category=sports`, lub `category=entertainment`.  `category` Parametru należy używać tylko z `/news` adresu URL. Istnieją pewne wymagania formalny, do określania kategorii; Zapoznaj się `category` w [parametr zapytania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters) dokumentacji. Aby zapewnić dostępność, zobacz [obsługiwane kraje i rynków](language-support.md#supported-markets-for-news-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

**Punkt końcowy 3:** zwraca wiadomości tematów, które popularność w sieciach społecznościowych. Gdy `/trendingtopics` jest opcją, wyszukiwania Bing ignoruje kilka innych parametrów, takich jak `freshness` i `?q=""`. Aby zapewnić dostępność, zobacz [obsługiwane kraje i rynków](language-support.md#supported-markets-for-news-trending-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Aby uzyskać szczegółowe informacje o nagłówków, parametrów, kody na rynku, obiekty odpowiedzi, błędów itd., zobacz [wyszukiwania wiadomości Bing w wersji 7 interfejsu API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) odwołania.
## <a name="response-json"></a>Odpowiedź JSON
Odpowiedź na żądanie wyszukiwania wiadomości zawiera wyniki jako obiekty JSON. Analizowanie wyników wymaga procedur, które obsługują elementy każdego typu. Zobacz [samouczek](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app) i [kod źródłowy](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app-source) przykłady.

## <a name="next-steps"></a>Kolejne kroki
**Bing** interfejsy API obsługują akcji wyszukiwania, które zwracają wyniki według ich typu. Wszystkie punkty końcowe wyszukiwania zwracają wyniki w postaci obiektów odpowiedzi JSON.  Wszystkie punkty końcowe obsługują zapytań, które zwracają określonego języka i/lub lokalizacji długość geograficzna, szerokość i wyszukiwania usługi radius.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez każdy punkt końcowy zobacz strony pomocy dla każdego typu.
Przykłady podstawowe żądań przy użyciu interfejsu API wyszukiwania wiadomości, zobacz [przewodniki szybkiego startu dla wyszukiwania wiadomości Bing](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
