---
title: Usługa LUIS ze wstępnie utworzonych jednostek numer odwołania — Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Ten artykuł zawiera informacje o numerze wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 4d14a3490d87693a4785111035b62e7ae59ee669
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041850"
---
# <a name="number-entity"></a>Number, jednostka
Istnieje wiele sposobów, w których wartości liczbowe są używane do Szacowanie ilościowe, express i opisują informacje. W tym artykule opisano tylko niektóre z przykładów. Usługa LUIS interpretuje wahania wypowiedzi użytkowników i zwraca spójną wartości liczbowych. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierające liczbę intencji aplikacji. 

## <a name="types-of-number"></a>Typy liczb
Liczba jest zarządzana z [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) repozytorium Github

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
Poniższy kod przedstawia odpowiedź JSON, Luis, zawierającej rozpoznawanie wartości 24, wypowiedź "dwadzieścia".

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

Dowiedz się więcej o [waluty](luis-reference-prebuilt-currency.md), [porządkowe](luis-reference-prebuilt-ordinal.md), i [procent](luis-reference-prebuilt-percentage.md). 