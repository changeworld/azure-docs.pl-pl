---
title: Wbudowane LUIS jednostek waluty odwołanie - Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Ten artykuł zawiera waluty informacji wbudowane jednostki opis języka (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 3e20642f6734b0247d23db1a63317eb8b4a96b5e
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321848"
---
# <a name="currency-entity"></a>Jednostkę waluty
Jednostkę waluty wbudowane wykrywa waluty w wielu nazw i krajów, niezależnie od LUIS kultury aplikacji. Ponieważ była już uczona tej jednostki, jest konieczne Dodaj zniesławiających przykład zawierający Waluta intencje aplikacji. Jednostkę waluty jest obsługiwana w [wiele kultur](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Typy waluty
Currency jest zarządzany z [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) repozytorium Github

## <a name="resolution-for-currency-entity"></a>Czas rozpoznawania nazw dla jednostki waluty
W poniższym przykładzie przedstawiono rozpoznanie **builtin.currency** jednostki.

```JSON
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
  "entities": [
    {
      "entity": "$10.99",
      "type": "builtin.currency",
      "startIndex": 23,
      "endIndex": 28,
      "resolution": {
        "unit": "Dollar",
        "value": "10.99"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [datetimeV2](luis-reference-prebuilt-datetimev2.md), [wymiaru](luis-reference-prebuilt-dimension.md), i [e-mail](luis-reference-prebuilt-email.md) jednostek. 