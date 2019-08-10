---
title: Wstępnie utworzona jednostka porządkowa v2 — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie skompilowane informacje o jednostkach w wersji 2 w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: diberry
ms.openlocfilehash: 1579c50f2dcf78e350ce014ffb1516cec4ac85b3
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933487"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Wstępnie utworzona jednostka porządkowa v2 dla aplikacji LUIS
Numer porządkowy v2 rozszerza [](luis-reference-prebuilt-ordinal.md) liczbę porządkową, aby zapewnić odwołania `next`względne, `previous`takie jak, `last`, i. Nie są one wyodrębniane przy użyciu wstępnie skompilowanej jednostki.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Rozwiązanie dla wstępnie skompilowanej liczby porządkowej v2

### <a name="api-version-2x"></a>Interfejs API w wersji 2. x

Poniższy przykład pokazuje rozdzielczość jednostki **wbudowanej. ordinalV2** .

```json
{
    "query": "what is the second to last choice in the list",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.823669851
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.823669851
        }
    ],
    "entities": [
        {
            "entity": "the second to last",
            "type": "builtin.ordinalV2.relative",
            "startIndex": 8,
            "endIndex": 25,
            "resolution": {
                "offset": "-1",
                "relativeTo": "end"
            }
        }
    ]
}
```

### <a name="preview-api-version-3x"></a>Wersja zapoznawcza interfejsu API w wersji 3. x

Poniższy kod JSON jest z `verbose` parametrem ustawionym na: `false`

```json
{
    "query": "what is the second to last choice in the list",
    "prediction": {
        "normalizedQuery": "what is the second to last choice in the list",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "ordinalV2": [
                {
                    "offset": -1,
                    "relativeTo": "end"
                }
            ]
        }
    }
}
```

Poniższy kod JSON jest z `verbose` parametrem ustawionym na: `true`

```json
{
    "query": "what is the second to last choice in the list",
    "prediction": {
        "normalizedQuery": "what is the second to last choice in the list",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "ordinalV2": [
                {
                    "offset": -1,
                    "relativeTo": "end"
                }
            ],
            "$instance": {
                "ordinalV2": [
                    {
                        "type": "builtin.ordinalV2.relative",
                        "text": "the second to last",
                        "startIndex": 8,
                        "length": 18,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [wartości procentowej](luis-reference-prebuilt-percentage.md), [numeru telefonu](luis-reference-prebuilt-phonenumber.md)i jednostek [temperatury](luis-reference-prebuilt-temperature.md) . 
