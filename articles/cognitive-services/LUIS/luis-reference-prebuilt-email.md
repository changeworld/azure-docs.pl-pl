---
title: Odwołanie do wiadomości e-mail wstępnie utworzone przez jednostki usługi LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie utworzone informacje o jednostce poczty e-mail w yrozmaicieniu języka (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: f7aa2a341a82d30f7022af49e626a3cd358854b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273479"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Wstępnie skompilowana encja poczty e-mail dla aplikacji usługi LUIS
Wyodrębnianie wiadomości e-mail zawiera cały adres e-mail z wypowiedź. Ponieważ ta jednostka jest już przeszkolony, nie trzeba dodawać wypowiedzi przykład zawierający wiadomości e-mail do intencji aplikacji. Jednostka poczty e-mail jest obsługiwana tylko w `en-us` kulturze.

## <a name="resolution-for-prebuilt-email"></a>Rozdzielczość dla wstępnie utworzonej poczty e-mail

Następujące obiekty encji są zwracane dla kwerendy:

`please send the information to patti@contoso.com`

#### <a name="v3-response"></a>[Odpowiedź V3](#tab/V3)

Następujący JSON jest `verbose` z parametrem ustawionym na: `false`

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Odpowiedź pełne V3](#tab/V3-verbose)

Następujący JSON jest `verbose` z parametrem ustawionym na: `true`

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ],
    "$instance": {
        "email": [
            {
                "type": "builtin.email",
                "text": "patti@contoso.com",
                "startIndex": 31,
                "length": 17,
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

W poniższym przykładzie przedstawiono rozdzielczość **jednostki builtin.email.**

```json
"entities": [
    {
        "entity": "patti@contoso.com",
        "type": "builtin.email",
        "startIndex": 31,
        "endIndex": 55,
        "resolution": {
        "value": "patti@contoso.com"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

Dowiedz się więcej o [liczbie](luis-reference-prebuilt-number.md), [liczba porządkowa](luis-reference-prebuilt-ordinal.md)i [procent](luis-reference-prebuilt-percentage.md).
