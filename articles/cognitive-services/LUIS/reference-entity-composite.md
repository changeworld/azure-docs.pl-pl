---
title: Typ jednostki złożonej
titleSuffix: Language Understanding - Azure Cognitive Services
description: Jednostka złożona składa się z innych jednostek, takich jak wstępnie zbudowane jednostki, proste, wyrażenie regularne i jednostki listy. Osobne jednostki tworzą całej jednostki.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: 55dcc5666e63b8a87ddcb13696991fe02843fbbd
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480155"
---
# <a name="composite-entity"></a>Złożone jednostki 

Jednostka złożona składa się z innych jednostek, takich jak wstępnie zbudowane jednostki, proste, wyrażenie regularne i jednostki listy. Osobne jednostki tworzą całej jednostki. 

**Ta jednostka jest przydatna, gdy dane:**

* Są ze sobą powiązane. 
* Są ze sobą powiązane w kontekście wypowiedzi.
* Używać różnych typów jednostek.
* Należy grupować i przetwarzać przez aplikację kliencką jako jednostkę informacji.
* Posiadanie różnych wyrażenia długości użytkowników, które wymagają uczenia maszynowego.

![Złożone jednostki](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Przykładowy plik JSON

Weź pod uwagę złożone jednostki `number` prekompilowane `Location::ToLocation` i z następującymi wypowiedź:

`book 2 tickets to paris`

Należy zauważyć, że `2`, liczba i `paris`, ToLocation mają słowa między nimi, które nie należą do żadnej z jednostki. Zielonym podkreśleniem, używane w etykietami wypowiedź w [LUIS](luis-reference-regions.md) witryny sieci Web, wskazuje obiekt złożony.

![Złożone jednostki](./media/luis-concept-data-extraction/composite-entity.png)

Złożone jednostki są zwracane w `compositeEntities` tablicy i wszystkich jednostek w ramach złożonego jest także zwracany w `entities` tablicy:

```JSON

"entities": [
    {
    "entity": "2 tickets to cairo",
    "type": "ticketInfo",
    "startIndex": 0,
    "endIndex": 17,
    "score": 0.67200166
    },
    {
    "entity": "2",
    "type": "builtin.number",
    "startIndex": 0,
    "endIndex": 0,
    "resolution": {
        "subtype": "integer",
        "value": "2"
    }
    },
    {
    "entity": "cairo",
    "type": "builtin.geographyV2",
    "startIndex": 13,
    "endIndex": 17
    }
],
"compositeEntities": [
    {
    "parentType": "ticketInfo",
    "value": "2 tickets to cairo",
    "children": [
        {
        "type": "builtin.geographyV2",
        "value": "cairo"
        },
        {
        "type": "builtin.number",
        "value": "2"
        }
    ]
    }
]
```    

|Obiekt danych|Nazwa jednostki|Wartość|
|--|--|--|
|Wstępnie utworzone jednostki — liczba|"builtin.number"|"2"|
|Wstępnie skonstruowany obiekt Entity-GeographyV2|"Location::ToLocation"|"Paryż"|

## <a name="next-steps"></a>Kolejne kroki

W tym [samouczku](luis-tutorial-composite-entity.md)Dodaj **jednostkę złożoną** , aby powiązać wyodrębnione dane różnych typów w jedną zawierającą ją jednostkę. Przez tworzenie pakietów danych, aplikacja kliencka może łatwo wyodrębnić powiązane dane w różnych typów danych.
