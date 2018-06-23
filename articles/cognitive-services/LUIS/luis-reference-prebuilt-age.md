---
title: Wbudowane LUIS jednostek wieku odwołanie - Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Ten artykuł zawiera wieku informacji wbudowane jednostki opis języka (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 59732469cf0d1e55643f3977958ec34a887130d3
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321988"
---
# <a name="age-entity"></a>Wiek jednostki
Jednostka wbudowane wieku przechwytuje wartość wieku zarówno numeryczne i pod względem dni, tygodnie, miesiące i lata. Ponieważ była już uczona tej jednostki, jest konieczne Dodaj zniesławiających przykład zawierający wiek do lokalizacji docelowych aplikacji. Wiek jednostki jest obsługiwana w [wiele kultur](luis-reference-prebuilt-entities.md). 

## <a name="types-of-age"></a>Typy wieku
Okres ważności jest zarządzany z [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) repozytorium Github

## <a name="resolution-for-prebuilt-age-entity"></a>Rozwiązania wieku wbudowane jednostki
W poniższym przykładzie przedstawiono rozpoznanie **builtin.age** jednostki.

```JSON
{
  "query": "A 90 day old utilities bill is quite late.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8236133
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8236133
    }
  ],
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [waluty](luis-reference-prebuilt-currency.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), i [wymiaru](luis-reference-prebuilt-dimension.md) jednostek. 