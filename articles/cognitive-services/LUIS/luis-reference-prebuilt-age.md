---
title: Wstępnie skonstruowana jednostka wieku — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie skompilowane informacje o jednostce wiekowej w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 2a49cf406dea045302f84e95318eca1557fc3e22
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465110"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>Wstępnie utworzona jednostka wieku dla aplikacji LUIS
Przedbudowana jednostka wiekowa przechwytuje wartość wieku w liczbie i w postaci dni, tygodni, miesięcy i lat. Ponieważ ta jednostka jest już przeszkolone, nie trzeba dodawać przykładowej wyrażenia długości zawierającej wiek do intencji aplikacji. Jednostka wieku jest obsługiwana w [wielu kulturach](luis-reference-prebuilt-entities.md). 

## <a name="types-of-age"></a>Typy wieku
Wiek jest zarządzany przez [Aparaty rozpoznawania tekstu —](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) repozytorium GitHub

## <a name="resolution-for-prebuilt-age-entity"></a>Rozwiązanie dla wstępnie skompilowanej jednostki wiekowej



#### <a name="v3-responsetabv3"></a>[Odpowiedź v3](#tab/V3)

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `false`:

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
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Pełna odpowiedź w wersji 3](#tab/V3-verbose)
Poniższy kod JSON jest z parametrem `verbose` ustawionym na `true`:

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
#### <a name="v2-responsetabv2"></a>[Odpowiedź w wersji 2](#tab/V2)

Poniższy przykład przedstawia rozdzielczość **wbudowanej jednostki wiekowej** .

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

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Dowiedz się więcej na temat jednostek [walutowych](luis-reference-prebuilt-currency.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md)i [wymiarów](luis-reference-prebuilt-dimension.md) . 
