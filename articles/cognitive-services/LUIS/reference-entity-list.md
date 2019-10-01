---
title: List — typ jednostki — LUIS
titleSuffix: Azure Cognitive Services
description: Jednostki listy reprezentują stały, zamknięty zestaw powiązanych słów wraz z ich synonimami. LUIS nie odnajduje dodatkowych wartości dla jednostek listy. Użyj opcji zalecamy, aby zobaczyć sugestie dotyczące nowych słów na podstawie bieżącej listy.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 1757faf8ab2be0b62956b6939ee068929f9275a4
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695254"
---
# <a name="list-entity"></a>Jednostka listy 

Jednostki listy reprezentują stały, zamknięty zestaw powiązanych słów wraz z ich synonimami. LUIS nie odnajduje dodatkowych wartości dla jednostek listy. Użyj opcji **zalecamy** , aby zobaczyć sugestie dotyczące nowych słów na podstawie bieżącej listy. Jeśli istnieje więcej niż jedna jednostka listy o tej samej wartości, każda jednostka zostanie zwrócona w zapytaniu punktu końcowego. 

Jednostka listy nie jest zauczenia maszynowego. Jest to dokładne dopasowanie tekstu. LUIS oznacza dowolne dopasowanie do elementu na dowolnej liście jako jednostki w odpowiedzi. 

**Jednostka jest dobrym dopasowaniem, gdy dane tekstowe:**

* Są znanym zestawem.
* Nie zmienia się często. Jeśli musisz zmienić listę często lub chcesz, aby lista została powiększana, to lepszy wybór jest prostą jednostką z listą fraz. 
* Zestaw nie przekracza maksymalnej [wartości Luis dla](luis-boundaries.md) tego typu jednostki.
* Tekst w wypowiedź jest dokładnym dopasowaniem synonimu lub nazwy kanonicznej. LUIS nie używa listy poza dokładnymi dopasowaniami do tekstu. Dopasowywanie rozmyte, bez uwzględniania wielkości liter, odporności, pluralizmu i innych wariantów nie są rozpoznawane za pomocą jednostki listy. Aby zarządzać odmianami, należy rozważyć użycie [wzorca](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) z opcjonalną składnią tekstu.

![Jednostka listy](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>Przykładowy kod JSON

Załóżmy, że aplikacja ma listę o nazwie `Cities`, co pozwala na odmianę nazw miast, w tym miasto lotniska (Sea-Tac), kod lotniska (SEA), pocztowy kod pocztowy (98101) i numer kierunkowy telefonu (206).

|Element listy|Synonimy elementów|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

W poprzednim wypowiedź słowo `paris` jest zamapowane na element paryski jako część jednostki listy `Cities`. Jednostka listy dopasowuje zarówno znormalizowaną nazwę elementu, jak i synonimy elementów.

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


Jest to kod JSON, jeśli w ciągu zapytania jest ustawiony `verbose=false`:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Jest to kod JSON, jeśli w ciągu zapytania jest ustawiony `verbose=true`:

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

W tym [samouczku](luis-quickstart-intent-and-list-entity.md)dowiesz się, jak za pomocą **jednostki listy** wyodrębnić dokładne dopasowania tekstu z listy znanych elementów. 
