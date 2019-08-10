---
title: Wstępnie utworzone jednostki numeru telefonu — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie utworzone jednostki informacja o numerze telefonu w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: bf2fd053cabeaf85b177e284f86ba378e0e4389e
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933417"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Wstępnie utworzona jednostka numeru telefonu dla aplikacji LUIS
`phonenumber` Jednostki wyodrębnia różne numery telefonów, łącznie z kodem kraju. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład do aplikacji. `phonenumber` Jednostki jest obsługiwana w `en-us` tylko kultury. 

## <a name="types-of-a-phone-number"></a>Typy numerów telefonów
`Phonenumber`jest zarządzany przez [Aparaty rozpoznawania tekstu —](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) repozytorium GitHub

## <a name="resolution-for-this-prebuilt-entity"></a>Rozwiązanie dla tej prekompilowanej jednostki

### <a name="api-version-2x"></a>Interfejs API w wersji 2. x

W poniższym przykładzie pokazano rozdzielczość **builtin.phonenumber** jednostki.

```json
{
  "query": "my mobile is 1 (800) 642-7676",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
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
}
```

### <a name="preview-api-version-3x"></a>Wersja zapoznawcza interfejsu API w wersji 3. x

Poniższy kod JSON jest z `verbose` parametrem ustawionym na: `false`

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
        "entities": {
            "phonenumber": [
                "1 (800) 642-7676"
            ]
        }
    }
}
```

Poniższy kod JSON jest z `verbose` parametrem ustawionym na: `true`

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
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
                        "score": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [procent](luis-reference-prebuilt-percentage.md), [numer](luis-reference-prebuilt-number.md), i [temperatury](luis-reference-prebuilt-temperature.md) jednostek. 
