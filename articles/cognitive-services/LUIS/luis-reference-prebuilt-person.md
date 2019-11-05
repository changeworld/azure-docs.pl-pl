---
title: Osoba — wstępnie utworzona jednostka — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie skompilowane informacje o jednostce PersonName w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 9777c62d97c70d4f6a0d0a4d912dea3fa8decd23
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499554"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>Osoba z wbudowaną jednostką dla aplikacji LUIS
Przedbudowana jednostka PersonName wykrywa nazwiska osób. Ponieważ ta jednostka jest już przeszkolony, nie trzeba dodawać przykładowej wyrażenia długości zawierającej nazwisko osoby do zamiaru aplikacji. Jednostka PersonName jest obsługiwana w [kulturze](luis-reference-prebuilt-entities.md)angielskiej i chińskiej.

## <a name="resolution-for-personname-entity"></a>Rozwiązanie dla jednostki PersonName

Następujące obiekty jednostki są zwracane dla zapytania:

`Is Jill Jones in Cairo?`


#### <a name="v3-responsetabv3"></a>[Odpowiedź v3](#tab/V3)


Poniższy kod JSON jest z parametrem `verbose` ustawionym na `false`:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Pełna odpowiedź w wersji 3](#tab/V3-verbose)
Poniższy kod JSON jest z parametrem `verbose` ustawionym na `true`:

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
#### <a name="v2-responsetabv2"></a>[Odpowiedź w wersji 2](#tab/V2)

Poniższy przykład pokazuje rozdzielczość jednostki **wbudowanej. PersonName** .

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

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Dowiedz się więcej o jednostkach [poczty e-mail](luis-reference-prebuilt-email.md), [liczb](luis-reference-prebuilt-number.md)i [porządkowej](luis-reference-prebuilt-ordinal.md) . 
