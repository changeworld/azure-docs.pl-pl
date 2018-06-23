---
title: Punkty końcowe wideo wyszukiwania | Dokumentacja firmy Microsoft
description: Podsumowanie punkt końcowy interfejsu API wyszukiwania wideo.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 12/04/2017
ms.author: v-gedod
ms.openlocfilehash: 9836d9928362ab37b0a81ff5043d99f9bf353f22
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346949"
---
# <a name="video-search-endpoints"></a>Wideo wyszukiwania punktów końcowych.
**Interfejsu API Search wideo** obejmuje trzy punkty końcowe.  Punkt końcowy 1 zwraca filmy wideo z sieci Web na podstawie zapytania. Punkt końcowy 2 zwraca informacjami na temat wideo na podstawie `modules` parametr adresu URL.  Punkt końcowy 3 zwraca trendów obrazów.

## <a name="endpoints"></a>Punkty końcowe
Aby uzyskać wyniki wideo przy użyciu interfejsu API Bing, Wyślij `GET` żądania do jednego z następujących punktów końcowych. Umożliwia dalsze Definiowanie specyfikacji w nagłówki i parametry adresu URL.

**Punk końcowy 1:** zwraca pliki wideo, które mają zastosowanie do użytkownika zapytania wyszukiwania zdefiniowane przez `?q=""`.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```

**Punkt końcowy 2:** zwraca informacjami na temat wideo, takich jak powiązane pliki wideo. Obejmują `modules` [parametr zapytania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query-parameters), która jest rozdzielana przecinkami lista wgląd do żądania.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details
```

**Punkt końcowy 3:** zwraca wideo, które są umożliwia analizę trendów na podstawie wyszukiwania żądań wprowadzone przez innych użytkowników. Pliki wideo są podzielone na różne kategorie, na przykład na podstawie warte wymienienia osób lub zdarzeń.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending
```

Aby uzyskać więcej informacji dotyczących nagłówków, parametry, kody rynku, obiekty odpowiedzi, błędów itd., zobacz [w wersji 7 interfejsu API Bing wideo wyszukiwania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) odwołania.
## <a name="response-json"></a>Odpowiedź JSON
Odpowiedź na żądanie wyszukiwania plików wideo, dołącza wyniki, jako obiektów JSON. Przykłady wyników analizy można znaleźć [samouczek](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app) i [kod źródłowy](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app-source).

## <a name="next-steps"></a>Kolejne kroki
**Bing** interfejsów API obsługi akcji wyszukiwania, które zwracają wyniki zgodnie z ich typu. Wszystkie punkty końcowe wyszukiwania zwracają wyniki w postaci obiektów JSON odpowiedzi.  Wszystkie punkty końcowe obsługi zapytań, które zwracają określonego języka i/lub lokalizacji geograficznej, szerokość i wyszukiwania usługi radius.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez każdy punkt końcowy zobacz strony podręcznika dla każdego typu.
Przykłady podstawowego żądania za pomocą wyszukiwania wideo interfejsu API, zobacz [wyszukiwania wideo szybkie — uruchamia](https://docs.microsoft.com/azure/cognitive-services/bing-video-search).