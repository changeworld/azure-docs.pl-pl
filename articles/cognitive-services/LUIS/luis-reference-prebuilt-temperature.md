---
title: Wstępnie skonstruowana Jednostka temperatury — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie skompilowane informacje o jednostkach w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 0c16a60b0ddcde733cfbc7b9c90e07b697526015
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677392"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>Wstępnie utworzona jednostka temperatury dla aplikacji LUIS
Temperatura wyodrębnia różne typy temperatur. Ponieważ ta jednostka jest już przeszkolone, nie trzeba dodawać przykładowej wyrażenia długości zawierającej temperaturę do aplikacji. Jednostka temperatury jest obsługiwana w [wielu kulturach](luis-reference-prebuilt-entities.md). 

## <a name="types-of-temperature"></a>Typy temperatur
Temperatura jest zarządzana przez [Aparaty rozpoznawania tekstu —](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) repozytorium GitHub

## <a name="resolution-for-prebuilt-temperature-entity"></a>Rozwiązanie dla prekompilowanej jednostki temperatury

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

Poniższy przykład przedstawia rozdzielczość **wbudowanej jednostki temperatury** .

```json
{
  "query": "set the temperature to 30 degrees",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.85310787
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.85310787
    }
  ],
  "entities": [
    {
      "entity": "30 degrees",
      "type": "builtin.temperature",
      "startIndex": 23,
      "endIndex": 32,
      "resolution": {
        "unit": "Degree",
        "value": "30"
      }
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `false`:

```json
{
    "query": "set the temperature to 30 degrees",
    "prediction": {
        "normalizedQuery": "set the temperature to 30 degrees",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.656305432
            }
        },
        "entities": {
            "temperature": [
                {
                    "number": 30,
                    "unit": "Degree"
                }
            ]
        }
    }
}
```

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `true`:

```json
{
    "query": "set the temperature to 30 degrees",
    "prediction": {
        "normalizedQuery": "set the temperature to 30 degrees",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.656305432
            }
        },
        "entities": {
            "temperature": [
                {
                    "number": 30,
                    "unit": "Degree"
                }
            ],
            "$instance": {
                "temperature": [
                    {
                        "type": "builtin.temperature",
                        "text": "30 degrees",
                        "startIndex": 23,
                        "length": 10,
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

Dowiedz się więcej o jednostkach [wartości procentowych](luis-reference-prebuilt-percentage.md), [liczbowych](luis-reference-prebuilt-number.md)i [wieku](luis-reference-prebuilt-age.md) . 
