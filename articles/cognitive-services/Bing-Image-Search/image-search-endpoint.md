---
title: Punkty końcowe interfejsu API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Interfejs API wyszukiwania obrazów zawiera trzy punkty końcowe. Punkt końcowy 1 zwraca obrazy z sieci Web. Punkt końcowy 2 zwraca obrazy.Endpoint 2 returns ImageInsights. Punkt końcowy 3 zwraca popularne obrazy.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 38416f6a580d270aefc287de0c198bd418a44db9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072626"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Punkty końcowe interfejsu API wyszukiwania obrazów Bing

**Interfejs API wyszukiwania obrazów** zawiera trzy punkty końcowe.  Punkt końcowy 1 zwraca obrazy z sieci Web na podstawie kwerendy. Punkt końcowy 2 zwraca [obrazy.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse)  Punkt końcowy 3 zwraca popularne obrazy.

## <a name="endpoints"></a>Punkty końcowe

Aby uzyskać wyniki obrazu przy użyciu interfejsu API Bing, wyślij żądanie do jednego z następujących punktów końcowych. Użyj nagłówków i parametrów adresu URL, aby zdefiniować dalsze specyfikacje.

**Punkt końcowy 1:** Zwraca obrazy, które są istotne dla zapytania `?q=""`wyszukiwania użytkownika zdefiniowane przez program .
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Punkt końcowy 2:** Zwraca szczegółowe informacje o obrazie, używając jednego `GET` lub `POST`.
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Żądanie GET zwraca szczegółowe informacje o obrazie, takie jak strony sieci Web, które zawierają obraz. Dołącz [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) parametr `GET` z żądaniem.

Można też dołączyć obraz binarny w `POST` treści żądania i ustawić `RecognizedEntities`parametr [modułów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) na . Spowoduje to zwrócenie [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) do użycia jako `GET` parametr w kolejnym żądaniu, który zwraca informacje o osobach na obrazie.  `modules` Ustaw, `All` aby uzyskać wszystkie `RecognizedEntities` spostrzeżenia, `POST` z wyjątkiem wyników `insightsToken`bez wykonywania innego połączenia za pomocą .


**Punkt końcowy 3:** Zwraca obrazy, które są popularne na podstawie żądań wyszukiwania składanych przez inne osoby. Obrazy są podzielone na różne kategorie, na przykład na podstawie godnych uwagi osób lub wydarzeń.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Aby uzyskać listę rynków obsługujących popularne obrazy, zobacz [Wyświetlanie trendów w obrazach](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Aby uzyskać szczegółowe informacje na temat nagłówków, parametrów, kodów rynku, obiektów odpowiedzi, błędów itp., zobacz odwołanie do [interfejsu API wyszukiwania obrazów Bing w wersji 7.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
## <a name="response-json"></a>Odpowiedź JSON
Odpowiedź na żądanie wyszukiwania obrazu zawiera wyniki jako obiekty JSON. Przykłady analizowania wyników można znaleźć w [samouczku](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) i [kodzie źródłowym](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Następne kroki
Interfejsy API **usługi Bing** obsługują akcje wyszukiwania, które zwracają wyniki zgodnie z ich typem.Wszystkie punkty końcowe wyszukiwania zwracają wyniki jako obiekty odpowiedzi JSON. Wszystkie punkty końcowe obsługują kwerendy, które zwracają określony język i/lub lokalizację według długości geograficznej, szerokości geograficznej i promienia wyszukiwania.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez każdy punkt końcowy, zobacz strony odwołań dla każdego typu.
Aby zapoznać się z przykładami podstawowych żądań za pomocą interfejsu API wyszukiwania obrazów, zobacz Szybkie [uruchamianie wyszukiwania obrazów](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).
