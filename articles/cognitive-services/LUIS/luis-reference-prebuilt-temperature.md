---
title: Wstępnie utworzone jednostki temperatury
titleSuffix: Azure
description: Ten artykuł zawiera temperatury informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 6a6600a0294eaef9f3b8ee6e7f6f021eb5c1e60b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709699"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>Temperatura wstępnie utworzone jednostki dla aplikacji usługi LUIS
Temperatura wyodrębnia różnych typów temperatury. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierający temperatury do aplikacji. Jednostki temperatury jest obsługiwana w [wiele kultur](luis-reference-prebuilt-entities.md). 

## <a name="types-of-temperature"></a>Typy temperatury
Temperatura jest zarządzana z [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) repozytorium GitHub

## <a name="resolution-for-prebuilt-temperature-entity"></a>Rozpoznawanie temperatury wstępnie utworzone jednostki
W poniższym przykładzie pokazano rozdzielczość **builtin.temperature** jednostki.

```json
{
  "query": "set the temperature to 30 degrees",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.85310787
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.85310787
    }
  ],
  "entities": [
    {
      "entity": "30 degrees",
      "type": "builtin.temperature",
      "startIndex": 23,
      "endIndex": 32,
      "resolution": {
        "unit": "Degree",
        "value": "30"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [procent](luis-reference-prebuilt-percentage.md), [numer](luis-reference-prebuilt-number.md), i [wiek](luis-reference-prebuilt-age.md) jednostek. 
