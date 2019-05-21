---
title: Wiek wstępnie utworzone jednostki
titleSuffix: Azure
description: Ten artykuł zawiera wiek informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: b8239688000f0ce32ca2c2be054b1443bbb698b5
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65069134"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>Wiek wstępnie utworzone jednostki dla aplikacji usługi LUIS
Wiek wstępnie utworzone jednostki przechwytuje wartość wieku zarówno liczbowo i pod względem dni, tygodnie, miesiące i lata. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierający wiek do intencji aplikacji. Okres ważności jednostki jest obsługiwana w [wiele kultur](luis-reference-prebuilt-entities.md). 

## <a name="types-of-age"></a>Typy wiek
Okres ważności jest zarządzana z [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) repozytorium GitHub

## <a name="resolution-for-prebuilt-age-entity"></a>Rozpoznawanie wiek wstępnie utworzone jednostki

### <a name="api-version-2x"></a>Wersja interfejsu API 2.x

W poniższym przykładzie pokazano rozdzielczość **builtin.age** jednostki.

```json
{
  "query": "A 90 day old utilities bill is quite late.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8236133
  },
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Wersja zapoznawcza interfejsu API 3.x

Następujący kod JSON jest `verbose` parametr `false`:

```json
{
    "query": "A 90 day old utilities bill is quite late.",
    "prediction": {
        "normalizedQuery": "a 90 day old utilities bill is quite late.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.558252
            }
        },
        "entities": {
            "age": [
                {
                    "number": 90,
                    "unit": "Day"
                }
            ]
        }
    }
}
```

Następujący kod JSON jest `verbose` parametr `true`:

```json
{
    "query": "A 90 day old utilities bill is quite late.",
    "prediction": {
        "normalizedQuery": "a 90 day old utilities bill is quite late.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.558252
            }
        },
        "entities": {
            "age": [
                {
                    "number": 90,
                    "unit": "Day"
                }
            ],
            "$instance": {
                "age": [
                    {
                        "type": "builtin.age",
                        "text": "90 day old",
                        "startIndex": 2,
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

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [waluty](luis-reference-prebuilt-currency.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), i [wymiaru](luis-reference-prebuilt-dimension.md) jednostek. 
