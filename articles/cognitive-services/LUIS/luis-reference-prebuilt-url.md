---
title: Wbudowane jednostki adresów URL — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje o wstępnie utworzonym obiekcie URL w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 12831ede2b9d9251f2e02fa396ee7d2fb2d61240
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499493"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>Obiekt prekompilowanego adresu URL dla aplikacji LUIS
Jednostka adresu URL wyodrębnia adresy URL z nazwami domen lub adresami IP. Ponieważ ta jednostka jest już przeszkolone, nie trzeba dodawać przykładowych wyrażenia długości zawierających adresy URL do aplikacji. Jednostka adresu URL jest obsługiwana tylko w `en-us` kulturze. 

## <a name="types-of-urls"></a>Typy adresów URL
Adres URL jest zarządzany przez [Aparaty rozpoznawania tekstu —](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) repozytorium GitHub

## <a name="resolution-for-prebuilt-url-entity"></a>Rozwiązanie dla wstępnie skompilowanej jednostki adresu URL

Następujące obiekty jednostki są zwracane dla zapytania:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-responsetabv3"></a>[Odpowiedź v3](#tab/V3)

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `false`:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Pełna odpowiedź w wersji 3](#tab/V3-verbose)

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `true`:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ],
    "$instance": {
        "url": [
            {
                "type": "builtin.url",
                "text": "https://www.luis.ai",
                "startIndex": 0,
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

W poniższym przykładzie przedstawiono rozwiązanie https://www.luis.ai jest doskonałym przykładem usługi poznawczej analizy

```json
"entities": [
    {
        "entity": "https://www.luis.ai",
        "type": "builtin.url",
        "startIndex": 0,
        "endIndex": 17
    }
]
```

* * * 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Dowiedz się więcej o liczbie [porządkowej](luis-reference-prebuilt-ordinal.md), [liczbie](luis-reference-prebuilt-number.md)i jednostkach [temperatury](luis-reference-prebuilt-temperature.md) .
