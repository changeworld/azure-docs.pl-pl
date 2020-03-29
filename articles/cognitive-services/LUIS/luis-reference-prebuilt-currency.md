---
title: Wstępnie skompilowana encja waluty — usługa LUIS
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
ms.openlocfilehash: 56d264fa976967ae4772f50b4aa6f58b30355d6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270782"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Wstępnie skompilowana encja waluty dla aplikacji usługi LUIS
Wstępnie utworzone jednostki waluty wykrywa waluty w wielu nominałach i krajach/regionach, niezależnie od kultury aplikacji usługi LUIS. Ponieważ ta jednostka jest już przeszkolony, nie trzeba dodawać wypowiedzi przykład zawierający waluty do intencji aplikacji. Jednostka waluty jest obsługiwana w [wielu kulturach](luis-reference-prebuilt-entities.md).

## <a name="types-of-currency"></a>Rodzaje waluty
Waluta jest zarządzana za pomocą repozytorium GitHub [w tekście aparatury rozpoznawania](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26)

## <a name="resolution-for-currency-entity"></a>Rozwiązanie dla jednostki walutowej

#### <a name="v3-response"></a>[Odpowiedź V3](#tab/V3)

Następujący JSON jest `verbose` z parametrem ustawionym na: `false`

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "units": "Dollar"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[Odpowiedź pełne V3](#tab/V3-verbose)
Następujący JSON jest `verbose` z parametrem ustawionym na: `true`

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "unit": "Dollar"
        }
    ],
    "$instance": {
        "money": [
            {
                "type": "builtin.currency",
                "text": "$10.99",
                "startIndex": 23,
                "length": 6,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[Odpowiedź V2](#tab/V2)

Poniższy przykład przedstawia rozdzielczość **jednostki builtin.currency.**

```json
"entities": [
    {
        "entity": "$10.99",
        "type": "builtin.currency",
        "startIndex": 23,
        "endIndex": 28,
        "resolution": {
        "unit": "Dollar",
        "value": "10.99"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

Dowiedz się więcej o [jednostkach datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimension](luis-reference-prebuilt-dimension.md)i [email.](luis-reference-prebuilt-email.md)
