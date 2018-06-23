---
title: Wbudowane LUIS jednostek e-mail odwołanie - Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Ten artykuł zawiera e-mail informacji wbudowane jednostki opis języka (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 517e92202bdd5bc8d970306b9e24999fa62a8e43
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321869"
---
# <a name="email-entity"></a>Adres e-mail jednostki
Wyodrębniania wiadomości e-mail zawiera adres e-mail cały z utterance. Ponieważ była już uczona tej jednostki, jest konieczne Dodaj zniesławiających przykład zawierający wiadomości e-mail do lokalizacji docelowych aplikacji. Jednostki poczty e-mail jest obsługiwana w `en-us` tylko kultury. 

## <a name="resolution-for-prebuilt-email"></a>Rozwiązania do obsługi poczty e-mail wbudowane
W poniższym przykładzie przedstawiono rozpoznanie **builtin.email** jednostki.

```JSON
{
  "query": "please send the information to patti.owens@microsoft.com",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.811592042
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.811592042
    }
  ],
  "entities": [
    {
      "entity": "patti.owens@microsoft.com",
      "type": "builtin.email",
      "startIndex": 31,
      "endIndex": 55
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [numer](luis-reference-prebuilt-number.md), [porządkowej](luis-reference-prebuilt-ordinal.md), i [procent](luis-reference-prebuilt-percentage.md). 