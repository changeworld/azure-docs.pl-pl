---
title: Wbudowane LUIS jednostek wymiaru odwołania - Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Ten artykuł zawiera wymiaru informacji wbudowane jednostki opis języka (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 3b2758f1d68ae3659f2e43cad555d327b21f8732
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321981"
---
# <a name="dimension-entity"></a>Wymiar jednostki
Jednostki wbudowane wymiaru wykrywa różnych typów wymiarów, niezależnie od LUIS kultury aplikacji. Ponieważ była już uczona tej jednostki, jest konieczne Dodaj zniesławiających przykład zawierający wymiary intencje aplikacji. Wymiar jednostki jest obsługiwana w [wiele kultur](luis-reference-prebuilt-entities.md). 

## <a name="types-of-dimension"></a>Typy wymiaru

Wymiar jest zarządzany z [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) repozytorium Github


## <a name="resolution-for-dimension-entity"></a>Rozpoznawanie wymiar jednostki
W poniższym przykładzie przedstawiono rozpoznanie **builtin.dimension** jednostki.

```JSON
{
  "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.762141049
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.762141049
    }
  ],
  "entities": [
    {
      "entity": "10 1/2 miles",
      "type": "builtin.dimension",
      "startIndex": 19,
      "endIndex": 30,
      "resolution": {
        "unit": "Mile",
        "value": "10.5"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [e-mail](luis-reference-prebuilt-email.md), [numer](luis-reference-prebuilt-number.md), i [porządkowej](luis-reference-prebuilt-ordinal.md) jednostek. 