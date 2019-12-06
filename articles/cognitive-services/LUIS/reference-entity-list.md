---
title: List — typ jednostki — LUIS
titleSuffix: Azure Cognitive Services
description: Jednostki listy reprezentują stały, zamknięty zestaw powiązanych słów wraz z ich synonimami. Usługa LUIS nie wykrywa dodatkowe wartości dla jednostek z listy. Użyj opcji zalecamy, aby zobaczyć sugestie dotyczące nowych słów na podstawie bieżącej listy.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 4313a1d644750c0961298bbee3ae211946de360a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849772"
---
# <a name="list-entity"></a>Jednostka listy

Jednostki listy reprezentują stały, zamknięty zestaw powiązanych słów wraz z ich synonimami. Usługa LUIS nie wykrywa dodatkowe wartości dla jednostek z listy. Użyj **zaleca się** funkcji, aby zobaczyć sugestie dotyczące nowych słów na podstawie bieżącej listy. Jeśli istnieje więcej niż jednej jednostki listy z taką samą wartość, każdy obiekt jest zwracany w kwerendy punktu końcowego.

Jednostka listy nie jest zauczenia maszynowego. Konieczne jest dopasowanie tekstu do dokładnego dopasowania. Usługa LUIS oznacza jakiegokolwiek dopasowania do elementu w dowolnej listy jako jednostki w odpowiedzi.

**Jednostka jest dobrym dopasowaniem, gdy dane tekstowe:**

* Są znanym zestawem.
* Nie zmienia się często. Jeśli musisz zmienić listę często lub chcesz, aby lista została powiększana, to lepszy wybór jest prostą jednostką z listą fraz.
* Zestaw nie przekracza maksymalnych [granic](luis-boundaries.md) usługi LUIS dla tego typu jednostki.
* Tekst w wypowiedzi to dokładne dopasowanie synonimu lub nazwy kanonicznej. Usługa LUIS nie korzysta z listy poza dokładnymi dopasowaniami tekstu. Dopasowywanie rozmyte, bez uwzględniania wielkości liter, odporności, pluralizmu i innych wariantów nie są rozpoznawane za pomocą jednostki listy. Aby zarządzać wariacjami, rozważ użycie [wzorca](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) z opcjonalną składnią tekstu.

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

W tym [samouczku](tutorial-list-entity.md)dowiesz się, jak za pomocą **jednostki listy** wyodrębnić dokładne dopasowania tekstu z listy znanych elementów.
