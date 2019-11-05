---
title: Wstępnie utworzone jednostki numeru telefonu — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie skompilowane informacje o jednostce numeru telefonu w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 1cc7469bf6b29ed864fac3955dc8770aa879f84d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499526"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Wstępnie utworzona jednostka numeru telefonu dla aplikacji LUIS
Jednostka `phonenumber` wyodrębnia różne numery telefonów, w tym kod kraju. Ponieważ ta jednostka jest już przeszkolone, nie trzeba dodawać przykładowej wyrażenia długości do aplikacji. Jednostka `phonenumber` jest obsługiwana tylko w `en-us` kultury. 

## <a name="types-of-a-phone-number"></a>Typy numerów telefonów
`Phonenumber` jest zarządzany przez [Aparaty rozpoznawania tekstu —](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) repozytorium GitHub

## <a name="resolution-for-this-prebuilt-entity"></a>Rozwiązanie dla tej prekompilowanej jednostki

Następujące obiekty jednostki są zwracane dla zapytania:

`my mobile is 1 (800) 642-7676`

#### <a name="v3-responsetabv3"></a>[Odpowiedź v3](#tab/V3)

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `false`:

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Pełna odpowiedź w wersji 3](#tab/V3-verbose)
Poniższy kod JSON jest z parametrem `verbose` ustawionym na `true`:

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
#### <a name="v2-responsetabv2"></a>[Odpowiedź w wersji 2](#tab/V2)

Poniższy przykład pokazuje rozdzielczość jednostki **wbudowanej.**

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

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Dowiedz się więcej o [wartościach procentowych](luis-reference-prebuilt-percentage.md), [liczbowych](luis-reference-prebuilt-number.md)i [temperatury](luis-reference-prebuilt-temperature.md) . 
