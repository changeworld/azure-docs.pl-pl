---
title: Encja wstępnie utworzone procentowe — USŁUGA LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera procent wstępnie utworzone informacje o jednostce w yrozmaicieniu języka (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 31ea1c36139abcb1e102161ad76a203073ba4dfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270515"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Procent wstępnie utworzonej jednostki dla aplikacji usługi LUIS
Liczby procentowe mogą być wyświetlane `3 1/2`jako ułamki `2%`lub jako wartość procentowa. Ponieważ ta jednostka jest już przeszkolony, nie trzeba dodawać wypowiedzi przykład zawierający procent intencji aplikacji. Encja procentowa jest obsługiwana w [wielu kulturach](luis-reference-prebuilt-entities.md).

## <a name="types-of-percentage"></a>Rodzaje wartości procentowej
Procent jest zarządzany z repozytorium GitHub [w tekście aparaturii](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114)

## <a name="resolution-for-prebuilt-percentage-entity"></a>Rozdzielczość dla wstępnie utworzonej encji procentowej

Następujące obiekty encji są zwracane dla kwerendy:

`set a trigger when my stock goes up 2%`

#### <a name="v3-response"></a>[Odpowiedź V3](#tab/V3)

Następujący JSON jest `verbose` z parametrem ustawionym na: `false`

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[Odpowiedź pełne V3](#tab/V3-verbose)
Następujący JSON jest `verbose` z parametrem ustawionym na: `true`

```json
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

Poniższy przykład przedstawia rozdzielczość **jednostki builtin.percentage.**

```json
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
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

Dowiedz się więcej o jednostkach [porządkowych,](luis-reference-prebuilt-ordinal.md) [liczba](luis-reference-prebuilt-number.md)i [temperatura.](luis-reference-prebuilt-temperature.md)
