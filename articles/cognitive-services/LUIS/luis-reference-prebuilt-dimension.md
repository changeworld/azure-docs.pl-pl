---
title: Wstępnie utworzone jednostki wymiarów — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wymiar informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 37b9ca071ca5b2e423d7efd93e4089e3a77b4e50
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933607"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Wstępnie utworzona jednostka dla aplikacji LUIS
Jednostki wymiaru wbudowanych wykrywa różnego rodzaju wymiarów, niezależnie od kultury aplikacji usługi LUIS. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierającego wymiary do intencji aplikacji. Wymiar jednostki jest obsługiwana w [wiele kultur](luis-reference-prebuilt-entities.md). 

## <a name="types-of-dimension"></a>Typy wymiaru

Wymiar jest zarządzany przez [Aparaty rozpoznawania tekstu —](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) repozytorium GitHub


## <a name="resolution-for-dimension-entity"></a>Rozpoznawanie jednostki wymiaru

### <a name="api-version-2x"></a>Interfejs API w wersji 2. x

W poniższym przykładzie pokazano rozdzielczość **builtin.dimension** jednostki.

```json
{
  "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.762141049
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.762141049
    }
  ],
  "entities": [
    {
      "entity": "10 1/2 miles",
      "type": "builtin.dimension",
      "startIndex": 19,
      "endIndex": 30,
      "resolution": {
        "unit": "Mile",
        "value": "10.5"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Wersja zapoznawcza interfejsu API w wersji 3. x

Poniższy kod JSON jest z `verbose` parametrem ustawionym na: `false`

```json
{
    "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
    "prediction": {
        "normalizedQuery": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.400049
            }
        },
        "entities": {
            "dimension": [
                {
                    "number": 10.5,
                    "unit": "Mile"
                }
            ]
        }
    }
}
```

Poniższy kod JSON jest z `verbose` parametrem ustawionym na: `true`

```json
{
    "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
    "prediction": {
        "normalizedQuery": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.400049
            }
        },
        "entities": {
            "dimension": [
                {
                    "number": 10.5,
                    "unit": "Mile"
                }
            ],
            "$instance": {
                "dimension": [
                    {
                        "type": "builtin.dimension",
                        "text": "10 1/2 miles",
                        "startIndex": 19,
                        "length": 12,
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

Dowiedz się więcej o [e-mail](luis-reference-prebuilt-email.md), [numer](luis-reference-prebuilt-number.md), i [porządkowe](luis-reference-prebuilt-ordinal.md) jednostek. 
