---
title: Wzorzec. dowolny typ jednostki
titleSuffix: Language Understanding - Azure Cognitive Services
description: Wzorzec. any jest symbolem zastępczym o zmiennej długości używanym tylko w szablonie wzorca wypowiedź, aby oznaczyć, gdzie rozpoczyna się i kończą.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: f3e5bd068b5df99bdfabff0bfe18ccebff980fc6
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480166"
---
# <a name="patternany-entity"></a>Jednostka Pattern.any 

Wzorzec. any jest symbolem zastępczym o zmiennej długości używanym tylko w szablonie wzorca wypowiedź, aby oznaczyć, gdzie rozpoczyna się i kończą.  

Wzorzec. wszystkie jednostki muszą być oznaczone jako przykłady szablonu [wzorca](luis-how-to-model-intent-pattern.md) , a nie przykłady użytkownika.

**Jednostka jest dobrym dopasowaniem w przypadku:**

* Koniec jednostki można pomylić z pozostałym tekstem wypowiedź. 

## <a name="usage"></a>Użycie

Dana aplikacja kliencka, która wyszukuje książki na podstawie tytułu, wzorca. wszystkie wyodrębnia pełny tytuł. Szablon wypowiedź przy użyciu wzorca. wszystkie te wyszukiwania są `Was {BookTitle} written by an American this year[?]`następujące. 

W poniższej tabeli każdy wiersz ma dwie wersje wypowiedź. Górna wypowiedź to sposób, w jaki LUIS początkowo widzi wypowiedź. Nie jest jasne, gdzie rozpoczyna się i kończą tytuł książki. Dolna wypowiedź używa wzorca. Każda jednostka do oznaczenia początku i końca jednostki. 

|Wypowiedź z jednostką pogrubioną|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>**Czy człowiek, który wykonał niezawodną swoją żona dla Hat i innych klinicznych wskaźnikami** , w tym roku?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>Czy **w ramach żabichu Pajamas** wypisano połowę w tym roku?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Był **to konkretny smutek z ciastka cytrynowego: Autor, który zostałzapisanyprzezamerykańskiewtymroku?**|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>**Czy w moim urządzeniu Pocket został Wocket** Czy ten rok został zapisany przez amerykańskie?|
||

## <a name="example-json"></a>Przykładowy plik JSON

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

W tym [samouczku](luis-tutorial-pattern-any.md)Użyj **wzorca. Każda** jednostka do wyodrębnienia danych z wyrażenia długości, gdzie wyrażenia długości są dobrze sformatowane, a koniec danych można łatwo pomylić z innymi słowami wypowiedź.