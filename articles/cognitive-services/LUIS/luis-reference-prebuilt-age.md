---
title: Wiek wstępnie utworzone jednostki
titleSuffix: Azure
description: Ten artykuł zawiera wiek informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: c901a384d7629a187a2a4fbd4295a1a68615abdb
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2019
ms.locfileid: "57342602"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>Wiek wstępnie utworzone jednostki dla aplikacji usługi LUIS
Wiek wstępnie utworzone jednostki przechwytuje wartość wieku zarówno liczbowo i pod względem dni, tygodnie, miesiące i lata. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierający wiek do intencji aplikacji. Okres ważności jednostki jest obsługiwana w [wiele kultur](luis-reference-prebuilt-entities.md). 

## <a name="types-of-age"></a>Typy wiek
Okres ważności jest zarządzana z [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) repozytorium GitHub

## <a name="resolution-for-prebuilt-age-entity"></a>Rozpoznawanie wiek wstępnie utworzone jednostki
W poniższym przykładzie pokazano rozdzielczość **builtin.age** jednostki.

```json
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
