---
title: PersonName wstępnie utworzone jednostki
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera personName informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 7b748c507d5c848cc83a8a0c55cb7b05903bc542
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473131"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>PersonName wstępnie utworzone jednostki dla aplikacji usługi LUIS
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
