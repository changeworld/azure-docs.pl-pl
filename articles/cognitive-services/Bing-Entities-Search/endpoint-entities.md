---
title: Punkt końcowy funkcji wyszukiwania jednostek Bing
titlesuffix: Azure Cognitive Services
description: Podsumowanie punktu końcowego interfejsu API wyszukiwania jednostek Bing.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: 4013b1791e4c725469d3f180ae0fdecc9ae2ebba
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55167057"
---
# <a name="entity-search-endpoint"></a>Punkt końcowy wyszukiwania jednostki
**Interfejs API wyszukiwania jednostek** zawiera jeden punkt końcowy, który zwraca jednostki z sieci Web na podstawie zapytania.

## <a name="endpoint"></a>Endpoint
Można pobrać jednostki wyników za pomocą **interfejsu API Bing**, Wyślij `GET` żądanie następujący punkt końcowy. Umożliwia dalsze Definiowanie specyfikacji w nagłówki i parametry adresu URL.

**Punkt końcowy:** Zwraca jednostki, które są istotne dla zapytania wyszukiwania użytkownika zdefiniowane przez `?q=""`.
```
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Aby uzyskać szczegółowe informacje o nagłówków, parametrów, kody na rynku, obiekty odpowiedzi, błędów itd., zobacz [interfejs API wyszukiwania jednostek Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) odwołania.

## <a name="response-json"></a>Response JSON
Odpowiedzi na żądanie wyszukiwania jednostki zawiera wyniki jako obiekty JSON. Aby zapoznać się z przykładami wyników, zobacz [wprowadzenie](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start).

## <a name="next-steps"></a>Kolejne kroki
**Bing** interfejsy API obsługują akcji wyszukiwania, które zwracają wyniki według ich typu. Wszystkie punkty końcowe wyszukiwania zwracają wyniki w postaci obiektów odpowiedzi JSON.  Wszystkie punkty końcowe obsługują zapytań, które zwracają określonego języka i/lub lokalizacji długość geograficzna, szerokość i wyszukiwania usługi radius.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez każdy punkt końcowy zobacz strony pomocy dla każdego typu.
Przykłady użycia interfejsu API wyszukiwania jednostek, zobacz [wprowadzenie](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start) i [zmiana rozmiaru i przycinanie obrazów miniatur](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/resize-and-crop-thumbnails).
