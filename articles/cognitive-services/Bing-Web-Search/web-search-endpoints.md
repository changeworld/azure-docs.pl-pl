---
title: Punkt końcowy sieci Web wyszukiwania | Dokumentacja firmy Microsoft
description: Podsumowanie punkt końcowy interfejsu API sieci Web wyszukiwania.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: 72fbe1a0eb4379ad032e649f7299e37a0214adfb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346976"
---
# <a name="web-search-endpoint"></a>Punkt końcowy wyszukiwania w sieci Web
**Interfejsu API sieci Web wyszukiwania** zwraca stron sieci Web, grup dyskusyjnych, obrazów, klipów wideo, i [jednostek](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Jednostek zawiera podsumowanie informacji o osoby, miejsca lub tematu.
## <a name="endpoint"></a>Endpoint
Aby uzyskać wyniki wyszukiwania w sieci Web przy użyciu interfejsu API Bing, Wyślij `GET` żądanie następujący punkt końcowy. Nagłówki i parametry adresu URL dalsze Definiowanie specyfikacji.

**Punkt końcowy**: sieci Web zwraca wyniki, które mają zastosowanie do użytkownika wyszukiwania zdefiniowane przez zapytanie `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```
Punkt końcowy: Aby uzyskać więcej informacji o nagłówkach, parametry, rynku kody, obiekty odpowiedzi, błędów, itp., zobacz [w wersji 7 interfejsu API sieci Web Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) odwołania.

## <a name="response-json"></a>Odpowiedź JSON
Odpowiedź na żądania wyszukiwania w sieci Web obejmuje wszystkie wyniki jako obiektów JSON. Podczas analizowania wynik wymaga procedury obsługi elementów każdego typu. Zobacz [samouczek](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) i [kod źródłowy](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app-source) przykłady.

## <a name="next-steps"></a>Kolejne kroki
**Bing** interfejsów API obsługi akcji wyszukiwania, które zwracają wyniki zgodnie z ich typu. Wszystkie punkty końcowe wyszukiwania zwracają wyniki w postaci obiektów JSON odpowiedzi.  Wszystkie punkty końcowe obsługi zapytań, które zwracają określonego języka i/lub lokalizacji geograficznej, szerokość i wyszukiwania usługi radius.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez każdy punkt końcowy zobacz strony podręcznika dla każdego typu.
Przykłady podstawowego żądania za pomocą wyszukiwania w sieci Web interfejsu API, zobacz [wyszukiwania sieci Web szybkie — uruchamia](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).