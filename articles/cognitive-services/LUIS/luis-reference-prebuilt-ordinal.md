---
title: Wbudowane LUIS jednostek odwołanie porządkowej - Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Ten artykuł zawiera informacje porządkowej jednostki wbudowane opis języka (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 2ff9b083e6cabe455baea3ed777dd6cc00b6fbfe
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321967"
---
# <a name="ordinal-entity"></a>{Numer porządkowy jednostki
Numer porządkowy jest reprezentację liczbową obiektu wewnątrz zestawu: `first`, `second`, `third`. Ponieważ była już uczona tej jednostki, jest konieczne Dodaj zniesławiających przykład zawierającą numer porządkowy do lokalizacji docelowych aplikacji. {Numer porządkowy jednostki jest obsługiwana w [wiele kultur](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Typy numer
Liczba porządkowa jest zarządzany z [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) repozytorium Github

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Czas rozpoznawania nazw dla jednostki porządkowej wbudowane
W poniższym przykładzie przedstawiono rozpoznanie **builtin.ordinal** jednostki.

```JSON
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