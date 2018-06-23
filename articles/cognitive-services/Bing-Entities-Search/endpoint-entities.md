---
title: Punkt końcowy wyszukiwania jednostki | Dokumentacja firmy Microsoft
description: Podsumowanie punkt końcowy interfejsu API jednostek wyszukiwania.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: a2557c6000445544b3b47a05d7d356ccaa9928b4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346885"
---
# <a name="entity-search-endpoint"></a>Punkt końcowy wyszukiwania jednostki
**Encji wyszukiwania API** zawiera jeden punkt końcowy, który zwraca jednostki z sieci Web na podstawie zapytania.

## <a name="endpoint"></a>Endpoint
Aby pobrać jednostki wyniki za pomocą **interfejsu API Bing**, Wyślij `GET` żądanie następujący punkt końcowy. Umożliwia dalsze Definiowanie specyfikacji w nagłówki i parametry adresu URL.

**Punkt końcowy:** zwraca jednostki, które mają zastosowanie do użytkownika zapytania wyszukiwania zdefiniowane przez `?q=""`.
```
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Aby uzyskać więcej informacji dotyczących nagłówków, parametry, kody rynku, obiekty odpowiedzi, błędów itd., zobacz [w wersji 7 interfejsu API Bing encji wyszukiwania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) odwołania.

## <a name="response-json"></a>Odpowiedź JSON
Odpowiedź na żądanie wyszukiwania jednostki dołącza wyniki, jako obiektów JSON. Aby uzyskać przykłady wyników, zobacz [wprowadzenie](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start).

## <a name="next-steps"></a>Kolejne kroki
**Bing** interfejsów API obsługi akcji wyszukiwania, które zwracają wyniki zgodnie z ich typu. Wszystkie punkty końcowe wyszukiwania zwracają wyniki w postaci obiektów JSON odpowiedzi.  Wszystkie punkty końcowe obsługi zapytań, które zwracają określonego języka i/lub lokalizacji geograficznej, szerokość i wyszukiwania usługi radius.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez każdy punkt końcowy zobacz strony podręcznika dla każdego typu.
Aby uzyskać przykłady użycia wyszukiwania jednostki interfejsu API, zobacz [wprowadzenie](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start) i [Resizing i przycinanie obrazów miniatur](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/resize-and-crop-thumbnails).