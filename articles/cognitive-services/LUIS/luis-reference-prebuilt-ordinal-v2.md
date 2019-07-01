---
title: Numer porządkowy V2 wstępnie utworzone jednostki
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ten artykuł zawiera numer porządkowy V2 informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.openlocfilehash: 862b962f5642e01d7ed8250f49d51a6132447083
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486141"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Numer porządkowy V2 wstępnie utworzone jednostki dla aplikacji usługi LUIS
Rozwija porządkowa numer V2 [numer](luis-reference-prebuilt-ordinal.md) zapewnienie odwołania względne, takie jak `next`, `last`, i `previous`. Nie zostały one wyodrębnione porządkowe wstępnie utworzone jednostki.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Rozwiązania dla wstępnie utworzone jednostki w wersji 2 porządkowe

### <a name="api-version-2x"></a>Wersja interfejsu API 2.x

W poniższym przykładzie pokazano rozdzielczość **builtin.ordinalV2** jednostki.

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

### <a name="preview-api-version-3x"></a>Wersja zapoznawcza interfejsu API 3.x

Następujący kod JSON jest `verbose` parametr `false`:

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

Następujący kod JSON jest `verbose` parametr `true`:

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

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [procent](luis-reference-prebuilt-percentage.md), [numer telefonu](luis-reference-prebuilt-phonenumber.md), i [temperatury](luis-reference-prebuilt-temperature.md) jednostek. 
