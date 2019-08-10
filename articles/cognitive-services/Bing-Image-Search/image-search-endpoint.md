---
title: Punkty końcowe dla interfejs API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Lista dostępnych punktów końcowych dla interfejs API wyszukiwania obrazów Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: fc1a0670767b134ad6e330fb41fc5564c754d91c
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883419"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Punkty końcowe dla interfejs API wyszukiwania obrazów Bing

**Interfejs API wyszukiwanie obrazów** zawiera trzy punkty końcowe.  Punkt końcowy 1 zwraca obrazy z sieci Web na podstawie zapytania. Punkt końcowy 2 zwraca [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse).  Punkt końcowy 3 zwraca obrazy trendów.

## <a name="endpoints"></a>Punkty końcowe

Aby uzyskać wyniki obrazu przy użyciu interfejsu API Bing, Wyślij żądanie do jednego z następujących punktów końcowych. Użyj nagłówków i parametrów adresu URL, aby zdefiniować dalsze specyfikacje.

**Punkt końcowy 1:** Zwraca obrazy, które są istotne dla zapytania wyszukiwania użytkownika zdefiniowanego przez `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Punkt końcowy 2:** Zwraca szczegółowe informacje o obrazie przy użyciu `GET` albo lub. `POST`
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Żądanie GET zwraca szczegółowe informacje o obrazie, takie jak strony sieci Web, które zawierają obraz. Dołącz `GET` parametr [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) do żądania.

Można również dołączyć obraz binarny w treści `POST` żądania i ustawić parametr [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) na. `RecognizedEntities` Spowoduje to zwrócenie [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) do użycia jako parametru w kolejnym `GET` żądaniu, które zwraca informacje o osobach w obrazie.  Ustaw `modules` , `All` Aby uzyskać wszystkie szczegółowe informacje, z wyjątkiem `RecognizedEntities` wyników `POST` bez tworzenia innego wywołania przy użyciu `insightsToken`.


**Punkt końcowy 3:** Zwraca obrazy, które są trendem na podstawie żądań wyszukiwania wykonywanych przez inne osoby. Obrazy są rozdzielone na różne kategorie, na przykład na podstawie wartych osób lub wydarzeń.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Aby zapoznać się z listą rynków, które obsługują trendy w obrazach, zobacz [trendy obrazów](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Aby uzyskać szczegółowe informacje o nagłówkach, parametrach, kodach rynku, obiektach odpowiedzi, błędach itp., zobacz [interfejs API wyszukiwania obrazów Bing odwołanie wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) .
## <a name="response-json"></a>KOD JSON odpowiedzi
Odpowiedź na żądanie wyszukiwania obrazów zawiera wyniki jako obiekty JSON. Przykłady analizy wyników można znaleźć w samouczku [](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) i [kodzie źródłowym](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Następne kroki
Interfejsy API **Bing** obsługują akcje wyszukiwania, które zwracają wyniki zgodnie z ich typem. Wszystkie punkty końcowe wyszukiwania zwracają wyniki jako obiekty odpowiedzi JSON.  Wszystkie punkty końcowe obsługują zapytania, które zwracają określony język i/lub lokalizację według długości geograficznej, szerokości geograficznej i usługi wyszukiwania.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez poszczególne punkty końcowe, zobacz strony referencyjne dla każdego typu.
Przykłady podstawowych żądań przy użyciu interfejsu API wyszukiwania obrazów można znaleźć w temacie [Wyszukiwanie obrazów szybkie uruchamianie](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).
