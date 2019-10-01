---
title: Prebudowana jednostka w wersji 2 — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie skompilowane informacje o jednostkach geographyV2 w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 04bc3019a55920351b0e91c87e63b8309d94e34c
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677606"
---
# <a name="geographyv2-prebuilt-entity-for-a-luis-app"></a>GeographyV2 prekompilowaną jednostkę dla aplikacji LUIS
Wstępnie utworzona jednostka geographyV2 wykrywa miejsca. Ponieważ ta jednostka jest już przeszkolone, nie trzeba dodawać przykładowej wyrażenia długości zawierającej GeographyV2 do intencji aplikacji. Jednostka GeographyV2 jest obsługiwana w [kulturze](luis-reference-prebuilt-entities.md)angielskiej.

## <a name="subtypes"></a>Podtypów
Lokalizacje geograficzne mają Podtyp:

|Podtyp|Przeznaczenie|
|--|--|
|`poi`|punkt orientacyjny|
|`city`|Nazwa miasta|
|`countryRegion`|Nazwa kraju lub regionu|
|`continent`|Nazwa kontynentu|
|`state`|Nazwa województwa|


## <a name="resolution-for-geographyv2-entity"></a>Rozwiązanie dla jednostki GeographyV2

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

Poniższy przykład pokazuje rozdzielczość jednostki **wbudowanej. geographyV2** .

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.8008023
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.8008023
        }
    ],
    "entities": [
        {
            "entity": "the sphinx",
            "type": "builtin.geographyV2.poi",
            "startIndex": 18,
            "endIndex": 27
        },
        {
            "entity": "gizah",
            "type": "builtin.geographyV2.city",
            "startIndex": 32,
            "endIndex": 36
        },
        {
            "entity": "egypt",
            "type": "builtin.geographyV2.countryRegion",
            "startIndex": 38,
            "endIndex": 42
        },
        {
            "entity": "africa",
            "type": "builtin.geographyV2.continent",
            "startIndex": 47,
            "endIndex": 52
        },
        {
            "entity": "texas",
            "type": "builtin.geographyV2.state",
            "startIndex": 72,
            "endIndex": 76
        },
        {
            "entity": "carol",
            "type": "builtin.personName",
            "startIndex": 0,
            "endIndex": 4
        }
    ]
} 
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `false`:

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "prediction": {
        "normalizedQuery": "carol is visiting the sphinx in gizah egypt in africa before heading to texas",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5115521
            }
        },
        "entities": {
            "geographyV2": [
                "the sphinx",
                "gizah",
                "egypt",
                "africa",
                "texas"
            ]
        }
    }
}
```

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `true`:

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "prediction": {
        "normalizedQuery": "carol is visiting the sphinx in gizah egypt in africa before heading to texas",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5115521
            }
        },
        "entities": {
            "geographyV2": [
                "the sphinx",
                "gizah",
                "egypt",
                "africa",
                "texas"
            ],
            "$instance": {
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2",
                        "text": "the sphinx",
                        "startIndex": 18,
                        "length": 10,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    },
                    {
                        "type": "builtin.geographyV2",
                        "text": "gizah",
                        "startIndex": 32,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    },
                    {
                        "type": "builtin.geographyV2",
                        "text": "egypt",
                        "startIndex": 38,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    },
                    {
                        "type": "builtin.geographyV2",
                        "text": "africa",
                        "startIndex": 47,
                        "length": 6,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    },
                    {
                        "type": "builtin.geographyV2",
                        "text": "texas",
                        "startIndex": 72,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

* * * 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Dowiedz się więcej o jednostkach [poczty e-mail](luis-reference-prebuilt-email.md), [liczb](luis-reference-prebuilt-number.md)i [porządkowej](luis-reference-prebuilt-ordinal.md) . 
