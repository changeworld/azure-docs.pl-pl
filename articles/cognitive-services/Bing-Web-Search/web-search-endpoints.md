---
title: Punkt końcowy wyszukiwania w sieci Web
titleSuffix: Azure Cognitive Services
description: Aby uzyskać wyniki wyszukiwania `GET` w sieci Web, wyślij żądanie do następującego punktu końcowego. Nagłówki i parametry adresu URL definiują dalsze specyfikacje.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: 2edc6a4cf4d4fb174c1b2067d8b3fb3b6f31859f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74111337"
---
# <a name="web-search-endpoint"></a>Punkt końcowy wyszukiwania w sieci Web

Interfejs **API wyszukiwania w sieci Web** zwraca strony sieci Web, wiadomości, obrazy, klipy wideo i [encje](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Jednostki mają informacje podsumowujące o osobie, miejscu lub temacie.

## <a name="endpoint"></a>Endpoint

Aby uzyskać wyniki wyszukiwania w sieci Web `GET` przy użyciu interfejsu API Bing, wyślij żądanie do następującego punktu końcowego. Nagłówki i parametry adresu URL definiują dalsze specyfikacje.

**Punkt końcowy**: Zwraca wyniki sieci Web, które są `?q=""`istotne dla zapytania wyszukiwania użytkownika zdefiniowanego przez program .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Punkt końcowy: Aby uzyskać szczegółowe informacje na temat nagłówków, parametrów, kodów rynku, obiektów odpowiedzi, błędów i innych, zobacz odwołanie do [interfejsu API sieci Web Bing w wersji 7.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)

## <a name="response-json"></a>Odpowiedź JSON

Odpowiedź na żądanie wyszukiwania w sieci Web zawiera wszystkie wyniki jako obiekty JSON. Analizowanie wyniku wymaga procedur, które obsługują elementy każdego typu. Zobacz [przykłady samouczka](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) i [kodu źródłowego.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search)

## <a name="next-steps"></a>Następne kroki

Interfejsy API **usługi Bing** obsługują akcje wyszukiwania, które zwracają wyniki zgodnie z ich typem.Wszystkie punkty końcowe wyszukiwania zwracają wyniki jako obiekty odpowiedzi JSON. Wszystkie punkty końcowe obsługują zapytania, które zwracają określony język i lokalizację według długości geograficznej, szerokości geograficznej i promienia wyszukiwania.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez każdy punkt końcowy, zobacz strony odwołań dla każdego typu.
Aby zapoznać się z przykładami podstawowych żądań przy użyciu interfejsu API wyszukiwania w sieci Web, zobacz [Wyszukiwanie szybkich uruchomień w sieci Web](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
