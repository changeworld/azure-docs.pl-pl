---
title: Wstępnie skompilowana jednostka porządkowa V2 — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wbudowane informacje o jednostce utworzonej w wersji 2 w usłudze Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 5e852313db75e598da647ea0f985e2ee18af16de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270485"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Wstępnie skompilowana jednostka porządkowej wersji 2 dla aplikacji usługi LUIS
Liczba porządkowa V2 rozszerza liczbę [porządkową,](luis-reference-prebuilt-ordinal.md) aby `next` `last`zapewnić `previous`względne odwołania, takie jak , i . Nie są one wyodrębniane przy użyciu jednostki prekompilowane.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Rozdzielczość dla wstępnie utworzonej jednostki porządkowej V2

Następujące obiekty encji są zwracane dla kwerendy:

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[Odpowiedź V3](#tab/V3)

Następujący JSON jest `verbose` z parametrem ustawionym na: `false`

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[Odpowiedź pełne V3](#tab/V3-verbose)

Następujący JSON jest `verbose` z parametrem ustawionym na: `true`

```json
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
```
#### <a name="v2-response"></a>[Odpowiedź V2](#tab/V2)

Poniższy przykład przedstawia rozdzielczość **jednostki builtin.ordinalV2.**

```json
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
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

Dowiedz się więcej o [procentach,](luis-reference-prebuilt-percentage.md) [numerach telefonu](luis-reference-prebuilt-phonenumber.md)i jednostkach [temperatury.](luis-reference-prebuilt-temperature.md)
