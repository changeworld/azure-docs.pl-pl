---
title: Punkty końcowe wyszukiwania klipów wideo — wyszukiwania wideo Bing
titlesuffix: Azure Cognitive Services
description: Podsumowanie punktu końcowego interfejsu API wyszukiwania wideo Bing.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: rosh
ms.openlocfilehash: c153f577f76944d22f9a1b0fb4b24d332d2a02c8
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220775"
---
# <a name="video-search-endpoints"></a>Wideo Wyszukaj punkty końcowe
**Interfejsu API wyszukiwania wideo** obejmuje trzy punkty końcowe.  Punkt końcowy 1 zwraca filmy wideo z sieci Web na podstawie zapytania. Punkt końcowy 2 Zwraca szczegółowe informacje dotyczące filmu wideo, na podstawie `modules` parametr adresu URL.  Punkt końcowy 3 zwraca popularnych obrazów.

## <a name="endpoints"></a>Punkty końcowe
Aby uzyskać wyniki wideo przy użyciu interfejsu API usługi Bing, Wyślij `GET` żądania do jednego z następujących punktów końcowych. Umożliwia dalsze Definiowanie specyfikacji w nagłówki i parametry adresu URL.

**Punk końcowy 1:** zwraca filmów wideo, które są istotne dla zapytania wyszukiwania użytkownika zdefiniowane przez `?q=""`.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```

**Punkt końcowy 2:** zwraca szczegółowe informacje dotyczące filmu wideo, takich jak powiązane filmy wideo. Obejmują `modules` [parametr zapytania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query-parameters), który jest rozdzielana przecinkami lista wglądu w szczegółowe dane na żądanie.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details
```

**Punkt końcowy 3:** zwraca filmów wideo, które stają się popularne na podstawie wyszukiwania żądań wykonywanymi przez innych użytkowników. Filmy wideo są podzielone na różne kategorie, na przykład, na podstawie warte zauważenia osób lub zdarzenia.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending
```

Aby uzyskać szczegółowe informacje o nagłówków, parametrów, kody na rynku, obiekty odpowiedzi, błędów itd., zobacz [interfejsu API wyszukiwania wideo Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) odwołania.
## <a name="response-json"></a>Odpowiedź JSON
Odpowiedź na żądanie wyszukiwania filmów wideo zawiera wyniki jako obiekty JSON. Zobacz przykłady podczas analizowania wyników [samouczek](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app) i [kod źródłowy](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app-source).

## <a name="next-steps"></a>Kolejne kroki
**Bing** interfejsy API obsługują akcji wyszukiwania, które zwracają wyniki według ich typu. Wszystkie punkty końcowe wyszukiwania zwracają wyniki w postaci obiektów odpowiedzi JSON.  Wszystkie punkty końcowe obsługują zapytań, które zwracają określonego języka i/lub lokalizacji długość geograficzna, szerokość i wyszukiwania usługi radius.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez każdy punkt końcowy zobacz strony pomocy dla każdego typu.
Przykłady podstawowe żądań przy użyciu interfejsu API wyszukiwania wideo, zobacz [przewodniki szybkiego startu dla wyszukiwania wideo](https://docs.microsoft.com/azure/cognitive-services/bing-video-search).