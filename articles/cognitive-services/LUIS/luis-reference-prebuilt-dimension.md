---
title: Wymiar ze wstępnie utworzonych jednostek
titleSuffix: Azure
description: Ten artykuł zawiera wymiar informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 9099cdbb91e41998065d953b9d48b3b501df7c10
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712767"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Wymiar wstępnie utworzone jednostki dla aplikacji usługi LUIS
Jednostki wymiaru wbudowanych wykrywa różnego rodzaju wymiarów, niezależnie od kultury aplikacji usługi LUIS. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierającego wymiary do intencji aplikacji. Wymiar jednostki jest obsługiwana w [wiele kultur](luis-reference-prebuilt-entities.md). 

## <a name="types-of-dimension"></a>Typy wymiaru

Wymiar jest zarządzana z [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) repozytorium GitHub


## <a name="resolution-for-dimension-entity"></a>Rozpoznawanie jednostki wymiaru
W poniższym przykładzie pokazano rozdzielczość **builtin.dimension** jednostki.

```json
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

Dowiedz się więcej o [e-mail](luis-reference-prebuilt-email.md), [numer](luis-reference-prebuilt-number.md), i [porządkowe](luis-reference-prebuilt-ordinal.md) jednostek. 
