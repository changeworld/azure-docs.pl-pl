---
title: Typ jednostki listy — USŁUGA LUIS
description: Encje listy reprezentują stały, zamknięty zestaw powiązanych słów wraz z ich synonimami. Usługa LUIS nie odnajduje dodatkowych wartości dla jednostek listy. Użyj funkcji Poleć, aby wyświetlić sugestie dotyczące nowych słów na podstawie bieżącej listy.
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 795d16bc2e0c4223ff3ac283a72493923d3ab355
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297241"
---
# <a name="list-entity"></a>Jednostka listy

Encje listy reprezentują stały, zamknięty zestaw powiązanych słów wraz z ich synonimami. Usługa LUIS nie odnajduje dodatkowych wartości dla jednostek listy. Użyj funkcji **Poleć,** aby wyświetlić sugestie dotyczące nowych słów na podstawie bieżącej listy. Jeśli istnieje więcej niż jedna encja listy o tej samej wartości, każda jednostka jest zwracana w kwerendzie punktu końcowego.

Jednostka listy nie jest nauczona maszynowo. Jest to dokładne dopasowanie tekstu. Usługa LUIS oznacza dowolne dopasowanie do elementu na dowolnej liście jako encję w odpowiedzi.

**Encja jest dobrze dopasowana, gdy dane tekstowe:**

* Są znanym zestawem.
* Nie zmienia się często. Jeśli chcesz często zmieniać listę lub chcesz, aby lista się rozwijała, lepszym wyborem jest prosta encja promowana za pomocą listy fraz.
* Zestaw nie przekracza maksymalnych [granic](luis-boundaries.md) usługi LUIS dla tego typu jednostki.
* Tekst w wypowiedź jest dopasowanie bez uwzględniania wielkości liter z synonimem lub nazwy kanonicznej. Usługa LUIS nie używa listy poza dopasowaniem. Dopasowywanie rozmyte, wynikające, liczba mnoga i inne odmiany nie są rozpoznawane za pomocą jednostki listy. Aby zarządzać wariacjami, rozważ użycie [wzorca](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) z opcjonalną składnią tekstu.

![encja listy](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>Przykład .json do zaimportowania do encji listy

  Wartości można importować do istniejącej encji listy przy użyciu następującego formatu .json:

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

Załóżmy, że aplikacja `Cities`ma listę, o nazwie , co pozwala na zmiany nazw miast, w tym miasta lotniska (Sea-tac), kod lotniska (SEA), pocztowy kod pocztowy (98101) i numer kierunkowy telefonu (206).

|Element listy|Synonimy przedmiotów|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

W poprzedniej wypowiedź `paris` słowo jest mapowane do elementu `Cities` paris jako część jednostki listy. Encja listy jest zgodna zarówno znormalizowaną nazwą elementu, jak i synonimami elementu.

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania V3](#tab/V3)


Jest to JSON, jeśli `verbose=false` jest ustawiona w ciągu zapytania:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Jest to JSON, jeśli `verbose=true` jest ustawiona w ciągu zapytania:

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
|Encja listy|`Cities`|`paris`|


## <a name="next-steps"></a>Następne kroki

W tym [samouczku](tutorial-list-entity.md)dowiesz się, jak użyć **jednostki listy** do wyodrębniania dokładnych dopasowań tekstu z listy znanych elementów.
