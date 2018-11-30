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
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: a543e60c6e77ed9fdb825ad6cb2a936119677671
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334588"
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
