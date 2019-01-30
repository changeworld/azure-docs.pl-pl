---
title: Wartość procentowa wstępnie utworzone jednostki
titleSuffix: Azure
description: Ten artykuł zawiera wartość procentową informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 3cdf8844ae614e301e1477a039d949b055034ba8
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208418"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Wartość procentowa wstępnie utworzone jednostki dla aplikacji usługi LUIS
Procent liczby mogą wystąpić jako użycie ułamkowych, `3 1/2`, lub jako wartość procentowa `2%`. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierający procent w celu intencji aplikacji. Procent jednostek jest obsługiwana w [wiele kultur](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Typy wartości procentowej
Wartość procentowa jest zarządzana z [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) repozytorium GitHub

## <a name="resolution-for-prebuilt-percentage-entity"></a>Rozpoznawanie procent wstępnie utworzone jednostki
W poniższym przykładzie pokazano rozdzielczość **builtin.percentage** jednostki.

```json
{
  "query": "set a trigger when my stock goes up 2%",
  "topScoringIntent": {
    "intent": "SetTrigger",
    "score": 0.971157849
  },
  "intents": [
    {
      "intent": "SetTrigger",
      "score": 0.971157849
    }
  ],
  "entities": [
    {
      "entity": "2%",
      "type": "builtin.percentage",
      "startIndex": 36,
      "endIndex": 37,
      "resolution": {
        "value": "2%"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [porządkowe](luis-reference-prebuilt-ordinal.md), [numer](luis-reference-prebuilt-number.md), i [temperatury](luis-reference-prebuilt-temperature.md) jednostek. 
