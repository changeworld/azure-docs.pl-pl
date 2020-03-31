---
title: Partycjonowanie danych w interfejsie API gremlin usługi Azure Cosmos DB
description: Dowiedz się, jak można użyć wykresu podzielonego na partycje w usłudze Azure Cosmos DB. W tym artykule opisano również wymagania i najlepsze rozwiązania dotyczące wykresu podzielonego na partycje.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 44d3b7c2b9e23b90f696162747d9728b18fb7d3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623374"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Używanie grafu podzielonego na partycje w usłudze Azure Cosmos DB

Jedną z kluczowych funkcji interfejsu API Gremlin w usłudze Azure Cosmos DB jest możliwość obsługi wykresów na dużą skalę za pomocą skalowania poziomego. Kontenery można skalować niezależnie pod względem przechowywania i przepływności. Można utworzyć kontenery w usłudze Azure Cosmos DB, które mogą być automatycznie skalowane do przechowywania danych wykresu. Dane są automatycznie równoważone na podstawie określonego **klucza partycji**.

**Partycjonowanie jest wymagane,** jeśli kontener ma przechowywać więcej niż 20 GB w rozmiarze lub jeśli chcesz przydzielić więcej niż 10 000 jednostek żądań na sekundę (RUs). Te same ogólne zasady z [mechanizmu partycjonowania usługi Azure Cosmos DB](partition-data.md) mają zastosowanie z kilkoma optymalizacjami specyficznymi dla wykresu opisanymi poniżej.

![Partycjonowanie wykresu.](./media/graph-partitioning/graph-partitioning.png)

## <a name="graph-partitioning-mechanism"></a>Mechanizm partycjonowania wykresu

Poniższe wskazówki opisują sposób działania strategii partycjonowania w usłudze Azure Cosmos DB:

- **Zarówno wierzchołki, jak i krawędzie są przechowywane jako dokumenty JSON**.

- **Wierzchołki wymagają klucza partycji**. Ten klucz określi, w której partycji wierzchołek będzie przechowywany za pomocą algorytmu mieszania. Nazwa właściwości klucza partycji jest definiowana podczas tworzenia `/partitioning-key-name`nowego kontenera i ma format: .

- **Krawędzie będą przechowywane z ich źródłowym wierzchołkiem**. Innymi słowy dla każdego wierzchołka jego klucz partycji definiuje, gdzie są one przechowywane wraz z jego krawędzi wychodzących. Ta optymalizacja jest wykonywana w celu uniknięcia zapytań między partycjami podczas korzystania z `out()` kardynalności w kwerendach wykresu.

- **Krawędzie zawierają odwołania do wierzchołków, na które wskazują**. Wszystkie krawędzie są przechowywane z kluczami partycji i identyfikatorami wierzchołków, na które wskazują. To obliczenie `out()` sprawia, że wszystkie zapytania kierunkowe zawsze są kwerendą podzieloną na partycje o określonym zakresie, a nie ślepą kwerendą międzyplatformową. 

- **Zapytania wykresu muszą określić klucz partycji**. Aby w pełni korzystać z partycjonowania poziomego w usłudze Azure Cosmos DB, klucz partycji należy określić, gdy wybrano pojedynczy wierzchołek, gdy jest to możliwe. Poniżej przedstawiono kwerendy dotyczące wybierania jednego lub wielu wierzchołków na wykresie podzielonym na partycje:

    - `/id`i `/label` nie są obsługiwane jako klucze partycji dla kontenera w interfejsie API Gremlin.


    - Wybieranie wierzchołka według **identyfikatora, `.has()` **a następnie użycie kroku do określenia właściwości klucza partycji: 
    
        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Wybieranie wierzchołka przez **określenie krotki z wartością klucza partycji i identyfikatorem:** 
    
        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Określanie **tablicy krotek wartości klucza partycji i identyfikatorów:**
    
        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Wybieranie zestawu wierzchołków z ich identyfikatorami i **określanie listy wartości klucza partycji:** 
    
        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - Korzystanie ze **strategii partycji** na początku kwerendy i określanie partycji dla zakresu pozostałej części zapytania Gremlin: 
    
        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Najważniejsze wskazówki dotyczące korzystania z wykresu podzielonego na partycje

Użyj następujących wskazówek, aby zapewnić wydajność i skalowalność podczas korzystania z wykresów podzielonych na partycje z nieograniczoną ilością kontenerów:

- **Zawsze określ wartość klucza partycji podczas wykonywania kwerendy nad wierzchołkiem**. Uzyskanie wierzchołka ze znanej partycji jest sposobem na osiągnięcie wydajności. Wszystkie kolejne operacje sąsiadujących zawsze będą ograniczone do partycji, ponieważ Edges zawierają identyfikator odwołania i klucz partycji do ich wierzchołków docelowych.

- **Użyj kierunku wychodzącego podczas wykonywania zapytań o krawędzie, gdy jest to możliwe**. Jak wspomniano powyżej, krawędzie są przechowywane z ich wierzchołków źródłowych w kierunku wychodzącym. Tak więc szanse na odwołanie się do zapytań między partycjami są zminimalizowane, gdy dane i zapytania są zaprojektowane z myślą o tym wzorcu. Wręcz przeciwnie, zapytanie `in()` zawsze będzie kosztowne zapytanie fan-out.

- **Wybierz klucz partycji, który będzie równomiernie dystrybuować dane między partycjami**. Decyzja ta w dużym stopniu zależy od modelu danych rozwiązania. Dowiedz się więcej o tworzeniu odpowiedniego klucza partycji w [partycjonowaniu i skalowaniu w usłudze Azure Cosmos DB](partition-data.md).

- **Optymalizuj zapytania w celu uzyskania danych w granicach partycji**. Optymalna strategia partycjonowania zostanie dostosowana do wzorców zapytań. Zapytania, które uzyskują dane z jednej partycji zapewniają najlepszą możliwą wydajność.

## <a name="next-steps"></a>Następne kroki

Następnie możesz przystąpić do czytania następujących artykułów:

* Dowiedz się więcej o [partycji i skalowaniu w usłudze Azure Cosmos DB](partition-data.md).
* Dowiedz się więcej o [obsłudze gremlin w gremlin API](gremlin-support.md).
* Dowiedz się więcej o [wprowadzeniu do interfejsu API Gremlin](graph-introduction.md).
