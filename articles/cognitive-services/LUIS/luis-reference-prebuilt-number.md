---
title: Liczba wstępnie utworzonych jednostek — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje o numerze wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 88f36fb6d73e2ec88940e7eb53d982824e194074
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560203"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Liczba wstępnie utworzonych jednostek dla aplikacji LUIS
Istnieje wiele sposobów, w których wartości liczbowe są używane do Szacowanie ilościowe, express i opisują informacje. W tym artykule opisano tylko niektóre z przykładów. Usługa LUIS interpretuje wahania wypowiedzi użytkowników i zwraca spójną wartości liczbowych. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierające liczbę intencji aplikacji. 

## <a name="types-of-number"></a>Typy liczb
Numer jest zarządzany przez [Aparaty rozpoznawania tekstu —](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) repozytorium GitHub

## <a name="examples-of-number-resolution"></a>Przykłady numer rozwiązania

| Wypowiedź        | Jednostka   | Rozwiązanie |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      | 
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


Usługa LUIS zawiera wartość **`builtin.number`** jednostki w `resolution` pole zwraca odpowiedź w formacie JSON.

## <a name="resolution-for-prebuilt-number"></a>Rozwiązania dla wbudowanych numeru


### <a name="api-version-2x"></a>Interfejs API w wersji 2. x

Poniższy kod przedstawia odpowiedź JSON, Luis, zawierającej rozpoznawanie wartości 24, wypowiedź "dwadzieścia".

```json
{
  "query": "order two dozen eggs",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.105443209
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.105443209
    },
    {
      "intent": "OrderFood",
      "score": 0.9468431361
    },
    {
      "intent": "Help",
      "score": 0.000399122015
    },
  ],
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "subtype": "integer",
        "value": "24"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Wersja zapoznawcza interfejsu API w wersji 3. x

Poniższy kod JSON jest z `verbose` parametrem ustawionym na: `false`

```json
{
    "query": "order two dozen eggs",
    "prediction": {
        "normalizedQuery": "order two dozen eggs",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "number": [
                24
            ]
        }
    }
}
```

Poniższy kod JSON jest z `verbose` parametrem ustawionym na: `true`

```json
{
    "query": "order two dozen eggs",
    "prediction": {
        "normalizedQuery": "order two dozen eggs",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "number": [
                24
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "two dozen",
                        "startIndex": 6,
                        "length": 9,
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

Dowiedz się więcej o [waluty](luis-reference-prebuilt-currency.md), [porządkowe](luis-reference-prebuilt-ordinal.md), i [procent](luis-reference-prebuilt-percentage.md). 
