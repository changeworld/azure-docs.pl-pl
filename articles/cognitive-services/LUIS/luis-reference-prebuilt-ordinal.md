---
title: Numer porządkowy wstępnie utworzone jednostki
titleSuffix: Azure
description: Ten artykuł zawiera informacje porządkowe wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 22b66b36eb3bde4e5953435d3ecf9d872d999589
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218703"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Numer porządkowy wstępnie utworzone jednostki dla aplikacji usługi LUIS
Numer porządkowy jest reprezentacji liczbowej obiektu wewnątrz zestawu: `first`, `second`, `third`. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierający porządkowego do intencji aplikacji. Numer porządkowy jednostki jest obsługiwana w [wiele kultur](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Typy numer
Liczba porządkowa jest zarządzana z [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) repozytorium GitHub

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Rozwiązania dla wstępnie utworzone jednostki porządkowe
W poniższym przykładzie pokazano rozdzielczość **builtin.ordinal** jednostki.

```json
{
  "query": "Order the second option",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.9993253
  },
  "intents": [
    {
      "intent": "OrderFood",
      "score": 0.9993253
    },
    {
      "intent": "None",
      "score": 0.05046708
    }
  ],
  "entities": [
    {
      "entity": "second",
      "type": "builtin.ordinal",
      "startIndex": 10,
      "endIndex": 15,
      "resolution": {
        "value": "2"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [procent](luis-reference-prebuilt-percentage.md), [phonenumber](luis-reference-prebuilt-phonenumber.md), i [temperatury](luis-reference-prebuilt-temperature.md) jednostek. 
