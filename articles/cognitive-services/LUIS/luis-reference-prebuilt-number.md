---
title: Liczba wstępnie utworzonej jednostki — USŁUGA LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje o jednostce utworzonej numer w yrozmaicieniu języka (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 85e3589d7467691e2b9a11879510ab980bbd875a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273463"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Numer wstępnie utworzonej jednostki dla aplikacji usługi LUIS
Istnieje wiele sposobów, w których wartości liczbowe są używane do kwantyfikacji, wyrażania i opisywania informacji. W tym artykule opisano tylko niektóre z możliwych przykładów. Usługa LUIS interpretuje różnice w wypowiedziach użytkowników i zwraca spójne wartości liczbowe. Ponieważ ta jednostka jest już przeszkolony, nie trzeba dodawać wypowiedzi przykład zawierający liczbę do intencji aplikacji.

## <a name="types-of-number"></a>Typy numerów
Numer jest zarządzany z repozytorium GitHub [w tekście aparatury-aparaty rozpoznawania](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml)

## <a name="examples-of-number-resolution"></a>Przykłady rozpoznawania numerów

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


Usługa LUIS zawiera rozpoznaną **`builtin.number`** wartość `resolution` encji w polu zwracanej odpowiedzi JSON.

## <a name="resolution-for-prebuilt-number"></a>Rozdzielczość dla wstępnie utworzonego numeru

Następujące obiekty encji są zwracane dla kwerendy:

`order two dozen eggs`

#### <a name="v3-response"></a>[Odpowiedź V3](#tab/V3)

Następujący JSON jest `verbose` z parametrem ustawionym na: `false`

```json
"entities": {
    "number": [
        24
    ]
}
```
#### <a name="v3-verbose-response"></a>[Odpowiedź pełne V3](#tab/V3-verbose)

Następujący JSON jest `verbose` z parametrem ustawionym na: `true`

```json
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

Poniższy przykład pokazuje odpowiedź JSON z usługi LUIS, która obejmuje rozdzielczość wartości 24 dla wypowiedź "dwa tuziny".

```json
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
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

Dowiedz się więcej o [walucie](luis-reference-prebuilt-currency.md), [liczba porządkowa](luis-reference-prebuilt-ordinal.md)i [procent](luis-reference-prebuilt-percentage.md).
