---
title: Keyphrase wstępnie utworzone jednostki
titleSuffix: Azure
description: Ten artykuł zawiera keyphrase informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 5ef7ccb58533161d8397ad42e70de1999908dc36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473267"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>keyPhrase wstępnie utworzone jednostki dla aplikacji usługi LUIS
keyPhrase wyodrębnia szereg frazy kluczowe z wypowiedź. Nie ma potrzeby dodawanie wypowiedzi przykład zawierający keyPhrase do aplikacji. Jednostka keyPhrase jest obsługiwana w [wiele kultur](luis-language-support.md#languages-supported) jako część [analizy tekstu](../text-analytics/overview.md) funkcji. 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Rozpoznawanie keyPhrase wstępnie utworzone jednostki
W poniższym przykładzie pokazano rozdzielczość **builtin.keyPhrase** jednostki.

```json
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
