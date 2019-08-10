---
title: Punkt końcowy wyszukiwania w sieci Web
titleSuffix: Azure Cognitive Services
description: Podsumowanie punktu końcowego interfejsu API wyszukiwania w sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: be622c5636c253c48bec4d67fba58319262c2603
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883560"
---
# <a name="web-search-endpoint"></a>wyszukiwanie w sieci Web punkt końcowy

**Interfejs API wyszukiwanie w sieci Web** zwraca strony sieci Web, wiadomości, obrazy, wideo i [jednostki](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Jednostki zawierają informacje podsumowujące dotyczące osoby, miejsca lub tematu.

## <a name="endpoint"></a>Endpoint

Aby uzyskać wyniki wyszukiwania w sieci Web przy użyciu interfejsu API Bing `GET` , Wyślij żądanie do poniższego punktu końcowego. Nagłówki i parametry URL definiują dalsze specyfikacje.

**Punkt końcowy**: Zwraca wyniki sieci Web, które są istotne dla zapytania wyszukiwania użytkownika zdefiniowanego `?q=""`przez.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Punktu końcowego Aby uzyskać szczegółowe informacje o nagłówkach, parametrach, kodach rynku, obiektach odpowiedzi, błędach i innych, zobacz Dokumentacja [interfejsu API sieci Web Bing wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) .

## <a name="response-json"></a>KOD JSON odpowiedzi

Odpowiedź na żądanie wyszukiwania w sieci Web obejmuje wszystkie wyniki jako obiekty JSON. Analizowanie wyniku wymaga procedur, które obsługują elementy każdego typu. Przykłady można [](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) znaleźć w samouczku i [kodzie źródłowym](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) .

## <a name="next-steps"></a>Następne kroki

Interfejsy API **Bing** obsługują akcje wyszukiwania, które zwracają wyniki zgodnie z ich typem. Wszystkie punkty końcowe wyszukiwania zwracają wyniki jako obiekty odpowiedzi JSON.  Wszystkie punkty końcowe obsługują zapytania, które zwracają określony język i lokalizację według długości geograficznej, szerokości geograficznej i usługi wyszukiwania.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez poszczególne punkty końcowe, zobacz strony referencyjne dla każdego typu.
Przykłady podstawowych żądań za pomocą interfejsu API wyszukiwania w sieci Web można znaleźć [w temacie wyszukiwanie w sieci Web — szybki start](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
