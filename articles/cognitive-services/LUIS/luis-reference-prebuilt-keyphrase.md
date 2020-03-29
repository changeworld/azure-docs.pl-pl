---
title: Wstępnie skompilowana encja keyphrase — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie utworzone informacje o jednostce z frazą keyphrase w zrozumieni usługę (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 53be1b13f1e2744e143a4be0777e3a8e3135460e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270530"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>Wstępnie skompilowana jednostka keyPhrase dla aplikacji usługi LUIS
KeyPhrase jednostki wyodrębnia wiele fraz kluczowych z wypowiedź. Nie trzeba dodawać wypowiedzi przykład zawierający keyPhrase do aplikacji. KeyPhrase jednostki jest obsługiwany w [wielu kulturach](luis-language-support.md#languages-supported) jako część funkcji [analizy tekstu.](../text-analytics/overview.md)

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Rozdzielczość dla wstępnie utworzonej jednostki keyPhrase

Następujące obiekty encji są zwracane dla kwerendy:

`where is the educational requirements form for the development and engineering group`

#### <a name="v3-response"></a>[Odpowiedź V3](#tab/V3)

Następujący JSON jest `verbose` z parametrem ustawionym na: `false`

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Odpowiedź pełne V3](#tab/V3-verbose)
Następujący JSON jest `verbose` z parametrem ustawionym na: `true`

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "educational requirements",
                "startIndex": 13,
                "length": 24,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "development",
                "startIndex": 51,
                "length": 11,
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

Poniższy przykład przedstawia rozdzielczość **jednostki builtin.keyPhrase.**

```json
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
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

Dowiedz się więcej o [procentach,](luis-reference-prebuilt-percentage.md) [liczbach](luis-reference-prebuilt-number.md)i jednostkach [wiekowych.](luis-reference-prebuilt-age.md)
