---
title: List — typ jednostki — LUIS
description: Jednostki listy reprezentują stały, zamknięty zestaw powiązanych słów wraz z ich synonimami. Usługa LUIS nie wykrywa dodatkowe wartości dla jednostek z listy. Użyj opcji zalecamy, aby zobaczyć sugestie dotyczące nowych słów na podstawie bieżącej listy.
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 795d16bc2e0c4223ff3ac283a72493923d3ab355
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297241"
---
# <a name="list-entity"></a>Jednostka listy

Jednostki listy reprezentują stały, zamknięty zestaw powiązanych słów wraz z ich synonimami. Usługa LUIS nie wykrywa dodatkowe wartości dla jednostek z listy. Użyj opcji **zalecamy** , aby zobaczyć sugestie dotyczące nowych słów na podstawie bieżącej listy. Jeśli istnieje więcej niż jednej jednostki listy z taką samą wartość, każdy obiekt jest zwracany w kwerendy punktu końcowego.

Jednostka listy nie jest zauczenia maszynowego. Konieczne jest dopasowanie tekstu do dokładnego dopasowania. Usługa LUIS oznacza jakiegokolwiek dopasowania do elementu w dowolnej listy jako jednostki w odpowiedzi.

**Jednostka jest dobrym dopasowaniem, gdy dane tekstowe:**

* Są znanym zestawem.
* Nie zmienia się często. Jeśli musisz zmienić listę często lub chcesz, aby lista została powiększana, to lepszy wybór jest prostą jednostką z listą fraz.
* Zestaw nie przekracza maksymalnych [granic](luis-boundaries.md) usługi LUIS dla tego typu jednostki.
* Tekst w wypowiedź jest dopasowaniem bez uwzględniania wielkości liter z synonimem lub nazwą kanoniczną. LUIS nie używa listy poza dopasowaniem. Dopasowywanie rozmyte, Rdzeniowanie, plural i inne odmiany nie są rozpoznawane za pomocą jednostki listy. Aby zarządzać wariacjami, rozważ użycie [wzorca](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) z opcjonalną składnią tekstu.

![Lista jednostek](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>Przykład. JSON do zaimportowania do jednostki listy

  Możesz zaimportować wartości do istniejącej jednostki listy przy użyciu następującego formatu JSON:

  ```JSON
  [
      {
          "canonicalForm": "Blue",
          "list": [
              "navy",
              "royal",
              "baby"
          ]
      },
      {
          "canonicalForm": "Green",
          "list": [
              "kelly",
              "forest",
              "avacado"
          ]
      }
  ]
  ```

## <a name="example-json-response"></a>Przykładowa odpowiedź JSON

Załóżmy, że aplikacja ma listę o nazwie `Cities`, co pozwala na różnice nazw miast, w tym miasto lotniska (Sea-Tac), kod lotniska (SEA), pocztowy kod pocztowy (98101) i numer kierunkowy telefonu (206).

|Element listy|Synonimy — element|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1``+33`|

`book 2 tickets to paris`

W poprzednim wypowiedź słowa `paris` są mapowane na element paryski jako część jednostki `Cities` listy. Jednostka listy dopasowuje zarówno znormalizowaną nazwę elementu, jak i synonimy elementu.

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)


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

W tym [samouczku](tutorial-list-entity.md)dowiesz się, jak za pomocą **jednostki listy** wyodrębnić dokładne dopasowania tekstu z listy znanych elementów.
