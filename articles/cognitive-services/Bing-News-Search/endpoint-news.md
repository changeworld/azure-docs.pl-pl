---
title: Punkty końcowe funkcji wyszukiwania wiadomości Bing
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera podsumowanie punktów końcowych interfejsu API wyszukiwania wiadomości; aktualności, najważniejsze wiadomości i popularne wiadomości.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: dc7d16fe809e3e324f384b0d9e088dd7e6ab261c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74111487"
---
# <a name="bing-news-search-api-endpoints"></a>Punkty końcowe interfejsu API wyszukiwania wiadomości Bing

Interfejs **API wyszukiwania wiadomości** zwraca artykuły z wiadomościami, strony sieci Web, obrazy, klipy wideo i [encje](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Jednostki zawierają podsumowanie informacji o osobie, miejscu lub temacie.

## <a name="endpoints"></a>Punkty końcowe

Aby uzyskać wyniki wyszukiwania wiadomości przy użyciu interfejsu `GET` API wyszukiwania wiadomości Bing, wyślij żądanie do jednego z następujących punktów końcowych. Nagłówki i parametry adresu URL definiują dalsze specyfikacje.

### <a name="news-items-by-search-query"></a>Wiadomości według zapytania wyszukiwania

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Zwraca elementy wiadomości na podstawie zapytania wyszukiwania. Jeśli zapytanie wyszukiwania jest puste, interfejs API zwróci najważniejsze artykuły z różnych kategorii. Wyślij zapytanie przez adres URL kodowanie wyszukiwanego`q=""` terminu i dołączanie go do parametru. Aby uzyskać informacje o dostępności, zobacz [Obsługiwane kraje/regiony i rynki](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Najpopularniejsze wiadomości według kategorii

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Zwraca najważniejsze wiadomości według kategorii. Możesz w szczególności poprosić o najlepsze artykuły `category=business`biznesowe, sportowe lub rozrywkowe za pomocą , `category=sports`lub `category=entertainment`. Parametr `category` może być używany `/news` tylko z adresem URL. Istnieją pewne wymagania formalne dotyczące określania kategorii; odnoszą `category` się do w dokumentacji [parametrów kwerendy.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) Wyślij zapytanie przez adres URL kodowanie wyszukiwanego`q=""` terminu i dołączanie go do parametru. Aby uzyskać informacje o dostępności, zobacz [Obsługiwane kraje/regiony i rynki](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Popularne tematy z wiadomościami 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Zwraca tematy wiadomości, które są obecnie popularne w sieciach społecznościowych. Gdy `/trendingtopics` opcja jest dołączona, wyszukiwanie Bing ignoruje `freshness` kilka `?q=""`innych parametrów, takich jak i . Aby uzyskać informacje o dostępności, zobacz [Obsługiwane kraje/regiony i rynki](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje na temat nagłówków, parametrów, kodów rynku, obiektów odpowiedzi, błędów itp., zobacz odwołanie do [interfejsu API wyszukiwania wiadomości Bing w wersji 7.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez każdy punkt końcowy, zobacz strony odwołań dla każdego typu.
Aby zapoznać się z przykładami podstawowych żądań korzystających z interfejsu API wyszukiwania wiadomości, zobacz [Szybkie uruchamianie wyszukiwania wiadomości Bing](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
