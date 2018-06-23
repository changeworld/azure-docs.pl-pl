---
title: Obraz wyszukiwania punktów końcowych | Dokumentacja firmy Microsoft
description: Podsumowanie punkt końcowy interfejsu API wyszukiwania obrazu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 11/30/2017
ms.author: v-gedod
ms.openlocfilehash: 0d5f28bfdb45b27b04df068f75e8a20d0235d12b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346884"
---
# <a name="image-search-endpoints"></a>Punkty końcowe wyszukiwania obrazu
**Interfejsu API Search obrazu** obejmuje trzy punkty końcowe.  Punkt końcowy 1 zwraca obrazów z sieci Web na podstawie zapytania. Zwraca punkt końcowy 2 [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse).  Punkt końcowy 3 zwraca trendów obrazów.
## <a name="endpoints"></a>Punkty końcowe
Aby uzyskać wyniki obrazu przy użyciu interfejsu API Bing, Wyślij żądanie do jednej z następujących punktów końcowych. Umożliwia dalsze Definiowanie specyfikacji w nagłówki i parametry adresu URL.

**Punkt końcowy 1:** zwraca obrazów, które mają zastosowanie do użytkownika zapytania wyszukiwania zdefiniowane przez `?q=""`.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Punkt końcowy 2:** zwraca szczegółowe informacje o obrazie, za pomocą `GET` lub `POST`.
``` 
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Żądanie GET zwraca szczegółowe informacje o obrazie, takich jak strony sieci Web, które zawierają obrazu. Obejmują [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) parametr `GET` żądania.

Lub obraz binarny można uwzględnić w treści `POST` żądania i ustawić [modułów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) parametr `RecognizedEntities`. Spowoduje to zwrócenie [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v5-reference#insightstoken) można użyć jako parametru w kolejnej `GET` żądania, która zwraca informacje o osobach w obrazie.  Ustaw `modules` do `All` Aby uzyskać wszystkie szczegółowe informacje, z wyjątkiem `RecognizedEntities` w wynikach `POST` bez wykonywania wywołań przy użyciu `insightsToken`. 


**Punkt końcowy 3:** zwraca obrazów, które są umożliwia analizę trendów na podstawie wyszukiwania żądań wprowadzone przez innych użytkowników. Obrazy są podzielone na różne kategorie, na przykład na podstawie warte wymienienia osób lub zdarzeń.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Aby uzyskać listę rynkach, które obsługuje trendów obrazów, zobacz [umożliwia analizę trendów obrazów](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Aby uzyskać więcej informacji dotyczących nagłówków, parametry, kody rynku, obiekty odpowiedzi, błędów itd., zobacz [interfejsu API Bing obraz wyszukiwania w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) odwołania.
## <a name="response-json"></a>Odpowiedź JSON
Odpowiedzi na żądanie wyszukiwania obrazu, dołącza wyniki, jako obiektów JSON. Przykłady wyników analizy można znaleźć [samouczek](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) i [kod źródłowy](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Kolejne kroki
**Bing** interfejsów API obsługi akcji wyszukiwania, które zwracają wyniki zgodnie z ich typu. Wszystkie punkty końcowe wyszukiwania zwracają wyniki w postaci obiektów JSON odpowiedzi.  Wszystkie punkty końcowe obsługi zapytań, które zwracają określonego języka i/lub lokalizacji geograficznej, szerokość i wyszukiwania usługi radius.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez każdy punkt końcowy zobacz strony podręcznika dla każdego typu.
Przykłady podstawowe żądań, używając przycisku Wyszukaj obraz interfejsu API, zobacz [wyszukiwania obrazu szybkie — uruchamia](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).