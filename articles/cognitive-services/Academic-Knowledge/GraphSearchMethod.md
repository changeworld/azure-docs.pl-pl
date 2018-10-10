---
title: Metoda wyszukiwania — interfejs Academic Knowledge API Graph
titlesuffix: Azure Cognitive Services
description: Aby zwrócić zestaw obiektów akademickich na podstawie wzorców określony wykres, należy użyć metody wyszukiwanie graficzne interfejsu Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 83f29106d72f564f894c968102b703ab6bb5d8c2
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902387"
---
# <a name="graph-search-method"></a>Metoda wyszukiwania programu Graph

**Wyszukiwanie graficzne** interfejsu API REST służy do zwracania zbiór obiektów akademickich na podstawie wzorców podanym wykresem.  Odpowiedź jest zestaw ścieżek wykres spełniających ograniczenia określone przez użytkownika. Ścieżka wykres to sekwencja przeplotem wykres węzły i krawędzie w formie _v0-e0, v1, e1,..., vn_, gdzie _v0_ jest węzeł początkowy ścieżki.
<br>

**Punkt końcowy REST:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?
```   
<br>

## <a name="request-parameters"></a>Parametry żądania  
Name (Nazwa)     | Wartość | Wymagana?  | Opis
-----------|-----------|---------|--------
**Tryb**       | Ciąg tekstowy | Yes | Nazwa trybu w którym chcesz użyć. Wartość to *json* lub *lambda*.

Metoda wyszukiwania programu graph musi zostać wywołany przez żądanie HTTP POST. Żądanie post powinna zawierać nagłówek typu zawartości: **application/json**.

##### <a name="json-search"></a>Wyszukiwania JSON 

Aby uzyskać *json* wyszukiwanie, treść wpisu jest obiekt JSON. Obiekt JSON opisano wzorzec ścieżki, za pomocą ograniczeń określonych przez użytkownika (zobacz [specyfikacji obiekt JSON](JSONSearchSyntax.md) dla *json* wyszukiwania).


##### <a name="lambda-search"></a>Wyszukiwania lambda

Aby uzyskać *lambda* wyszukiwania, treść wpisu to ciąg tekstowy. Treść wpisu jest ciągu zapytania LIKQ lambda, która jest pojedynczą instrukcję języka C# (zobacz [specyfikacji ciągu zapytania](LambdaSearchSyntax.md) dla *lambda* wyszukiwania). 

<br>
## <a name="response-json"></a>Odpowiedź (JSON)
Name (Nazwa) | Opis
-------|-----   
**wyniki** | Tablica 0 lub więcej jednostek, które pasują do wyrażenia zapytania. Każda jednostka zawiera wartości żądanych atrybutów. W tym polu jest wyświetlany, jeśli żądanie zostało pomyślnie przetworzone.
**Błąd** | Kody stanu HTTP. W tym polu jest wyświetlany, jeśli żądanie zakończy się niepowodzeniem.
**Komunikat** | Komunikat o błędzie. W tym polu jest wyświetlany, jeśli żądanie zakończy się niepowodzeniem.

Jeśli zapytanie nie może zostać przetworzony w ramach _800 ms_, _limitu czasu_ zostanie zwrócony błąd. 

<br>
#### <a name="example"></a>Przykład:

##### <a name="json-search"></a>Wyszukiwania JSON
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=json
```
<br>
Aby uzyskać *json* wyszukiwania, jeśli chcemy uzyskać dokumentów o tytułach zawiera "wykresu aparatu" i zapisany przez "bin shao", można określić zapytanie w następujący sposób.

```JSON
{
  "path": "/paper/AuthorIDs/author",
  "paper": {
    "type": "Paper",
    "NormalizedTitle": "graph engine",
    "select": [
      "OriginalTitle"
    ]
  },
  "author": {
    "return": {
      "type": "Author",
      "Name": "bin shao"
    }
  }
}
```

Wyniki kwerendy jest tablica ścieżek wykresu. Ścieżka programu graph jest Tablica obiektów węzła odpowiadający węzły określonego w ścieżce zapytania. Te obiekty węzeł ma co najmniej jedną właściwość *CellID*, który reprezentuje identyfikator jednostki. Inne właściwości mogą być pobierane przez określenie nazwy właściwości, za pośrednictwem wybierz operator [ *przechodzenia przez akcję obiektu*](JSONSearchSyntax.md).

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668,
        "OriginalTitle": "Trinity: a distributed graph engine on a memory cloud"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2171539317,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2411554868,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 73304046,
        "OriginalTitle": "The Trinity graph engine"
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
 ```

##### <a name="lambda-search"></a>Wyszukiwania lambda 

```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=lambda
```
<br>
Aby uzyskać *lambda* wyszukiwania, jeśli chcemy uzyskać Autor identyfikatory dany dokument, możemy napisać zapytanie podobny do następującego.

```
MAG.StartFrom(@"{
    type  : ""Paper"",
    match : {
        NormalizedTitle : ""trinity: a distributed graph engine on a memory cloud""
    }
}").FollowEdge("AuthorIDs").VisitNode(Action.Return)
```

Dane wyjściowe *lambda* zapytania wyszukiwania jest również tablica ścieżek wykresu:

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2142490828
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2116756368
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
```
 
## <a name="graph-schema"></a>Schemat grafu

Schemat grafu jest przydatne przy pisaniu zapytań wyszukiwania programu graph. Jest on wyświetlany na poniższej ilustracji.

![Schemat grafu akademickie firmy Microsoft](./Images/AcademicGraphSchema.png)
