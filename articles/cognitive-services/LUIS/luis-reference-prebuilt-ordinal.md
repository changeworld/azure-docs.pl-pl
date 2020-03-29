---
title: Wstępnie utworzone jednostki porządkowej — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wbudowane informacje o jednostce utworzonej w usłudze Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: bb3bb27db48255f534e873ed4e93ac62f07016af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273444"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Wstępnie skompilowana jednostka porządkowa dla aplikacji usługi LUIS
Liczba porządkowa jest liczbową reprezentacją obiektu wewnątrz `first` `second`zestawu: , , `third`. Ponieważ ta jednostka jest już przeszkolony, nie trzeba dodawać wypowiedzi przykład zawierający porządkowe do intencji aplikacji. Jednostka porządkowa jest obsługiwana w [wielu kulturach](luis-reference-prebuilt-entities.md).

## <a name="types-of-ordinal"></a>Rodzaje liczby porządkowej
Liczba porządkowa jest zarządzana za pomocą repozytorium GitHub [w tekście aparatury-aparaty rozpoznawania](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45)

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Rozdzielczość dla wstępnie utworzonej jednostki porządkowej

Następujące obiekty encji są zwracane dla kwerendy:

`Order the second option`

#### <a name="v3-response"></a>[Odpowiedź V3](#tab/V3)

Następujący JSON jest `verbose` z parametrem ustawionym na: `false`

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[Odpowiedź pełne V3](#tab/V3-verbose)
Następujący JSON jest `verbose` z parametrem ustawionym na: `true`

```json
"entities": {
    "ordinal": [
        2
    ],
    "$instance": {
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "second",
                "startIndex": 10,
                "length": 6,
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

Poniższy przykład przedstawia rozdzielczość **jednostki wbudowanej.ordinal.**

```json
"entities": [
  {
    "entity": "second",
    "type": "builtin.ordinal",
    "startIndex": 10,
    "endIndex": 15,
    "resolution": {
      "value": "2"
    }
  }
]
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

Dowiedz się więcej o [elementach OrdinalV2](luis-reference-prebuilt-ordinal-v2.md), [numer telefonu](luis-reference-prebuilt-phonenumber.md)i [temperatura.](luis-reference-prebuilt-temperature.md)
