---
title: Punkt końcowy funkcji automatycznego sugerowania Bing
titlesuffix: Azure Cognitive Services
description: Podsumowanie punktu końcowego interfejsu API automatycznego sugerowania Bing.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: c2d1c97ad2af266558f9b664162526d5006d2092
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830448"
---
# <a name="bing-autosuggest-endpoint"></a>Punkt końcowy funkcji automatycznego sugerowania Bing

**Interfejs API automatycznego sugerowania Bing** obejmuje jednego punktu końcowego, który zwraca Lista proponowanych zapytań z częściowego wyszukiwany termin.

## <a name="endpoint"></a>Endpoint

Aby uzyskać sugerowane zapytania przy użyciu interfejsu API usługi Bing, Wyślij `GET` żądanie następujący punkt końcowy. Umożliwia dalsze Definiowanie specyfikacji w nagłówki i parametry adresu URL.

**Punkt końcowy:** zwraca sugestie dotyczące wyszukiwania jako wyniki JSON, które dotyczą dane wejściowe użytkownika zdefiniowane przez `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Aby uzyskać szczegółowe informacje o nagłówków, parametrów, kody na rynku, obiekty odpowiedzi, błędów itd., zobacz [interfejs API automatycznego sugerowania Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference) odwołania.

## <a name="response-json"></a>Odpowiedź JSON

Odpowiedź na żądanie automatycznego sugerowania zawiera wyniki jako obiekty JSON. Zobacz przykłady podczas analizowania wyników [samouczek](tutorials/autosuggest.md) i [kod źródłowy](tutorials/autosuggest-source.md).

## <a name="next-steps"></a>Kolejne kroki

**Bing** interfejsy API obsługują akcji wyszukiwania, które zwracają wyniki według ich typu. Wszystkie punkty końcowe wyszukiwania zwracają wyniki w postaci obiektów odpowiedzi JSON.
Wszystkie punkty końcowe obsługują zapytań, które zwracają określonego języka i/lub lokalizacji długość geograficzna, szerokość i wyszukiwania usługi radius.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez każdy punkt końcowy zobacz strony pomocy dla każdego typu.
Przykłady podstawowe żądań przy użyciu interfejsu API automatycznego sugerowania można znaleźć [automatycznego sugerowania przewodników Szybki Start](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).