---
title: Punkty końcowe dla API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Lista dostępnych punktów końcowych interfejsu API wyszukiwania obrazów Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 076dbb26c267cf65a0d6f3a9835375b09951f2aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388515"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Punkty końcowe dla API wyszukiwania obrazów Bing

**Interfejsu API wyszukiwania obrazów** obejmuje trzy punkty końcowe.  Punkt końcowy 1 zwraca obrazów z sieci Web na podstawie zapytania. Zwraca punkt końcowy 2 [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse).  Punkt końcowy 3 zwraca popularnych obrazów.

## <a name="endpoints"></a>Punkty końcowe

Aby uzyskać wyniki obraz przy użyciu interfejsu API usługi Bing, Wyślij żądanie do jednej z następujących punktów końcowych. Umożliwia dalsze Definiowanie specyfikacji w nagłówki i parametry adresu URL.

**Punkt końcowy 1:** Zwraca obrazów, które są istotne dla zapytania wyszukiwania użytkownika zdefiniowane przez `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Punkt końcowy 2:** Zwraca szczegółowe informacje o obrazie za pomocą `GET` lub `POST`.
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Żądanie pobrania zwraca szczegółowe informacje o pliku obrazu, takich jak strony sieci Web, które obejmują obraz. Obejmują [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) parametrem `GET` żądania.

Lub może zawierać obrazów binarnych w treści `POST` żądania i ustaw [modułów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) parametr `RecognizedEntities`. Spowoduje to zwrócenie [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) można użyć jako parametru w kolejnej `GET` żądanie, która zwraca informacje o osobach w obrazie.  Ustaw `modules` do `All` można pobrać wszystkie szczegółowe informacje, z wyjątkiem `RecognizedEntities` w wynikach `POST` bez konieczności szukania innego przy użyciu wywołania `insightsToken`.


**Punkt końcowy 3:** Zwraca obrazów, które stają się popularne na podstawie żądania wyszukiwania wykonywanymi przez innych użytkowników. Obrazy są podzielone na różne kategorie, na przykład, na podstawie warte zauważenia osób lub zdarzenia.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Aby uzyskać listę rynków, które obsługują popularnych obrazów, zobacz [popularne obrazy](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Aby uzyskać szczegółowe informacje o nagłówków, parametrów, kody na rynku, obiekty odpowiedzi, błędów itd., zobacz [interfejsu API wyszukiwania obrazów Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) odwołania.
## <a name="response-json"></a>Response JSON
Odpowiedź na żądanie wyszukiwania obrazów zawiera wyniki jako obiekty JSON. Zobacz przykłady podczas analizowania wyników [samouczek](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) i [kod źródłowy](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Kolejne kroki
**Bing** interfejsy API obsługują akcji wyszukiwania, które zwracają wyniki według ich typu. Wszystkie punkty końcowe wyszukiwania zwracają wyniki w postaci obiektów odpowiedzi JSON.  Wszystkie punkty końcowe obsługują zapytań, które zwracają określonego języka i/lub lokalizacji długość geograficzna, szerokość i wyszukiwania usługi radius.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez każdy punkt końcowy zobacz strony pomocy dla każdego typu.
Przykłady podstawowe żądań przy użyciu interfejsu API wyszukiwania obrazów, zobacz [wyszukiwania obrazów, przewodniki szybkiego startu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).
