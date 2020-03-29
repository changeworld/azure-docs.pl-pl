---
title: Typ jednostki wyrażenia regularnego — USŁUGA LUIS
titleSuffix: Azure Cognitive Services
description: Wyrażenie regularne jest najlepsze dla nieprzetworzonego tekstu wypowiedź. Ignoruje przypadek i ignoruje wariant kulturowy.  Dopasowywanie wyrażeń regularnych jest stosowane po zmianie sprawdzania pisowni na poziomie znaku, a nie na poziomie tokenu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: b9da76a80183f353a74d43e667bf6c9219eb6c05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74841221"
---
# <a name="regular-expression-entity"></a>Jednostka wyrażenia regularnego

Jednostka wyrażenia regularnego wyodrębnia jednostkę na podstawie wzorca wyrażenia regularnego, który podasz.

Wyrażenie regularne jest najlepsze dla nieprzetworzonego tekstu wypowiedź. Ignoruje przypadek i ignoruje wariant kulturowy.  Dopasowywanie wyrażeń regularnych jest stosowane po zmianie sprawdzania pisowni na poziomie znaku, a nie na poziomie tokenu. Jeśli wyrażenie regularne jest zbyt złożone, na przykład przy użyciu wielu nawiasów, nie można dodać wyrażenia do modelu. Używa części, ale nie całej biblioteki [regex .NET.](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions)

**Jednostka jest dobrze dopasowana, gdy:**

* Dane są konsekwentnie sformatowane z każdą odmianą, która jest również spójna.
* Wyrażenie regularne nie wymaga więcej niż 2 poziomy zagnieżdżania.

![Jednostka wyrażenia regularnego](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Zagadnienia dotyczące użycia

Wyrażenia regularne mogą odpowiadać więcej niż można się spodziewać. Przykładem tego jest dopasowywanie `one` `two`słów liczbowych, takich jak i . Przykładem jest następujący wyrażenie regularne, `one` które pasuje do liczby wraz z innymi liczbami:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

To wyrażenie wyrażenia wyrażenia regularnego pasuje również do wszystkich `phone`wyrazów, które kończą się tymi liczbami, takich jak . Aby rozwiązać takie problemy, upewnij się, że dopasowania wyrażenia regularnego uwzględniają granice słów. Wyrażenie regularne do użycia granic słów w tym przykładzie jest używane w następującym wyrażeniem.

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Przykład JSON

Podczas `kb[0-9]{6}`korzystania z , jako definicji jednostki wyrażenia regularnego, następująca odpowiedź JSON jest wypowiedź przykład z zwracanych jednostek wyrażenia regularnego dla kwerendy:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v2](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania V3](#tab/V3)


Jest to JSON, jeśli `verbose=false` jest ustawiona w ciągu zapytania:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Jest to JSON, jeśli `verbose=true` jest ustawiona w ciągu zapytania:

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>Następne kroki

W tym [samouczku](tutorial-regex-entity.md)utwórz aplikację do wyodrębniania spójnie sformatowanych danych z wypowiedź przy użyciu **jednostki wyrażenia regularnego.**