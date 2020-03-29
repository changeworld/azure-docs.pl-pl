---
title: Typ jednostki złożonej — USŁUGA LUIS
titleSuffix: Azure Cognitive Services
description: Jednostka złożona składa się z innych jednostek, takich jak wstępnie utworzone jednostki, proste, wyrażenie regularne i jednostki listy. Oddzielne jednostki tworzą całą encję.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: a5a1ad467074ee0aa55d14d50ae153ac68304e6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71695169"
---
# <a name="composite-entity"></a>Jednostka złożona 

Jednostka złożona składa się z innych jednostek, takich jak wstępnie utworzone jednostki, proste, wyrażenie regularne i jednostki listy. Oddzielne jednostki tworzą całą encję. 

**Ta jednostka jest dobrym rozwiązaniem, gdy dane:**

* Są ze sobą spokrewnione. 
* Są ze sobą powiązane w kontekście wypowiedzi.
* Użyj różnych typów encji.
* Muszą być pogrupowane i przetwarzane przez aplikację kliencką jako jednostkę informacji.
* Mają wiele wypowiedzi użytkownika, które wymagają uczenia maszynowego.

![jednostka złożona](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Przykład JSON

Należy wziąć pod uwagę `number` `Location::ToLocation` złożoną jednostkę wstępnie utworzone i z następującym wypowiedź:

`book 2 tickets to cairo`

Należy `2`zauważyć, że `cairo`, liczba i , ToLocation mają słowa między nimi, które nie są częścią żadnej z jednostek. Zielone podkreślenie, używane w wypowiedź etykietą w witrynie sieci Web [usługi LUIS,](luis-reference-regions.md) wskazuje jednostki złożonej.

![Jednostka złożona](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v2](#tab/V2)

Jednostki złożone są `compositeEntities` zwracane w tablicy i wszystkie jednostki w kompozyt są również zwracane w `entities` tablicy:

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

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania V3](#tab/V3)

Jest to JSON, jeśli `verbose=false` jest ustawiona w ciągu zapytania:

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

Jest to JSON, jeśli `verbose=true` jest ustawiona w ciągu zapytania:

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
|Wstępnie utworzony encji - numer|"wbudowany.numer"|"2"|
|Wstępnie zbudowany encja - GeographyV2|"Lokalizacja::ToLokacja"|"kair"|

## <a name="next-steps"></a>Następne kroki

W tym [samouczku](luis-tutorial-composite-entity.md)dodaj **jednostkę złożoną,** aby połączyć wyodrębnione dane różnych typów do jednej jednostki zawierającej. Łącząc dane, aplikacja kliencka może łatwo wyodrębnić powiązane dane w różnych typach danych.
