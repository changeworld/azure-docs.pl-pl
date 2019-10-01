---
title: Simple — typ jednostki — LUIS
titleSuffix: Azure Cognitive Services
description: Prosta jednostka to ogólna jednostka, która opisuje pojedyncze koncepcje i jest poznania z kontekstu wykorzystanego przez maszynę. Ponieważ proste jednostki są zazwyczaj nazwami, takimi jak nazwy firmowe, nazwy produktów lub inne kategorie nazw, Dodaj listę fraz przy użyciu prostej jednostki, aby zwiększyć sygnał użytych nazw.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 2eb3ff847f9bfc162adfb281d2ac1fad6f8c5093
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695127"
---
# <a name="simple-entity"></a>Jednostka prosta 

Prosta jednostka to ogólna jednostka, która opisuje pojedyncze koncepcje i jest poznania z kontekstu wykorzystanego przez maszynę. Ponieważ proste jednostki są zazwyczaj nazwami, takimi jak nazwy firmowe, nazwy produktów lub inne kategorie nazw, Dodaj [listę fraz](luis-concept-feature.md) przy użyciu prostej jednostki, aby zwiększyć sygnał użytych nazw. 

**Jednostka jest dobrym dopasowaniem w przypadku:**

* Dane nie są ciągle sformatowane, ale wskazują na to samo. 

![Jednostka prosta](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Przykładowy kod JSON

`Bob Jones wants 3 meatball pho`

W poprzednim wypowiedź `Bob Jones` jest oznaczona jako prosta jednostka `Customer`.

Dane zwrócone z punktu końcowego zawierają nazwę jednostki, odnaleziony tekst z wypowiedź, lokalizację odnalezionego tekstu oraz wynik:

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

Jest to kod JSON, jeśli w ciągu zapytania jest ustawiony `verbose=false`:

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
|Jednostka prosta|`Customer`|`bob jones`|

## <a name="next-steps"></a>Następne kroki

W tym [samouczku](luis-quickstart-primary-and-secondary-data.md)Wyodrębnij dane o nazwie zadania zatrudnienia z wypowiedź przy użyciu **jednostki prostej**. Aby zwiększyć dokładność wyodrębniania, Dodaj [listę fraz](luis-concept-feature.md) dla warunków specyficznych dla jednostki prostej.