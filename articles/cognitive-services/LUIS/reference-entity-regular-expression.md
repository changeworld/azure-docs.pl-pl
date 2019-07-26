---
title: Typ jednostki wyrażenia regularnego
titleSuffix: Language Understanding - Azure Cognitive Services
description: Wyrażenie regularne jest najlepsze dla nieprzetworzonego tekstu wypowiedź. On ignoruje wielkość liter i ignoruje wariant kultury.  Dopasowywanie wyrażeń regularnych są stosowane po sprawdzania pisowni zmiany na poziomie znak, a nie na poziomie tokenu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: dfffea26ccd8dfcecea2c6a2e07234f3ab27f72b
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480232"
---
# <a name="regular-expression-entity"></a>Jednostka wyrażenia regularnego 

Jednostka wyrażenia regularnego wyodrębnia jednostkę na podstawie podania wzorca wyrażenia regularnego.

Wyrażenie regularne jest najlepsze dla nieprzetworzonego tekstu wypowiedź. On ignoruje wielkość liter i ignoruje wariant kultury.  Dopasowywanie wyrażeń regularnych są stosowane po sprawdzania pisowni zmiany na poziomie znak, a nie na poziomie tokenu. Jeśli wyrażenie regularne jest zbyt złożone, na przykład przy użyciu wielu nawiasów, nie można dodać wyrażenia do modelu. Używa części, ale nie całej biblioteki [wyrażeń regularnych programu .NET](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) . 

**Jednostka jest dobrym dopasowaniem w przypadku:**

* Dane są spójne z uwzględnieniem spójnych zmian.
* Wyrażenie regularne nie wymaga więcej niż 2 poziomów zagnieżdżenia. 

![Jednostka wyrażenia regularnego](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Zagadnienia dotyczące użycia

Wyrażenia regularne mogą odpowiadać więcej niż oczekiwano. Przykładem jest numeryczne Dopasowywanie wyrazów, `one` takie `two`jak i. Przykładem jest następujące wyrażenie regularne, które pasuje do liczby `one` wraz z innymi liczbami:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
``` 

To wyrażenie regularne również dopasowuje wszystkie wyrazy kończące się tymi liczbami, `phone`na przykład. Aby rozwiązać te problemy, należy się upewnić, że dopasowuje wyrażenie regularne uwzględnia granice wyrazu. Wyrażenie regularne do używania granic wyrazów dla tego przykładu jest używane w następujących wyrażeniach regularnych:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Przykładowy plik JSON

W przypadku `kb[0-9]{6}`użycia, jako definicji jednostki wyrażenia regularnego następująca odpowiedź JSON to przykład wypowiedź z zwracanymi jednostkami wyrażenia regularnego dla zapytania: `When was kb123456 published?`

```JSON
{
  "query": "when was kb123456 published?",
  "topScoringIntent": {
    "intent": "FindKBArticle",
    "score": 0.933641255
  },
  "intents": [
    {
      "intent": "FindKBArticle",
      "score": 0.933641255
    },
    {
      "intent": "None",
      "score": 0.04397359
    }
  ],
  "entities": [
    {
      "entity": "kb123456",
      "type": "KB number",
      "startIndex": 9,
      "endIndex": 16
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

W tym [samouczku](luis-quickstart-intents-regex-entity.md)utworzysz aplikację do wyodrębniania spójnie sformatowanych danych z wypowiedź przy użyciu jednostki **wyrażenia regularnego** .