---
title: Ocena zapytań za pomocą funkcji profil wykonania dla interfejsu API usługi Azure Cosmos DB języka Gremlin
description: Dowiedz się, jak rozwiązywanie problemów i poprawić za pomocą kroku profilu wykonywania zapytań języka Gremlin.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 2f3967c64e79b2bc7b01b35eff26f5ac0d4e3db4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288609"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Jak ocenić zapytań języka Gremlin za pomocą kroku profil wykonania

Ten artykuł zawiera omówienie sposobu używania wykonanie kroku profilu dla baz danych programu graph usługi Azure Cosmos DB — interfejs API Gremlin. Ten krok obejmuje odpowiednie informacje dotyczące rozwiązywania problemów i optymalizacji zapytań który jest zgodny z dowolnego zapytania języka Gremlin, które mogą być wykonywane względem konta interfejsu API języka Gremlin usługi Cosmos DB.

Aby użyć tego kroku, po prostu Dołącz `executionProfile()` wywołania funkcji na końcu zapytania języka Gremlin. **Będzie można wykonać zapytanie w języku Gremlin** , i zwraca wynik operacji obiekt odpowiedź JSON z profil wykonania zapytania.

Na przykład:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

Po wywołaniu `executionProfile()` krok, odpowiedź będzie miała obiekt JSON, który obejmuje wykonywany krok Gremlin, całkowity czas, jaki zajęło i tablicę operatorów środowiska uruchomieniowego Cosmos DB, które spowodowało instrukcji.

> [!NOTE]
> Ta implementacja profilu wykonywania nie jest zdefiniowana w specyfikacji Apache Tinkerpop. Odnosi się do usługi Azure Cosmos DB Gremlin implementacji interfejsu API.


## <a name="response-example"></a>Przykład odpowiedzi

Oto przykład danych wyjściowych, który zostanie zwrócony z adnotacjami:

> [!NOTE]
> W tym przykładzie jest oznaczony za pomocą komentarze objaśniające, ogólną strukturę odpowiedzi. Odpowiedź rzeczywiste executionProfile nie będzie zawierał żadnych komentarzy.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. Depending on the query, these might be directly adjacent to a set of vertices, or separate, in the case of an E() query.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from the preceding graph operations.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> Krok executionProfile wykona zapytanie w języku Gremlin. Obejmuje to `addV` lub `addE`kroków, które spowoduje utworzenie i spowoduje to zatwierdzenie zmian określona w zapytaniu. W rezultacie także będzie wiązać jednostek żądania generowane przez zapytanie w języku Gremlin.

## <a name="execution-profile-response-objects"></a>Obiekty odpowiedzi profilu wykonania

Odpowiedź funkcji executionProfile() umożliwia uzyskanie hierarchii obiektów JSON o następującej strukturze:
  - **Obiekt operacji języka gremlin**: Reprezentuje całej operacji języka Gremlin, który został wykonany. Zawiera następujące właściwości.
    - `gremlin`: Jawna instrukcja języka Gremlin została wykonana.
    - `totalTime`: Czas w milisekundach, który wykonanie kroku naliczany w. 
    - `metrics`: Tablica, która zawiera listę operatorów środowiska uruchomieniowego Cosmos DB, które były wykonywane w celu wykonania zapytania. Ta lista jest posortowana w kolejności wykonywania.
    
  - **Operatory środowiska uruchomieniowego cosmos DB**: Reprezentuje poszczególne składniki całej operacji języka Gremlin. Ta lista jest posortowana w kolejności wykonywania. Każdy obiekt zawiera następujące właściwości:
    - `name`: Nazwa operatora. Jest to typ kroku, które zostały ocenione, a następnie wykonać. Dowiedz się więcej w poniższej tabeli.
    - `time`: Ilość czasu w milisekundach, które miały danego operatora.
    - `annotations`: Zawiera dodatkowe informacje specyficzne dla operatora, który został wykonany.
    - `annotations.percentTime`: Wartość procentowa łącznego czasu, który zajęło wykonanie określonego operatora.
    - `counts`: Liczba obiektów zwróconych z warstwy magazynu przez tego operatora. To jest zawarty w `counts.resultCount` wartości skalarnej w ciągu.
    - `storeOps`: Reprezentuje operację magazynu, która może obejmować jeden lub wiele partycji.
    - `storeOps.fanoutFactor`: Reprezentuje liczbę partycji, które są dostępne w tej operacji określonego magazynu.
    - `storeOps.count`: Reprezentuje liczbę wyników, które ta operacja magazynu zwróciła.
    - `storeOps.size`: Reprezentuje rozmiar w bajtach wynik operacji magazynowania.

Operator środowiska uruchomieniowego języka Gremlin usługi cosmos DB|Opis
---|---
`GetVertices`| W tym kroku uzyskuje predicated zestaw obiektów z warstwy trwałości. 
`GetEdges`| W tym kroku uzyskuje krawędzie, które sąsiadują ze zbiorem wierzchołków. Ten krok może spowodować w jednej lub wielu operacji magazynu.
`GetNeighborVertices`| W tym kroku uzyskuje wierzchołki, które są połączone z zestawem krawędzi. Krawędzie zawiera partycję kluczy oraz identyfikatory wierzchołków ich źródłowym i docelowym.
`Coalesce`| W tym kroku kont oceny dwie operacje zawsze wtedy, gdy `coalesce()` kroku języka Gremlin jest wykonywana.
`CartesianProductOperator`| W tym kroku oblicza formułuje iloczyn między dwoma zestawami danych. Zazwyczaj wykonywane zawsze, gdy predykaty `to()` lub `from()` są używane.
`ConstantSourceOperator`| W tym kroku oblicza wyrażenie do produkcji w wyniku wartości stałej.
`ProjectOperator`| Ten krok przygotowuje i serializuje odpowiedź przy użyciu wynik poprzedniego działania.
`ProjectAggregation`| Ten krok przygotowuje i serializuje odpowiedzi dla operacji agregacji.

> [!NOTE]
> Ta lista będzie można zaktualizować w miarę dodawania nowych operatorów.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Przykłady sposobu analizowania odpowiedzi profilu wykonania

Oto przykłady typowych optymalizacje, które wykrył przy użyciu odpowiedzi profil wykonania:
  - Zapytania wielokierunkowego ukryta.
  - Nieprzefiltrowanymi zapytanie.

### <a name="blind-fan-out-query-patterns"></a>Ukryta wielokierunkowego wzorce zapytań

Załóżmy następującą odpowiedź profil wykonania z **partycjonowane wykres**:

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

Następujących wniosków z niego jest możliwe:
- Zapytanie jest jednego Identyfikatora wyszukiwania, ponieważ instrukcja języka Gremlin jest zgodny ze wzorcem `g.V('id')`.
- Ocenie z `time` metryki, opóźnienie to zapytanie wydaje się być duże, ponieważ jest ono [więcej niż 10 MS w ramach pojedynczej operacji odczytu punktu](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide).
- Jeśli spojrzymy na `storeOps` obiektu widać, że `fanoutFactor` jest `5`, co oznacza, że [5 partycje](https://docs.microsoft.com/azure/cosmos-db/partition-data) uzyskiwał dostęp do tej operacji.

Jako zawarcie tej analizy możemy określić, że pierwsze zapytanie uzyskuje dostęp do partycji więcej niż jest to konieczne. Można to zmienić, określając klucz partycjonowania w zapytaniu jako predykat. Doprowadzi to mniejsze opóźnienia i mniej koszt na zapytanie. Dowiedz się więcej o [partycjonowanie grafu](graph-partitioning.md). Optymalne więcej zapytań będzie `g.V('tt0093640').has('partitionKey', 't1001')`.

### <a name="unfiltered-query-patterns"></a>Wzorce niefiltrowane zapytań

Porównaj następujące odpowiedzi wykonywania dwóch w profilu. Dla uproszczenia tych przykładach używany jest pojedynczy wykres podzielonym na partycje.

To pierwsze zapytanie pobiera wszystkie wierzchołki z etykietą `tweet` i następnie uzyskuje ich sąsiednich wierzchołków:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

Zwróć uwagę, profilu tego samego zapytania, ale teraz dodatkowy filtr `has('lang', 'en')`, przed rozpoczęciem pracy z sąsiadujących wierzchołków:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

Te dwa zapytania osiągnięto ten sam wynik, pierwszy z nich będą jednak wymagały większej liczby jednostek żądania, ponieważ potrzebny do iteracji większych początkowego zestawu danych, przed wykonaniem kwerendy sąsiadujących elementów. Widoczne wskaźniki to zachowanie podczas porównywania następujące parametry z obu odpowiedzi:
- `metrics[0].time` Wartość jest większa w pierwszej odpowiedzi, który wskazuje, czy ten pojedynczy krok trwała dłużej, aby rozwiązać.
- `metrics[0].counts.resultsCount` Wartość jest większa, jak również w pierwszej odpowiedzi, co oznacza, że początkowy zestaw roboczy danych był większy.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [obsługiwane funkcje języka Gremlin](gremlin-support.md) w usłudze Azure Cosmos DB. 
* Dowiedz się więcej o [interfejs API Gremlin w usłudze Azure Cosmos DB](graph-introduction.md).
