---
title: Użyj profilu wykonania do oceny zapytań w interfejsie API gremlin usługi Azure Cosmos DB
description: Dowiedz się, jak rozwiązywać problemy i ulepszać zapytania gremlin przy użyciu kroku profilu wykonywania.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 5705ef4fb6aa895009d554617c968543cc3fcd63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441848"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Jak użyć kroku profilu wykonywania do oceny zapytań aparatu Gremlin

Ten artykuł zawiera omówienie sposobu użycia kroku profilu wykonywania dla baz danych grafów interfejsu API Gremlin usługi Azure Cosmos DB. Ten krok zawiera istotne informacje dotyczące rozwiązywania problemów i optymalizacji zapytań oraz jest zgodny z dowolnymi zapytaniami Gremlin, które można wykonać względem konta interfejsu API Gremlin usługi Cosmos DB.

Aby użyć tego kroku, `executionProfile()` wystarczy dołączyć wywołanie funkcji na końcu kwerendy Gremlin. **Twoje zapytanie Gremlin zostanie wykonane,** a wynik operacji zwróci obiekt odpowiedzi JSON z profilem wykonywania kwerendy.

Przykład:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

Po wywołaniu `executionProfile()` kroku odpowiedzi będzie obiekt JSON, który zawiera wykonywany krok Gremlin, całkowity czas, jaki zajęło i tablicy operatorów środowiska wykonawczego usługi Cosmos DB, które wyniku instrukcji.

> [!NOTE]
> Ta implementacja dla profilu wykonania nie jest zdefiniowana w specyfikacji Apache Tinkerpop. Jest specyficzne dla implementacji interfejsu API usługi Azure Cosmos DB Gremlin.


## <a name="response-example"></a>Przykład odpowiedzi

Poniżej znajduje się przykład z adnotacjami danych wyjściowych, które zostaną zwrócone:

> [!NOTE]
> W tym przykładzie jest adnotacjami z komentarzami, które wyjaśniają ogólną strukturę odpowiedzi. Rzeczywiste wykonanieProdusz profil nie będzie zawierać żadnych komentarzy.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. 
    // Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
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
        // This operation obtains a set of Edge objects. 
        // Depending on the query, these might be directly adjacent to a set of vertices, 
        // or separate, in the case of an E() query.
        //
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
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
        // This operation represents the serialization and preparation for a result from 
        // the preceding graph operations. The metrics include: time, percentTime of total 
        // execution time and resultCount.
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
> Krok executionProfile wykona kwerendę Gremlin. Obejmuje to `addV` `addE`lub kroki, które spowoduje utworzenie i zatwierdzi zmiany określone w kwerendzie. W rezultacie jednostki żądania generowane przez zapytanie Gremlin również zostaną obciążone.

## <a name="execution-profile-response-objects"></a>Obiekty odpowiedzi profilu wykonania

Odpowiedź funkcji executionProfile() daje hierarchię obiektów JSON o następującej strukturze:
  - **Obiekt operacji Gremlin:** Reprezentuje całą operację Gremlin, która została wykonana. Zawiera następujące właściwości.
    - `gremlin`: Jawna instrukcja Gremlin, która została wykonana.
    - `totalTime`: Czas, w milisekundach, że wykonanie kroku poniesione. 
    - `metrics`: Tablica zawierająca każdy operator środowiska wykonawczego usługi Cosmos DB, które zostały wykonane w celu spełnienia kwerendy. Ta lista jest sortowana w kolejności wykonania.
    
  - **Operatory środowiska uruchomieniowego usługi Cosmos DB:** Reprezentuje każdy ze składników całej operacji Gremlin. Ta lista jest sortowana w kolejności wykonania. Każdy obiekt zawiera następujące właściwości:
    - `name`: Nazwa operatora. Jest to typ kroku, który został oceniony i wykonany. Więcej informacji w poniższej tabeli.
    - `time`: Ilość czasu, w milisekundach, który wziął dany operator.
    - `annotations`: Zawiera dodatkowe informacje, specyficzne dla operatora, który został wykonany.
    - `annotations.percentTime`: Procent całkowitego czasu wykonania określonego operatora.
    - `counts`: Liczba obiektów, które zostały zwrócone z warstwy magazynu przez ten operator. Jest to zawarte `counts.resultCount` w wartości skalarnej wewnątrz.
    - `storeOps`: Reprezentuje operację magazynu, która może obejmować jedną lub wiele partycji.
    - `storeOps.fanoutFactor`: Reprezentuje liczbę partycji, do których dostęp ma ta określona operacja magazynowania.
    - `storeOps.count`: Reprezentuje liczbę wyników zwróconych przez tę operację magazynu.
    - `storeOps.size`: Reprezentuje rozmiar w bajtach wyniku danej operacji magazynowania.

Operator środowiska wykonawczego Cosmos DB Gremlin|Opis
---|---
`GetVertices`| Ten krok uzyskuje zestaw obiektów z warstwy trwałości. 
`GetEdges`| Ten krok uzyskuje krawędzie, które przylegają do zestawu wierzchołków. Ten krok może spowodować jedną lub wiele operacji magazynu.
`GetNeighborVertices`| Ten krok uzyskuje wierzchołki, które są połączone z zestawem krawędzi. Krawędzie zawierają klucze partycji i identyfikatory zarówno ich wierzchołków źródłowych i docelowych.
`Coalesce`| Ten krok uwzględnia oceny dwóch operacji, `coalesce()` gdy krok Gremlin jest wykonywany.
`CartesianProductOperator`| Ten krok oblicza produkt kartezjański między dwoma zestawami danych. Zwykle wykonywane za każdym `to()` razem, gdy predykaty lub `from()` są używane.
`ConstantSourceOperator`| Ten krok oblicza wyrażenie do uzyskania stałej wartości w wyniku.
`ProjectOperator`| Ten krok przygotowuje i serializuje odpowiedź przy użyciu wyniku poprzednich operacji.
`ProjectAggregation`| Ten krok przygotowuje i serializuje odpowiedź dla operacji agregacji.

> [!NOTE]
> Lista ta będzie nadal aktualizowana w miarę dodawania nowych operatorów.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Przykłady sposobu analizowania odpowiedzi profilu wykonania

Poniżej przedstawiono przykłady typowych optymalizacji, które można zauważyć przy użyciu odpowiedzi profilu wykonania:
  - Ślepe zapytanie wentylatora.
  - Niefiltrowane zapytanie.

### <a name="blind-fan-out-query-patterns"></a>Ślepe wzorce zapytań wentylatora

Załóżmy następującą odpowiedź profilu wykonania z **wykresu podzielonego na partycje:**

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

Można z niego wyciągnąć następujące wnioski:
- Kwerenda jest pojedynczym odnośnym identyfikatorem, ponieważ `g.V('id')`instrukcja Gremlin podąża za wzorcem .
- Sądząc z `time` metryki, opóźnienie tego zapytania wydaje się być wysokie, ponieważ jest [więcej niż 10ms dla pojedynczej operacji odczytu punktu.](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide)
- Jeśli spojrzymy `storeOps` na obiekt, widzimy, że `fanoutFactor` jest `5`, co oznacza, że [5 partycji](https://docs.microsoft.com/azure/cosmos-db/partition-data) były dostępne przez tę operację.

Na wniosek tej analizy możemy ustalić, że pierwsze zapytanie uzyskuje dostęp do większej liczby partycji niż jest to konieczne. Można rozwiązać ten problem, określając klucz partycjonowania w kwerendzie jako predykat. Doprowadzi to do mniejszego opóźnienia i mniejszego kosztu na kwerendę. Dowiedz się więcej o [partycjonowaniu wykresów](graph-partitioning.md). Bardziej optymalną kwerendą byłoby `g.V('tt0093640').has('partitionKey', 't1001')`.

### <a name="unfiltered-query-patterns"></a>Niefiltrowane wzorce zapytań

Porównaj następujące dwie odpowiedzi profilu wykonania. Dla uproszczenia te przykłady użyć jednego wykresu partycjonowane.

Ta pierwsza kwerenda pobiera wszystkie wierzchołki z etykietą, `tweet` a następnie uzyskuje ich sąsiednich wierzchołków:

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

Zwróć uwagę na profil tej samej kwerendy, ale teraz z dodatkowym filtrem , `has('lang', 'en')`przed eksplorowanie sąsiednich wierzchołków:

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

Te dwa zapytania osiągnęły ten sam wynik, jednak pierwszy z nich będzie wymagać więcej jednostek żądania, ponieważ potrzebne do iteracji większego początkowego zestawu danych przed zapytaniem sąsiednich elementów. Możemy zobaczyć wskaźniki tego zachowania podczas porównywania następujących parametrów z obu odpowiedzi:
- Wartość `metrics[0].time` jest wyższa w pierwszej odpowiedzi, co wskazuje, że ten pojedynczy krok trwało dłużej, aby rozwiązać.
- Wartość `metrics[0].counts.resultsCount` jest wyższa również w pierwszej odpowiedzi, co wskazuje, że początkowy roboczy zestaw danych był większy.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [obsługiwanych funkcjach Gremlin](gremlin-support.md) w usłudze Azure Cosmos DB. 
* Dowiedz się więcej o [interfejsie API Gremlin w usłudze Azure Cosmos DB](graph-introduction.md).
