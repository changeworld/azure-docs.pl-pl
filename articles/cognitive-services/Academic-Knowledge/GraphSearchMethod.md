---
title: Metoda wyszukiwania w Academic Knowledge API programu Graph | Dokumentacja firmy Microsoft
description: Użyj metody Search wykresu w Academic Knowledge API, aby zwrócić zestaw jednostek academic na podstawie wzorców wykres określonych w kognitywnych usług firmy Microsoft.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: d811db117c934c0d41fbfea1220d241cc022e4a8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346877"
---
# <a name="graph-search-method"></a>Wykres metody wyszukiwania

**Wyszukiwania wykres** interfejsu API REST służy do zwracania zestawu jednostek academic na podstawie wzorców podanym wykresem.  Odpowiedź jest zestawu ścieżek wykres spełniających ograniczenia określone przez użytkownika. Ścieżka wykresu jest przeplotem sekwencji węzłów grafu i krawędzi w formie _v0, e0, v1, e1,..., vn_, gdzie _v0_ jest węzeł początkowy ścieżki.
<br>

**Punkt końcowy REST:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?
```   
<br>

## <a name="request-parameters"></a>Parametry żądania  
Name (Nazwa)     | Wartość | Wymagana?  | Opis
-----------|-----------|---------|--------
**Tryb**       | Ciąg tekstowy | Yes | Nazwa tryb, w którym ma zostać użyty. Wartość to *json* lub *lambda*.

Metoda wyszukiwania graph musi zostać wywołana za pomocą żądania HTTP POST. Żądanie post powinna zawierać nagłówek typu zawartości: **application/json**.

##### <a name="json-search"></a>Wyszukiwanie JSON 

Aby uzyskać *json* wyszukiwania, treść wpisu jest obiektem JSON. Obiekt JSON opisuje wzorzec ścieżki z ograniczeniami określone przez użytkownika (zobacz [specyfikacji obiekt JSON](JSONSearchSyntax.md) dla *json* wyszukiwania).


##### <a name="lambda-search"></a>Lambda wyszukiwania

Aby uzyskać *lambda* wyszukiwania, treść wpisu to ciąg tekstowy. Treść wpisu jest ciągu zapytania LIKQ lambda, czyli jednej instrukcji C# (zobacz [specyfikacji ciągu zapytania](LambdaSearchSyntax.md) dla *lambda* wyszukiwania). 

<br>
## <a name="response-json"></a>Odpowiedź (JSON)
Name (Nazwa) | Opis
-------|-----   
**Wyniki** | Tablica 0 lub więcej jednostek, które odpowiada wyrażeniu zapytania. Każdy obiekt zawiera wartości wymaganych atrybutów. W tym polu jest wyświetlany, jeśli pomyślnie przetworzyć żądania.
**Błąd** | Kody stanu HTTP. W tym polu jest wyświetlany, jeśli żądanie kończy się niepowodzeniem.
**Komunikat** | Komunikat o błędzie. W tym polu jest wyświetlany, jeśli żądanie kończy się niepowodzeniem.

Jeśli zapytanie nie można przetworzyć w ramach _800 ms_, _limitu czasu_ zostanie zwrócony błąd. 

<br>
#### <a name="example"></a>Przykład:

##### <a name="json-search"></a>Wyszukiwanie JSON
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=json
```
<br>
Aby uzyskać *json* wyszukiwania, jeśli chcemy uzyskać dokumenty zawierają o tytułach "graph aparat" i napisane przez "bin shao", można określić zapytania w następujący sposób.

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

Wyniki kwerendy jest tablicą ścieżek wykresu. Ścieżka wykresu jest Tablica obiektów węzła odpowiadający węzłów określona w ścieżce zapytania. Te obiekty w węźle ma co najmniej jedną właściwość *CellID*, który reprezentuje identyfikator jednostki. Inne właściwości mogą być pobierane przez określenie nazw właściwości, za pomocą operatora select z [ *obiekt akcji przechodzenie*](JSONSearchSyntax.md).

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

##### <a name="lambda-search"></a>Lambda wyszukiwania 

```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=lambda
```
<br>
Aby uzyskać *lambda* wyszukiwania, jeśli chcemy, aby pobrać autora identyfikatorów danego dokumentu, możemy napisać zapytanie podobny do następującego.

```
MAG.StartFrom(@"{
    type  : ""Paper"",
    match : {
        NormalizedTitle : ""trinity: a distributed graph engine on a memory cloud""
    }
}").FollowEdge("AuthorIDs").VisitNode(Action.Return)
```

Dane wyjściowe *lambda* zapytania wyszukiwania jest również tablicę wykres ścieżki:

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

Schemat grafu jest przydatna do zapisywania wykresu zapytania wyszukiwania. Jest on wyświetlany na poniższej ilustracji.

![Schemat grafu edukacyjne firmy Microsoft](./Images/AcademicGraphSchema.png)
