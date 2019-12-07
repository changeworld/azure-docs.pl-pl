---
title: Simple — typ jednostki — LUIS
titleSuffix: Azure Cognitive Services
description: Prosta Jednostka opisuje pojedyncze koncepcje z kontekstu uczenia maszynowego. Dodaj listę fraz przy użyciu prostej jednostki w celu poprawienia wyników.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 8b92aa6057c81ec9442372c5b85918cb92196d61
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894758"
---
# <a name="simple-entity"></a>Prosta jednostka

Prosta jednostka to ogólna jednostka, która opisuje pojedyncze koncepcje i jest poznania z kontekstu wykorzystanego przez maszynę. Ponieważ proste jednostki są zazwyczaj nazwami, takimi jak nazwy firmowe, nazwy produktów lub inne kategorie nazw, Dodaj [listę fraz](luis-concept-feature.md) przy użyciu prostej jednostki, aby zwiększyć sygnał użytych nazw.

**Jednostka jest dobrym dopasowaniem w przypadku:**

* Dane nie są ciągle sformatowane, ale wskazują na to samo.

![proste jednostki](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Przykładowy plik JSON

`Bob Jones wants 3 meatball pho`

W poprzednim wypowiedź `Bob Jones` jest oznaczona jako prosty `Customer` jednostki.

Dane zwrócone z punktu końcowego obejmuje nazwa jednostki, odnalezione tekst z wypowiedź, lokalizacja odnalezionych tekstu i oceny:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

Jest to kod JSON, jeśli `verbose=false` jest ustawiony w ciągu zapytania:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ]
}```

This is the JSON if `verbose=true` is set in the query string:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ],
    "$instance": {
        "Customer": [
            {
                "type": "Customer",
                "text": "Bob Jones",
                "startIndex": 0,
                "length": 9,
                "score": 0.9339134,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

|Obiekt danych|Nazwa jednostki|Wartość|
|--|--|--|
|Proste jednostki|`Customer`|`bob jones`|

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj składnię wzorca](reference-pattern-syntax.md)