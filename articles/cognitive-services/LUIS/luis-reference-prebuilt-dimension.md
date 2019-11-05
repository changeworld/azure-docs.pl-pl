---
title: Wstępnie utworzone jednostki wymiarów — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje o jednostkach wstępnie skompilowanych wymiarów w Language Understanding (LUIS).
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 6699b1617ccd1fef9a507e71fdd73a02b0e98bea
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465034"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Wstępnie utworzona jednostka dla aplikacji LUIS
Wstępnie utworzona jednostka wymiaru wykrywa różne typy wymiarów, niezależnie od kultury aplikacji LUIS. Ponieważ ta jednostka jest już przeszkolone, nie trzeba dodawać przykładowych wyrażenia długości zawierających wymiary do intencji aplikacji. Jednostka wymiaru jest obsługiwana w [wielu kulturach](luis-reference-prebuilt-entities.md). 

## <a name="types-of-dimension"></a>Typy wymiarów

Wymiar jest zarządzany przez [Aparaty rozpoznawania tekstu —](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) repozytorium GitHub.

## <a name="resolution-for-dimension-entity"></a>Rozwiązanie dla jednostki wymiaru

Następujące obiekty jednostki są zwracane dla zapytania:

`10 1/2 miles of cable`

#### <a name="v3-responsetabv3"></a>[Odpowiedź v3](#tab/V3)

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `false`:

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
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Pełna odpowiedź w wersji 3](#tab/V3-verbose)
Poniższy kod JSON jest z parametrem `verbose` ustawionym na `true`:

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

#### <a name="v2-responsetabv2"></a>[Odpowiedź w wersji 2](#tab/V2)

Poniższy przykład pokazuje rozdzielczość jednostki **wbudowanej. wymiar** .

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

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Dowiedz się więcej o jednostkach [poczty e-mail](luis-reference-prebuilt-email.md), [liczb](luis-reference-prebuilt-number.md)i [porządkowej](luis-reference-prebuilt-ordinal.md) . 
