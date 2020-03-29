---
title: Wstępnie zbudowany encję temperatury — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie utworzone informacje o encji w yrozmaicieniu języka (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 7e2b48c6353f56ab2269a8718146cb765797adba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270369"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>Wstępnie skompilowana jednostka temperatury dla aplikacji usługi LUIS
Temperatura wyodrębnia różne typy temperatur. Ponieważ ta jednostka jest już przeszkolony, nie trzeba dodawać wypowiedzi przykład zawierający temperaturę do aplikacji. Jednostka temperatury jest obsługiwana w [wielu kulturach](luis-reference-prebuilt-entities.md).

## <a name="types-of-temperature"></a>Rodzaje temperatur
Temperatura jest zarządzana za pomocą repozytorium GitHub [w tekście aparatury rozpoznawania](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819)

## <a name="resolution-for-prebuilt-temperature-entity"></a>Rozdzielczość dla wstępnie wbudowanego elementu temperatury

Następujące obiekty encji są zwracane dla kwerendy:

`set the temperature to 30 degrees`


#### <a name="v3-response"></a>[Odpowiedź V3](#tab/V3)

Następujący JSON jest `verbose` z parametrem ustawionym na: `false`

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[Odpowiedź pełne V3](#tab/V3-verbose)
Następujący JSON jest `verbose` z parametrem ustawionym na: `true`

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ],
    "$instance": {
        "temperature": [
            {
                "type": "builtin.temperature",
                "text": "30 degrees",
                "startIndex": 23,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[Odpowiedź V2](#tab/V2)

Poniższy przykład przedstawia rozdzielczość **jednostki builtin.temperature.**

```json
"entities": [
    {
        "entity": "30 degrees",
        "type": "builtin.temperature",
        "startIndex": 23,
        "endIndex": 32,
        "resolution": {
        "unit": "Degree",
        "value": "30"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

Dowiedz się więcej o [procentach,](luis-reference-prebuilt-percentage.md) [liczbach](luis-reference-prebuilt-number.md)i jednostkach [wiekowych.](luis-reference-prebuilt-age.md)
