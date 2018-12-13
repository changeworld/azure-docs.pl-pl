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
ms.openlocfilehash: 31dce870e99cbe74e2795a3ba661b0caf92dd48e
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140238"
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