---
title: Wstępnie skompilowana encja PersonName — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje o wstępnie utworzonej jednostce personName w zrozumieni usługę (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 768c719211e8a8f2133d3798343d076e795a3da0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273431"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>Wstępnie skompilowana encja PersonName dla aplikacji usługi LUIS
Wstępnie skompilowana jednostka personName wykrywa nazwy osób. Ponieważ ta jednostka jest już przeszkolony, nie trzeba dodawać wypowiedzi przykład zawierający personName do intencji aplikacji. personName entity jest obsługiwany w języku angielskim i chińskim [kultury](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>Rozwiązanie dla jednostki personName

Następujące obiekty encji są zwracane dla kwerendy:

`Is Jill Jones in Cairo?`


#### <a name="v3-response"></a>[Odpowiedź V3](#tab/V3)


Następujący JSON jest `verbose` z parametrem ustawionym na: `false`

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Odpowiedź pełne V3](#tab/V3-verbose)
Następujący JSON jest `verbose` z parametrem ustawionym na: `true`

```json
"entities": {
    "personName": [
        "Jill Jones"
    ],
    "$instance": {
        "personName": [
            {
                "type": "builtin.personName",
                "text": "Jill Jones",
                "startIndex": 3,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
    }
}
```
#### <a name="v2-response"></a>[Odpowiedź V2](#tab/V2)

Poniższy przykład przedstawia rozdzielczość **jednostki builtin.personName.**

```json
"entities": [
{
    "entity": "Jill Jones",
    "type": "builtin.personName",
    "startIndex": 3,
    "endIndex": 12
}
]
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

Dowiedz się więcej o [jednostkach poczty e-mail,](luis-reference-prebuilt-email.md) [numeru](luis-reference-prebuilt-number.md)i [porządkowych.](luis-reference-prebuilt-ordinal.md)
