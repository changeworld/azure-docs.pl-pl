---
title: Typ jednostki złożonej — LUIS
titleSuffix: Azure Cognitive Services
description: Jednostka złożona składa się z innych jednostek, takich jak wstępnie zbudowane jednostki, proste, wyrażenie regularne i jednostki listy. Oddzielne jednostki tworzą całość jednostki.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: a5a1ad467074ee0aa55d14d50ae153ac68304e6f
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695169"
---
# <a name="composite-entity"></a>Jednostka złożona 

Jednostka złożona składa się z innych jednostek, takich jak wstępnie zbudowane jednostki, proste, wyrażenie regularne i jednostki listy. Oddzielne jednostki tworzą całość jednostki. 

**Ta jednostka jest przydatna, gdy dane:**

* Są ze sobą powiązane. 
* Są powiązane ze sobą w kontekście wypowiedź.
* Używaj różnych typów jednostek.
* Należy grupować i przetwarzać przez aplikację kliencką jako jednostkę informacji.
* Posiadanie różnych wyrażenia długości użytkowników, które wymagają uczenia maszynowego.

![Jednostka złożona](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Przykładowy kod JSON

Rozważmy jednostkę złożoną prekompilowanego `number` i `Location::ToLocation` z następującymi wypowiedź:

`book 2 tickets to cairo`

Zwróć uwagę, że `2`, liczba i `cairo`, ToLocation zawierają słowa między nimi, które nie są częścią żadnych jednostek. Zielone podkreślenie używane w wypowiedź z etykietą w witrynie sieci Web [Luis](luis-reference-regions.md) wskazuje jednostkę złożoną.

![Jednostka złożona](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

Jednostki złożone są zwracane w tablicy `compositeEntities`, a wszystkie jednostki w ramach elementu złożonego są również zwracane w tablicy `entities`:

```JSON
  "entities": [
    {
      "entity": "2 tickets to cairo",
      "type": "ticketinfo",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.9214487
    },
    {
      "entity": "cairo",
      "type": "builtin.geographyV2.city",
      "startIndex": 18,
      "endIndex": 22
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "subtype": "integer",
        "value": "2"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "ticketinfo",
      "value": "2 tickets to cairo",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "builtin.geographyV2.city",
          "value": "cairo"
        }
      ]
    }
  ]
```    

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

Jest to kod JSON, jeśli w ciągu zapytania jest ustawiony `verbose=false`:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ]
        }
    ]
}
```

Jest to kod JSON, jeśli w ciągu zapytania jest ustawiony `verbose=true`:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 5,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2.city",
                        "text": "cairo",
                        "startIndex": 18,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "$instance": {
        "ticketinfo": [
            {
                "type": "ticketinfo",
                "text": "2 tickets to cairo",
                "startIndex": 5,
                "length": 18,
                "score": 0.9214487,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
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
|Wstępnie zbudowany numer jednostki|"wbudowane. Number"|dwóch|
|Wstępnie skonstruowany obiekt Entity-GeographyV2|"Lokalizacja:: ToLocation"|Kair|

## <a name="next-steps"></a>Następne kroki

W tym [samouczku](luis-tutorial-composite-entity.md)Dodaj **jednostkę złożoną** , aby powiązać wyodrębnione dane różnych typów w jedną zawierającą ją jednostkę. Poprzez zgrupowanie danych aplikacja kliencka może łatwo wyodrębnić powiązane dane w różnych typach danych.
