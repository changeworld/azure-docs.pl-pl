---
title: CurrentY wstępnie utworzone jednostki
titleSuffix: Azure
description: Ten artykuł zawiera waluty informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: f3d4c8aeabb33e9f0f8b53f63c60c8d6ca702a83
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876678"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Waluta wstępnie utworzone jednostki dla aplikacji usługi LUIS
Jednostkę waluty wbudowanych wykrywa waluty w wielu nazw i kraje, niezależnie od kultury aplikacji usługi LUIS. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierający Waluta intencji aplikacji. Jednostkę waluty jest obsługiwana w [wiele kultur](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Typy waluty
Waluta jest zarządzana z [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) repozytorium GitHub

## <a name="resolution-for-currency-entity"></a>Rozwiązania dla jednostki waluty
W poniższym przykładzie pokazano rozdzielczość **builtin.currency** jednostki.

```json
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
