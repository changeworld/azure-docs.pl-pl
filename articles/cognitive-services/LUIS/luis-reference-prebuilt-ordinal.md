---
title: Wstępnie skompilowany obiekt Entity-LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje porządkowe wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.openlocfilehash: 8070f614e84dd4c328d7451103c0850826704e60
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560220"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Wstępnie utworzona jednostka dla aplikacji LUIS
Numer porządkowy jest reprezentacji liczbowej obiektu wewnątrz zestawu: `first`, `second`, `third`. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierający porządkowego do intencji aplikacji. Numer porządkowy jednostki jest obsługiwana w [wiele kultur](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Typy numer
Numer porządkowy jest zarządzany przez [Aparaty rozpoznawania tekstu —](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) repozytorium GitHub

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Rozwiązania dla wstępnie utworzone jednostki porządkowe

### <a name="api-version-2x"></a>Interfejs API w wersji 2. x

W poniższym przykładzie pokazano rozdzielczość **builtin.ordinal** jednostki.

```json
{
  "query": "Order the second option",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.9993253
  },
  "intents": [
    {
      "intent": "OrderFood",
      "score": 0.9993253
    },
    {
      "intent": "None",
      "score": 0.05046708
    }
  ],
  "entities": [
    {
      "entity": "second",
      "type": "builtin.ordinal",
      "startIndex": 10,
      "endIndex": 15,
      "resolution": {
        "value": "2"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Wersja zapoznawcza interfejsu API w wersji 3. x

Poniższy kod JSON jest z `verbose` parametrem ustawionym na: `false`

```json
{
    "query": "Order the second option",
    "prediction": {
        "normalizedQuery": "order the second option",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "ordinal": [
                {
                    "offset": 2,
                    "relativeTo": "start"
                }
            ]
        }
    }
}
```

Poniższy kod JSON jest z `verbose` parametrem ustawionym na: `true`

```json
{
    "query": "Order the second option",
    "prediction": {
        "normalizedQuery": "order the second option",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "ordinal": [
                {
                    "offset": 2,
                    "relativeTo": "start"
                }
            ],
            "$instance": {
                "ordinal": [
                  {
                    "type": "builtin.ordinal",
                    "text": "second",
                    "startIndex": 10,
                    "length": 6,
                    "modelTypeId": 2,
                    "modelType": "Prebuilt Entity Extractor",
                    "recognitionSources": [
                        "model"
                    ]
                  }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat [OrdinalV2](luis-reference-prebuilt-ordinal-v2.md), [numeru telefonu](luis-reference-prebuilt-phonenumber.md)i jednostek [temperatury](luis-reference-prebuilt-temperature.md) . 
