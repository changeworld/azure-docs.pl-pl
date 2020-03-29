---
title: Prosty typ jednostki — USŁUGA LUIS
titleSuffix: Azure Cognitive Services
description: Prosta jednostka opisuje pojedynczą koncepcję z kontekstu nauczanego maszynowo. Dodaj listę fraz podczas korzystania z prostej jednostki, aby poprawić wyniki.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 8b92aa6057c81ec9442372c5b85918cb92196d61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74894758"
---
# <a name="simple-entity"></a>Prosta jednostka

Prosta jednostka jest ogólną jednostką, która opisuje pojedynczą koncepcję i jest uczony z kontekstu uczenia się maszynowego. Ponieważ proste jednostki są zazwyczaj nazwy, takie jak nazwy firm, nazwy produktów lub inne kategorie nazw, dodać [listę fraz,](luis-concept-feature.md) gdy za pomocą prostej jednostki, aby zwiększyć sygnał nazw używanych.

**Jednostka jest dobrze dopasowana, gdy:**

* Dane nie są konsekwentnie sformatowane, ale wskazują to samo.

![prosta jednostka](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Przykład JSON

`Bob Jones wants 3 meatball pho`

W poprzedniej wypowiedź, `Bob Jones` jest oznaczony `Customer` jako prosta jednostka.

Dane zwrócone z punktu końcowego obejmują nazwę jednostki, odnaleziony tekst z wypowiedź, lokalizację odnalezionego tekstu i wynik:

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania V3](#tab/V3)

Jest to JSON, jeśli `verbose=false` jest ustawiona w ciągu zapytania:

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
|Prosta jednostka|`Customer`|`bob jones`|

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się składnia wzorca](reference-pattern-syntax.md)