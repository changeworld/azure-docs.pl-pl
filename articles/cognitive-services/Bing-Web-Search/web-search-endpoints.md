---
title: Punkt końcowy wyszukiwania w sieci Web
titleSuffix: Azure Cognitive Services
description: Podsumowanie punktu końcowego interfejsu API wyszukiwania w sieci Web.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: 3058ca6cf0eb99486dd4c269d43b274fb367f7a9
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46125463"
---
# <a name="web-search-endpoint"></a>Punkt końcowy wyszukiwania w sieci Web
**API wyszukiwania w Internecie** zwraca stron sieci Web, grup dyskusyjnych, obrazy, wideo, a [jednostek](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Jednostki zawierają podsumowanie informacji dotyczących osoby, miejsca lub tematu.
## <a name="endpoint"></a>Endpoint
Aby uzyskać wyniki wyszukiwania w Internecie przy użyciu interfejsu API usługi Bing, Wyślij `GET` żądanie następujący punkt końcowy. Nagłówki i parametry adresu URL dalsze Definiowanie specyfikacji.

**Punkt końcowy**: wyniki z Internetu zwraca, które są istotne dla użytkownika wyszukiwania zapytanie zdefiniowane przez `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```
Punkt końcowy: Szczegółowe informacje na temat nagłówki i parametry, rynek kodów, obiekty odpowiedzi, błędy itp., zobacz [interfejsu API sieci Web Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) odwołania.

## <a name="response-json"></a>Odpowiedź JSON
Odpowiedź na żądanie wyszukiwania w sieci Web zawiera wszystkie wyniki jako obiekty JSON. Analizowanie wyników wymaga procedur, które obsługują elementy każdego typu. Zobacz [samouczek](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) i [kod źródłowy](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app-source) przykłady.

## <a name="next-steps"></a>Kolejne kroki
**Bing** interfejsy API obsługują akcji wyszukiwania, które zwracają wyniki według ich typu. Wszystkie punkty końcowe wyszukiwania zwracają wyniki w postaci obiektów odpowiedzi JSON.  Wszystkie punkty końcowe obsługują zapytań, które zwracają określonego języka i/lub lokalizacji długość geograficzna, szerokość i wyszukiwania usługi radius.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez każdy punkt końcowy zobacz strony pomocy dla każdego typu.
Przykłady podstawowe żądania za pomocą wyszukiwania w sieci Web interfejsu API, zobacz [wyszukiwania w sieci Web przewodniki szybkiego startu](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
