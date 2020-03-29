---
title: Numer telefonu Wstępnie utworzone jednostki — USŁUGA LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie utworzone informacje o jednostce z numerem telefonu w usłudze Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 4e2c8e27c6d4195252c6a5b423fa98b2a4247182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270466"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Wstępnie skompilowana encja numeru telefonu dla aplikacji usługi LUIS
Jednostka `phonenumber` wyodrębnia różne numery telefonów, w tym kod kraju. Ponieważ ta jednostka jest już przeszkolony, nie trzeba dodawać wypowiedzi przykład do aplikacji. Jednostka `phonenumber` jest obsługiwana `en-us` tylko w kulturze.

## <a name="types-of-a-phone-number"></a>Typy numeru telefonu
`Phonenumber`jest zarządzany z repozytorium GitHub [w tekście aparatury rozpoznawania](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml)

## <a name="resolution-for-this-prebuilt-entity"></a>Rozdzielczość dla tego wstępnie utworzonego encji

Następujące obiekty encji są zwracane dla kwerendy:

`my mobile is 1 (800) 642-7676`

#### <a name="v3-response"></a>[Odpowiedź V3](#tab/V3)

Następujący JSON jest `verbose` z parametrem ustawionym na: `false`

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Odpowiedź pełne V3](#tab/V3-verbose)
Następujący JSON jest `verbose` z parametrem ustawionym na: `true`

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ],
    "$instance": {

        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 (800) 642-7676",
                "startIndex": 13,
                "length": 16,
                "score": 1.0,
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

W poniższym przykładzie przedstawiono rozdzielczość **jednostki builtin.phonenumber.**

```json
"entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

Dowiedz się więcej o [procentach,](luis-reference-prebuilt-percentage.md) [liczbach](luis-reference-prebuilt-number.md)i jednostkach [temperatury.](luis-reference-prebuilt-temperature.md)
