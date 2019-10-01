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
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 77e1c9e64081e20ef064fd8341c54c13940f0dd4
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677311"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>Obiekt prekompilowanego adresu URL dla aplikacji LUIS
Jednostka adresu URL wyodrębnia adresy URL z nazwami domen lub adresami IP. Ponieważ ta jednostka jest już przeszkolone, nie trzeba dodawać przykładowych wyrażenia długości zawierających adresy URL do aplikacji. Jednostka adresu URL jest obsługiwana tylko w kulturze `en-us`. 

## <a name="types-of-urls"></a>Typy adresów URL
Adres URL jest zarządzany przez [Aparaty rozpoznawania tekstu —](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) repozytorium GitHub

## <a name="resolution-for-prebuilt-url-entity"></a>Rozwiązanie dla wstępnie skompilowanej jednostki adresu URL

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

Poniższy przykład pokazuje rozdzielczość jednostki **wbudowanej. URL** .

```json
{
  "query": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.781975448
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.781975448
    }
  ],
  "entities": [
    {
      "entity": "https://www.luis.ai",
      "type": "builtin.url",
      "startIndex": 0,
      "endIndex": 17
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `false`:

```json
{
    "query": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
    "prediction": {
        "normalizedQuery": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.421936184
            }
        },
        "entities": {
            "url": [
                "https://www.luis.ai"
            ]
        }
    }
}
```

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `true`:

```json
{
    "query": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
    "prediction": {
        "normalizedQuery": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.421936184
            }
        },
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
                        "length": 19,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```


* * * 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Dowiedz się więcej o liczbie [porządkowej](luis-reference-prebuilt-ordinal.md), [liczbie](luis-reference-prebuilt-number.md)i jednostkach [temperatury](luis-reference-prebuilt-temperature.md) .
