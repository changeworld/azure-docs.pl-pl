---
title: Typ jednostki o Poznaniu maszyn — LUIS
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 2289e8ac7744a7b4cbee300e77efda89d29ee2f5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74017220"
---
# <a name="machine-learned-entity"></a>Jednostka wyuczenia maszynowego 



**Jednostka jest dobrym dopasowaniem, gdy dane tekstowe:**


![Lista jednostek](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>Przykładowy plik JSON

Załóżmy, że aplikacja ma listę o nazwie `Cities`, dzięki czemu dla zmian nazwy miast, w tym mieście Kuwejcie (Sea tac), kod lotniska (SEA), kod pocztowy zip (98101) i numer kierunkowy telefonu (206).

|Element listy|Synonimy — element|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

W poprzednim wypowiedź, wyraz `paris` jest mapowany na element Paryż jako część `Cities` listy jednostek. Jednostka listy dopasowuje zarówno znormalizowaną nazwę elementu, jak i synonimy elementu.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)


Jest to kod JSON, jeśli `verbose=false` jest ustawiony w ciągu zapytania:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Jest to kod JSON, jeśli `verbose=true` jest ustawiony w ciągu zapytania:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
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
|Jednostka listy|`Cities`|`paris`|


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat jednostki [listy](reference-entity-list.md) i jednostki [wyrażenia regularnego](reference-entity-regular-expression.md) .