---
title: Usługa LUIS ze wstępnie utworzonych jednostek e-mail odwołanie — Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Ten artykuł zawiera adres e-mail informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 51e6a5da0d757023bd7cd1f61c77387a77b77de4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869054"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Wyślij wiadomość e-mail ze wstępnie utworzonych jednostek dla aplikacji usługi LUIS
Wyodrębniania wiadomości e-mail zawiera adres e-mail całego z wypowiedź. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierający do intencji aplikacji poczty e-mail. Adres e-mail jednostki jest obsługiwana w `en-us` tylko kultury. 

## <a name="resolution-for-prebuilt-email"></a>Rozwiązania do obsługi poczty e-mail wstępnie
W poniższym przykładzie pokazano rozdzielczość **builtin.email** jednostki.

```json
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

Dowiedz się więcej o [numer](luis-reference-prebuilt-number.md), [porządkowe](luis-reference-prebuilt-ordinal.md), i [procent](luis-reference-prebuilt-percentage.md). 
