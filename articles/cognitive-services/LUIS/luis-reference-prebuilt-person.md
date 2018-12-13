---
title: PersonName wstępnie utworzone jednostki
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera personName informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: ad19aa8eef72234e034fe9c0e1905e848ace0650
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091751"
---
# <a name="personname-entity"></a>PersonName, jednostka
Jednostki wstępnie personName wykrywa nazwiska osób. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierający personName do intencji aplikacji. Jednostka personName jest obsługiwana w językach angielskim i chiński [kultur](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>Rozpoznawanie personName jednostki
W poniższym przykładzie pokazano rozdzielczość **builtin.personName** jednostki.

```json
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  }
  "entities": [
    {
      "entity": "Jill Jones",
      "type": "builtin.personName",
      "startIndex": 3,
      "endIndex": 12
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [e-mail](luis-reference-prebuilt-email.md), [numer](luis-reference-prebuilt-number.md), i [porządkowe](luis-reference-prebuilt-ordinal.md) jednostek. 