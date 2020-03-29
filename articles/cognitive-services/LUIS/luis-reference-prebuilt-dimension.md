---
title: Wstępnie utworzone jednostki wymiaru — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie utworzone informacje o jednostce wymiaru w yrozmaicieniu języka (LUIS).
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 3c7bebb38d0331c76eeaaa1d9d6464c568a61b83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270630"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Wstępnie skompilowana encja wymiaru dla aplikacji usługi LUIS
Prekompilowana jednostka wymiaru wykrywa różne typy wymiarów, niezależnie od kultury aplikacji usługi LUIS. Ponieważ ta jednostka jest już przeszkolony, nie trzeba dodawać wypowiedzi przykład zawierający wymiary do intencji aplikacji. Encja wymiar jest obsługiwana w [wielu kulturach](luis-reference-prebuilt-entities.md).

## <a name="types-of-dimension"></a>Rodzaje wymiarów

Wymiar jest zarządzany z repozytorium GitHub [w tekście aparatów rozpoznawania.](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml)

## <a name="resolution-for-dimension-entity"></a>Rozdzielczość dla jednostki wymiaru

Następujące obiekty encji są zwracane dla kwerendy:

`10 1/2 miles of cable`

#### <a name="v3-response"></a>[Odpowiedź V3](#tab/V3)

Następujący JSON jest `verbose` z parametrem ustawionym na: `false`

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[Odpowiedź pełne V3](#tab/V3-verbose)
Następujący JSON jest `verbose` z parametrem ustawionym na: `true`

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ],
    "$instance": {
        "dimension": [
            {
                "type": "builtin.dimension",
                "text": "10 1/2 miles",
                "startIndex": 0,
                "length": 12,
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

Poniższy przykład przedstawia rozdzielczość **jednostki builtin.dimension.**

```json
{
    "entity": "10 1/2 miles",
    "type": "builtin.dimension",
    "startIndex": 0,
    "endIndex": 11,
    "resolution": {
    "unit": "Mile",
    "value": "10.5"
    }
}
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

Dowiedz się więcej o [jednostkach poczty e-mail,](luis-reference-prebuilt-email.md) [numeru](luis-reference-prebuilt-number.md)i [porządkowych.](luis-reference-prebuilt-ordinal.md)
