---
title: Punkt końcowy wyszukiwania w sieci Web
titleSuffix: Azure Cognitive Services
description: Podsumowanie punktu końcowego interfejsu API wyszukiwania w sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: e91f798e6bfae33f8f4c8b5aa2d0f6ddc0047389
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65798267"
---
# <a name="web-search-endpoint"></a>Punkt końcowy wyszukiwania w sieci Web

**API wyszukiwania w Internecie** zwraca stron sieci Web, grup dyskusyjnych, obrazy, wideo, a [jednostek](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Jednostki mają informacje podsumowania osoby, miejsca lub tematu.

## <a name="endpoint"></a>Endpoint

Aby uzyskać wyniki wyszukiwania w Internecie przy użyciu interfejsu API usługi Bing, Wyślij `GET` żądanie następujący punkt końcowy. Nagłówki i parametry adresu URL dalsze Definiowanie specyfikacji.

**Punkt końcowy**: Zwraca wyniki z Internetu, które są istotne dla zapytania wyszukiwania użytkownika zdefiniowane przez `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Punkt końcowy: Aby uzyskać szczegółowe informacje na temat nagłówków, parametry, kody na rynku, obiekty odpowiedzi, błędów i innych, zobacz [interfejsu API sieci Web Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) odwołania.

## <a name="response-json"></a>Response JSON

Odpowiedź na żądanie wyszukiwania w sieci Web zawiera wszystkie wyniki jako obiekty JSON. Analizowanie wyników wymaga procedur, które obsługują elementy każdego typu. Zobacz [samouczek](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) i [kod źródłowy](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) przykłady.

## <a name="next-steps"></a>Kolejne kroki

**Bing** interfejsy API obsługują akcji wyszukiwania, które zwracają wyniki według ich typu. Wszystkie punkty końcowe wyszukiwania zwracają wyniki w postaci obiektów odpowiedzi JSON.  Wszystkie punkty końcowe obsługują zapytań, które zwracają określonego języka i lokalizacji długość geograficzna, szerokość i wyszukiwania usługi radius.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez każdy punkt końcowy zobacz strony pomocy dla każdego typu.
Przykłady podstawowe żądania za pomocą wyszukiwania w sieci Web interfejsu API, zobacz [wyszukiwania w sieci Web przewodniki szybkiego startu](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
