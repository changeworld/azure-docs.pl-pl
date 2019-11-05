---
title: LUIS wstępnie utworzonych jednostek — dokumentacja poczty e-mail
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie skompilowane informacje o jednostce poczty e-mail w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6f262752a50b58eae8ffbea81b8e7fc4d8c65b98
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464985"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Wstępnie utworzona jednostka poczty e-mail dla aplikacji LUIS
Wyodrębnianie wiadomości e-mail obejmuje cały adres e-mail z wypowiedź. Ponieważ ta jednostka jest już przeszkolone, nie trzeba dodawać przykładowej wyrażenia długości zawierającej wiadomości e-mail do intencji aplikacji. Jednostka poczty e-mail jest obsługiwana tylko w `en-us` kulturze. 

## <a name="resolution-for-prebuilt-email"></a>Rozwiązanie dla wstępnie skompilowanej poczty e-mail

Następujące obiekty jednostki są zwracane dla zapytania:

`please send the information to patti@contoso.com`

#### <a name="v3-responsetabv3"></a>[Odpowiedź v3](#tab/V3)

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `false`:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Pełna odpowiedź w wersji 3](#tab/V3-verbose)

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `true`:

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
#### <a name="v2-responsetabv2"></a>[Odpowiedź w wersji 2](#tab/V2)

W poniższym przykładzie przedstawiono rozdzielczość **wbudowanej jednostki poczty e-mail** .

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

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Dowiedz się więcej o [liczbie](luis-reference-prebuilt-number.md), liczbie [porządkowej](luis-reference-prebuilt-ordinal.md)i [wartości procentowej](luis-reference-prebuilt-percentage.md). 
