---
title: Typ jednostki prostej
titleSuffix: Language Understanding - Azure Cognitive Services
description: Prosta jednostka to ogólna jednostka, która opisuje pojedyncze koncepcje i jest poznania z kontekstu wykorzystanego przez maszynę. Ponieważ proste jednostki są zazwyczaj nazwami, takimi jak nazwy firmowe, nazwy produktów lub inne kategorie nazw, Dodaj listę fraz przy użyciu prostej jednostki, aby zwiększyć sygnał użytych nazw.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: 3f03b33f685bb5c7c9ba8f2267b8556c5dadade4
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480221"
---
# <a name="simple-entity"></a>Prosta jednostka 

Prosta jednostka to ogólna jednostka, która opisuje pojedyncze koncepcje i jest poznania z kontekstu wykorzystanego przez maszynę. Ponieważ proste jednostki są zazwyczaj nazwami, takimi jak nazwy firmowe, nazwy produktów lub inne kategorie nazw, Dodaj [listę fraz](luis-concept-feature.md) przy użyciu prostej jednostki, aby zwiększyć sygnał użytych nazw. 

**Jednostka jest dobrym dopasowaniem w przypadku:**

* Dane nie są ciągle sformatowane, ale wskazują na to samo. 

![proste jednostki](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Przykładowy plik JSON

`Bob Jones wants 3 meatball pho`

W poprzednim wypowiedź `Bob Jones` jest oznaczona jako prosty `Customer` jednostki.

Dane zwrócone z punktu końcowego obejmuje nazwa jednostki, odnalezione tekst z wypowiedź, lokalizacja odnalezionych tekstu i oceny:

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|Obiekt danych|Nazwa jednostki|Wartość|
|--|--|--|
|Proste jednostki|`Customer`|`bob jones`|

## <a name="next-steps"></a>Kolejne kroki

W tym [samouczku](luis-quickstart-primary-and-secondary-data.md)Wyodrębnij dane o nazwie zadania zatrudnienia z wypowiedź przy użyciu **jednostki prostej**. Aby zwiększyć dokładność wyodrębniania, Dodaj [listę fraz](luis-concept-feature.md) dla warunków specyficznych dla jednostki prostej.