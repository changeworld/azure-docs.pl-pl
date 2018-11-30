---
title: Usługa LUIS ze wstępnie utworzonych jednostek e-mail odwołanie — Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Ten artykuł zawiera adres e-mail informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 6d05f62ad725a89b0a34b21b8a8d36bb8fa464b1
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441015"
---
# <a name="email-entity"></a>Email, jednostka
Wyodrębniania wiadomości e-mail zawiera adres e-mail całego z wypowiedź. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierający do intencji aplikacji poczty e-mail. Adres e-mail jednostki jest obsługiwana w `en-us` tylko kultury. 

## <a name="resolution-for-prebuilt-email"></a>Rozwiązania do obsługi poczty e-mail wstępnie
W poniższym przykładzie pokazano rozdzielczość **builtin.email** jednostki.

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

Dowiedz się więcej o [numer](luis-reference-prebuilt-number.md), [porządkowe](luis-reference-prebuilt-ordinal.md), i [procent](luis-reference-prebuilt-percentage.md). 