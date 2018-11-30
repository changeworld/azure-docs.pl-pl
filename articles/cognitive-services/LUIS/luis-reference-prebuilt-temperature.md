---
title: LUIS wbudowanych jednostek temperatury odwołanie — Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Ten artykuł zawiera temperatury informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 2716600346e65f2ca0b97e56cc156fdfb10ff3a4
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52440962"
---
# <a name="temperature-entity"></a>Temperature, jednostka
Temperatura wyodrębnia różnych typów temperatury. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierający temperatury do aplikacji. Jednostki temperatury jest obsługiwana w [wiele kultur](luis-reference-prebuilt-entities.md). 

## <a name="types-of-temperature"></a>Typy temperatury
Temperatura jest zarządzana z [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) repozytorium Github

## <a name="resolution-for-prebuilt-temperature-entity"></a>Rozpoznawanie temperatury wstępnie utworzone jednostki
W poniższym przykładzie pokazano rozdzielczość **builtin.temperature** jednostki.

```JSON
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