---
title: Keyphrase prekompilowana jednostka — LUIS
titleSuffix: Azure Cognitive Services
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
ms.openlocfilehash: d7ecb579305cb17525e220bc16be430c77d25080
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563433"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>keyPhrase prekompilowaną jednostkę dla aplikacji LUIS
keyPhrase wyodrębnia szereg frazy kluczowe z wypowiedź. Nie ma potrzeby dodawanie wypowiedzi przykład zawierający keyPhrase do aplikacji. Jednostka keyPhrase jest obsługiwana w [wiele kultur](luis-language-support.md#languages-supported) jako część [analizy tekstu](../text-analytics/overview.md) funkcji. 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Rozpoznawanie keyPhrase wstępnie utworzone jednostki

### <a name="api-version-2x"></a>Interfejs API w wersji 2. x

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
