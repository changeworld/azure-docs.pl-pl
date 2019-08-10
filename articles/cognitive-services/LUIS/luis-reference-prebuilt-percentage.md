---
title: Procent wstępnie skompilowanej jednostki — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wartość procentową informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 06dada1633fcb90153358e0f1d048c5fa618db0b
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933441"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Procent wstępnie skompilowanej jednostki dla aplikacji LUIS
Procent liczby mogą wystąpić jako użycie ułamkowych, `3 1/2`, lub jako wartość procentowa `2%`. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierający procent w celu intencji aplikacji. Procent jednostek jest obsługiwana w [wiele kultur](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Typy wartości procentowej
Wartość procentowa jest zarządzana przez [Aparaty rozpoznawania tekstu —](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) repozytorium GitHub

## <a name="resolution-for-prebuilt-percentage-entity"></a>Rozpoznawanie procent wstępnie utworzone jednostki

### <a name="api-version-2x"></a>Interfejs API w wersji 2. x

W poniższym przykładzie pokazano rozdzielczość **builtin.percentage** jednostki.

```json
{
  "query": "set a trigger when my stock goes up 2%",
  "topScoringIntent": {
    "intent": "SetTrigger",
    "score": 0.971157849
  },
  "intents": [
    {
      "intent": "SetTrigger",
      "score": 0.971157849
    }
  ],
  "entities": [
    {
      "entity": "2%",
      "type": "builtin.percentage",
      "startIndex": 36,
      "endIndex": 37,
      "resolution": {
        "value": "2%"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Wersja zapoznawcza interfejsu API w wersji 3. x

Poniższy kod JSON jest z `verbose` parametrem ustawionym na: `false`

```json
{
    "query": "set a trigger when my stock goes up 2%",
    "prediction": {
        "normalizedQuery": "set a trigger when my stock goes up 2%",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.541765451
            }
        },
        "entities": {
            "percentage": [
                2
            ]
        }
    }
}
```

Poniższy kod JSON jest z `verbose` parametrem ustawionym na: `true`

```json
{
    "query": "set a trigger when my stock goes up 2%",
    "prediction": {
        "normalizedQuery": "set a trigger when my stock goes up 2%",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.541765451
            }
        },
        "entities": {
            "percentage": [
                2
            ],
            "$instance": {
                "percentage": [
                    {
                        "type": "builtin.percentage",
                        "text": "2%",
                        "startIndex": 36,
                        "length": 2,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [porządkowe](luis-reference-prebuilt-ordinal.md), [numer](luis-reference-prebuilt-number.md), i [temperatury](luis-reference-prebuilt-temperature.md) jednostek. 
