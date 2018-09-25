---
title: PersonName wstępnie utworzone jednostki — dokumentacja usługi LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera personName informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 0bc9d59fa44125429bfcb4e6ca9e68d93b81c04d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46950577"
---
# <a name="personname-entity"></a>PersonName jednostki
Jednostki wstępnie personName wykrywa nazwiska osób. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierający personName do intencji aplikacji. Jednostka personName jest obsługiwana w językach angielskim i chiński [kultur](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>Rozpoznawanie personName jednostki
W poniższym przykładzie pokazano rozdzielczość **builtin.personName** jednostki.

```JSON
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