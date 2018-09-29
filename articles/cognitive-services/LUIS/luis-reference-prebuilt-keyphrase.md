---
title: Odwołanie usługi LUIS wstępnie keyphrase jednostek — Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Ten artykuł zawiera keyphrase informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/09/2018
ms.author: diberry
ms.openlocfilehash: 4133b7c7c3fabbe92a3208c567d7b4c6c2c27283
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434402"
---
# <a name="keyphrase-entity"></a>keyPhrase, jednostka
keyPhrase wyodrębnia szereg frazy kluczowe z wypowiedź. Nie ma potrzeby dodawanie wypowiedzi przykład zawierający keyPhrase do aplikacji. Jednostka keyPhrase jest obsługiwana w [wiele kultur](luis-language-support.md#languages-supported) jako część [analizy tekstu](../text-analytics/overview.md) funkcji. 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Rozpoznawanie keyPhrase wstępnie utworzone jednostki
W poniższym przykładzie pokazano rozdzielczość **builtin.keyPhrase** jednostki.

```JSON
{
  "query": "where is the educational requirements form for the development and engineering group",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.182757929
  },
  "entities": [
    {
      "entity": "development",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 61
    },
    {
      "entity": "educational requirements",
      "type": "builtin.keyPhrase",
      "startIndex": 13,
      "endIndex": 36
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [procent](luis-reference-prebuilt-percentage.md), [numer](luis-reference-prebuilt-number.md), i [wiek](luis-reference-prebuilt-age.md) jednostek.
