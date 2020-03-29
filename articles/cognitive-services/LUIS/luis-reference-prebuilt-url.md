---
title: Wstępnie utworzone jednostki adresu URL — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie utworzone informacje o jednostce adresu URL w yrozmaicieniu języka (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 49f145ce3e9022826abad5d274dd611bb2cc6530
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270349"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>Wstępnie skompilowana encja adresu URL dla aplikacji usługi LUIS
Jednostka adresu URL wyodrębnia adresy URL z nazwami domen lub adresami IP. Ponieważ ta jednostka jest już przeszkolony, nie trzeba dodawać wypowiedzi przykład zawierający adresy URL do aplikacji. Jednostka adresu URL `en-us` jest obsługiwana tylko w kulturze.

## <a name="types-of-urls"></a>Typy adresów URL
Adres URL jest zarządzany za pomocą repozytorium GitHub [w tekście aparatury rozpoznawania](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml)

## <a name="resolution-for-prebuilt-url-entity"></a>Rozwiązanie dla wstępnie utworzonej encji adresu URL

Następujące obiekty encji są zwracane dla kwerendy:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-response"></a>[Odpowiedź V3](#tab/V3)

Następujący JSON jest `verbose` z parametrem ustawionym na: `false`

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Odpowiedź pełne V3](#tab/V3-verbose)

Następujący JSON jest `verbose` z parametrem ustawionym na: `true`

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
#### <a name="v2-response"></a>[Odpowiedź V2](#tab/V2)

Poniższy przykład pokazuje rozdzielczość https://www.luis.ai jest doskonałym przykładem usług poznawczych sztucznej inteligencji

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

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

Dowiedz się więcej o jednostkach [porządkowych,](luis-reference-prebuilt-ordinal.md) [liczba](luis-reference-prebuilt-number.md)i [temperatura.](luis-reference-prebuilt-temperature.md)
