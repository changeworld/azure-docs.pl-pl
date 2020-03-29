---
title: Pattern.any typ jednostki — USŁUGA LUIS
titleSuffix: Azure Cognitive Services
description: Pattern.any jest symbolem zastępczym o zmiennej długości używanym tylko w wypowiedź szablonu wzorca, aby oznaczyć, gdzie jednostka zaczyna się i kończy.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 5164bf55ef8233cf34a470524da3bc852678d79a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75979176"
---
# <a name="patternany-entity"></a>Jednostka Pattern.any

Pattern.any jest symbolem zastępczym o zmiennej długości używanym tylko w wypowiedź szablonu wzorca, aby oznaczyć, gdzie jednostka zaczyna się i kończy.  

Pattern.any jednostki muszą być oznaczone w [przykładach szablonu wzorca,](luis-how-to-model-intent-pattern.md) a nie przykłady użytkowników intencji.

**Jednostka jest dobrze dopasowana, gdy:**

* Zakończenie jednostki można pomylić z pozostałym tekstem wypowiedź.

## <a name="usage"></a>Sposób użycia

Biorąc pod uwagę aplikację kliencką, która wyszukuje książki na podstawie tytułu, pattern.any wyodrębnia pełny tytuł. Wypowiedź szablonu przy użyciu pattern.any dla tego wyszukiwania książki jest `Was {BookTitle} written by an American this year[?]`.

W poniższej tabeli każdy wiersz ma dwie wersje wypowiedź. Najwyższa wypowiedź jest jak usługa LUIS początkowo widzi wypowiedź. Nie jest jasne, gdzie zaczyna się i kończy tytuł książki. Wypowiedź dolna używa Pattern.any jednostki, aby oznaczyć początek i koniec jednostki.

|Wypowiedź z jednostką pogrubiona|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Czy **człowiek, który pomylił swoją żonę na kapelusz i inne kliniczne opowieści** napisane przez Amerykanina w tym roku?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>Czy **Half Asleep w Frog Piżamy** napisane przez Amerykanina w tym roku?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Czy **szczególny smutek Lemon Cake: Powieść** napisana przez Amerykanina w tym roku?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>Czy **w mojej kieszeni jest Wocket!** napisany przez Amerykanina w tym roku?|
||



## <a name="example-json"></a>Przykład JSON

Rozpatrzmy następujące zapytanie:

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

Z osadzoną nazwą formularza, aby wyodrębnić jako Pattern.any:

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v2](#tab/V2)

```JSON
"entities": [
  {
    "entity": "understand your responsibilities as a member of the community",
    "type": "FormName",
    "startIndex": 18,
    "endIndex": 78,
    "role": ""
  }
```


#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania V3](#tab/V3)

Jest to JSON, jeśli `verbose=false` jest ustawiona w ciągu zapytania:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

Jest to JSON, jeśli `verbose=true` jest ustawiona w ciągu zapytania:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ],
    "$instance": {
        "FormName": [
            {
                "type": "FormName",
                "text": "Understand your responsibilities as a member of the community",
                "startIndex": 18,
                "length": 61,
                "modelTypeId": 7,
                "modelType": "Pattern.Any Entity Extractor",
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

W tym [samouczku](luis-tutorial-pattern.md)użyj **Pattern.any** jednostki wyodrębnić dane z wypowiedzi, gdzie wypowiedzi są dobrze sformatowane i gdzie koniec danych można łatwo pomylić z pozostałymi słowami wypowiedź.
