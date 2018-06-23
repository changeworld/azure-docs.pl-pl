---
title: Wbudowane LUIS jednostek numer odwołania - Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Ten artykuł zawiera informacje o numerze jednostki wbudowane opis języka (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: aa0b389a0694a3b742259fd42bed08055fbbadbe
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321862"
---
# <a name="number-entity"></a>Liczba jednostek
Istnieje wiele sposobów, w których używa się wartości liczbowe określenie express i opisano informacje. W tym artykule omówiono niektóre możliwe przykłady. LUIS interpretuje zmienności zniesławiających użytkownika i zwraca spójne wartości liczbowych. Ponieważ była już uczona tej jednostki, jest konieczne Dodaj zniesławiających przykład zawierające liczbę opcji aplikacji. 

## <a name="types-of-number"></a>Typy numer
Liczba jest zarządzany z [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) repozytorium Github

## <a name="examples-of-number-resolution"></a>Przykłady numer rozwiązania

| utterance        | Jednostka   | Rozwiązanie |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      | 
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


Wartość zawiera LUIS **`builtin.number`** jednostki w `resolution` pole zwraca odpowiedź w formacie JSON.

## <a name="resolution-for-prebuilt-number"></a>Rozpoznawanie numer wbudowane
W poniższym przykładzie przedstawiono odpowiedź w formacie JSON z LUIS, zawierającą rozpoznanie wartość 24 utterance "dwadzieścia".

```JSON
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
        "value": "24"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [waluty](luis-reference-prebuilt-currency.md), [porządkowej](luis-reference-prebuilt-ordinal.md), i [procent](luis-reference-prebuilt-percentage.md). 