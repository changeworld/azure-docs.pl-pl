---
title: CurrentY wstępnie utworzone jednostki
titleSuffix: Azure
description: Ten artykuł zawiera waluty informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: c87bfddb611e3e232d4a43f45ae60412566b98fc
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53161767"
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