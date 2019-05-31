---
title: Punkty końcowe funkcji wyszukiwania wiadomości Bing
titlesuffix: Azure Cognitive Services
description: Podsumowanie punktu końcowego interfejsu API wyszukiwania wiadomości.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 6ea218da23d65696c76008cb15e183fcc4bbda10
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383225"
---
# <a name="bing-news-search-api-endpoints"></a>Punkty końcowe interfejsu API wyszukiwania wiadomości Bing

**Interfejsu API wyszukiwania wiadomości** zwraca artykułów, strony sieci Web, obrazy, wideo, wiadomości i [jednostek](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Jednostki zawierają podsumowanie informacji dotyczących osoby, miejsca lub tematu.

## <a name="endpoints"></a>Punkty końcowe

Aby uzyskać wiadomości wyników wyszukiwania za pomocą interfejsu API wyszukiwania wiadomości Bing, Wyślij `GET` żądania do jednego z następujących punktów końcowych. Nagłówki i parametry adresu URL dalsze Definiowanie specyfikacji.

### <a name="news-items-by-search-query"></a>Elementy wiadomości przez zapytanie wyszukiwania

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Zwraca elementy wiadomości oparty na zapytaniu przeszukiwania. Jeśli zapytanie wyszukiwania jest pusta, interfejs API zwróci najciekawsze artykuły z różnych kategorii. Wyślij zapytanie według adresu url kodowanie wyszukiwanego terminu i dodanie go do`q=""` parametru. Aby zapewnić dostępność, zobacz [obsługiwane kraje/regiony i rynków](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Elementy najważniejszych wiadomości według kategorii

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Zwraca elementy z najważniejszych wiadomości według kategorii. Konkretnie poprosić biznesowych Sport i rozrywka artykułów z wykorzystaniem `category=business`, `category=sports`, lub `category=entertainment`.  `category` Parametru należy używać tylko z `/news` adresu URL. Istnieją pewne wymagania formalny, do określania kategorii; Zapoznaj się `category` w [parametr zapytania](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) dokumentacji. Wyślij zapytanie według adresu url kodowanie wyszukiwanego terminu i dodanie go do`q=""` parametru. Aby zapewnić dostępność, zobacz [obsługiwane kraje/regiony i rynków](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Popularne tematy wiadomości 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Zwraca tematy wiadomości, które popularność w sieciach społecznościowych. Gdy `/trendingtopics` jest opcją, wyszukiwania Bing ignoruje kilka innych parametrów, takich jak `freshness` i `?q=""`. Aby zapewnić dostępność, zobacz [obsługiwane kraje/regiony i rynków](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać szczegółowe informacje o nagłówków, parametrów, kody na rynku, obiekty odpowiedzi, błędów itd., zobacz [wyszukiwania wiadomości Bing w wersji 7 interfejsu API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) odwołania.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez każdy punkt końcowy zobacz strony pomocy dla każdego typu.
Przykłady podstawowe żądań przy użyciu interfejsu API wyszukiwania wiadomości, zobacz [przewodniki szybkiego startu dla wyszukiwania wiadomości Bing](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
