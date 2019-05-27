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
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 988609f411ad405b0f1dc244b23fb6db446136a2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "65071997"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>keyPhrase wstępnie utworzone jednostki dla aplikacji usługi LUIS
keyPhrase wyodrębnia szereg frazy kluczowe z wypowiedź. Nie ma potrzeby dodawanie wypowiedzi przykład zawierający keyPhrase do aplikacji. Jednostka keyPhrase jest obsługiwana w [wiele kultur](luis-language-support.md#languages-supported) jako część [analizy tekstu](../text-analytics/overview.md) funkcji. 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Rozpoznawanie keyPhrase wstępnie utworzone jednostki

### <a name="api-version-2x"></a>Wersja interfejsu API 2.x

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
