---
title: Procent wstępnie skompilowanej jednostki — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera procent wstępnie utworzonych informacji o jednostkach w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 0005d679819f7516e1ded6294030cd42813e085f
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677532"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Procent wstępnie skompilowanej jednostki dla aplikacji LUIS
Liczby procentowe mogą być wyświetlane jako ułamki, `3 1/2` lub jako procent, `2%`. Ponieważ ta jednostka jest już przeszkolone, nie trzeba dodawać przykładowej wyrażenia długości zawierającej procent do intencji aplikacji. Jednostka procentowa jest obsługiwana w [wielu kulturach](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Typy wartości procentowych
Wartość procentowa jest zarządzana przez [Aparaty rozpoznawania tekstu —](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) repozytorium GitHub

## <a name="resolution-for-prebuilt-percentage-entity"></a>Rozwiązanie dla wstępnie skompilowanej jednostki wartości procentowej

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

Poniższy przykład pokazuje rozdzielczość jednostki **wbudowanej. procent** .

```json
{
  "query": "set a trigger when my stock goes up 2%",
  "topScoringIntent": {
    "intent": "SetTrigger",
    "score": 0.971157849
  },
  "intents": [
    {
      "intent": "SetTrigger",
      "score": 0.971157849
    }
  ],
  "entities": [
    {
      "entity": "2%",
      "type": "builtin.percentage",
      "startIndex": 36,
      "endIndex": 37,
      "resolution": {
        "value": "2%"
      }
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `false`:

```json
{
    "query": "set a trigger when my stock goes up 2%",
    "prediction": {
        "normalizedQuery": "set a trigger when my stock goes up 2%",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.541765451
            }
        },
        "entities": {
            "percentage": [
                2
            ]
        }
    }
}
```

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `true`:

```json
{
    "query": "set a trigger when my stock goes up 2%",
    "prediction": {
        "normalizedQuery": "set a trigger when my stock goes up 2%",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.541765451
            }
        },
        "entities": {
            "percentage": [
                2
            ],
            "$instance": {
                "percentage": [
                    {
                        "type": "builtin.percentage",
                        "text": "2%",
                        "startIndex": 36,
                        "length": 2,
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
