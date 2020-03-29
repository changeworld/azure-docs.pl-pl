---
title: Jednostka Wiek wstępnie utworzonej — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie utworzone informacje o jednostce wieku w zrozumieni usługę (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 1607b80276a9c35bf0ac2f0a00bd2226e2c07f97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270793"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>Wstępnie skompilowana encja wiekowa dla aplikacji usługi LUIS
Wstępnie utworzony element wieku przechwytuje wartość wieku zarówno numerycznie, jak i pod względem dni, tygodni, miesięcy i lat. Ponieważ ta jednostka jest już przeszkolony, nie trzeba dodawać wypowiedzi przykład zawierający wieku do intencji aplikacji. Jednostka Wiek jest obsługiwana w [wielu kulturach](luis-reference-prebuilt-entities.md).

## <a name="types-of-age"></a>Rodzaje wieku
Age jest zarządzany za pomocą repozytorium GitHub [w tekście aparatury rozpoznawania](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3)

## <a name="resolution-for-prebuilt-age-entity"></a>Rozwiązanie dla wstępnie utworzonej jednostki wiekowej



#### <a name="v3-response"></a>[Odpowiedź V3](#tab/V3)

Następujący JSON jest `verbose` z parametrem ustawionym na: `false`

```json
"entities": {
    "age": [
        {
            "number": 90,
            "unit": "Day"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[Odpowiedź pełne V3](#tab/V3-verbose)
Następujący JSON jest `verbose` z parametrem ustawionym na: `true`

```json
"entities": {
    "age": [
        {
            "number": 90,
            "unit": "Day"
        }
    ],
    "$instance": {
        "age": [
            {
                "type": "builtin.age",
                "text": "90 day old",
                "startIndex": 2,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[Odpowiedź V2](#tab/V2)

Poniższy przykład przedstawia rozdzielczość **jednostki builtin.age.**

```json
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

Dowiedz się więcej o [walutach,](luis-reference-prebuilt-currency.md) [datetimeV2](luis-reference-prebuilt-datetimev2.md)i encjach [wymiarów.](luis-reference-prebuilt-dimension.md)
