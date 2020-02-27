---
title: Partycjonowanie danych w usłudze Azure Cosmos DB — interfejs API Gremlin
description: Dowiedz się, jak można użyć partycjonowane programu graph w usłudze Azure Cosmos DB. W tym artykule opisano również wymagania i najlepsze rozwiązania dotyczące wykres podzielonym na partycje.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 44d3b7c2b9e23b90f696162747d9728b18fb7d3f
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623374"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Przy użyciu wykresu podzielonym na partycje w usłudze Azure Cosmos DB

Jedną z kluczowych funkcji interfejsu API języka Gremlin w usłudze Azure Cosmos DB jest zdolność do obsługi dużych wykresów za pomocą skalowanie w poziomie. Kontenery można skalować niezależnie pod względem magazynu i przepływności. Możesz tworzyć kontenery w usłudze Azure Cosmos DB, która może być automatycznie skalowana do przechowywania danych programu graph. Dane są automatycznie równoważone w oparciu o określony **klucz partycji**.

**Partycjonowanie jest wymagane** , jeśli oczekuje się, że rozmiar kontenera przekracza 20 GB lub jeśli chcesz przydzielić więcej niż 10 000 jednostek żądań na sekundę (jednostek ru). Te same ogólne zasady dotyczące [mechanizmu partycjonowania Azure Cosmos DB](partition-data.md) stosują się z kilkoma optymalizacjami specyficznymi dla wykresu opisanymi poniżej.

![Partycjonowanie wykresu.](./media/graph-partitioning/graph-partitioning.png)

## <a name="graph-partitioning-mechanism"></a>Mechanizm partycjonowania wykresu

W poniższych wytycznych opisano sposób działania strategii partycjonowania w Azure Cosmos DB:

- **Zarówno wierzchołki, jak i krawędzie są przechowywane jako dokumenty JSON**.

- **Wierzchołki wymagają klucza partycji**. Ten klucz określi, w której partycji będą przechowywane wierzchołka za pomocą algorytmu wyznaczania wartości skrótu. Nazwa właściwości klucza partycji jest definiowana podczas tworzenia nowego kontenera i ma format: `/partitioning-key-name`.

- **Krawędzie będą przechowywane z wierzchołkiem źródła**. Innymi słowy dla każdego wierzchołka kluczu partycji określa, gdzie są przechowywane wraz z jego krawędzi wychodzących. Ta optymalizacja ma na celu uniknięcie zapytań między partycjami w przypadku używania kardynalności `out()` w zapytaniach grafu.

- **Krawędzie zawierają odwołania do wierzchołków, do których prowadzą punkty**. Wszystkie krawędzie są przechowywane z kluczami partycji i identyfikatorami wierzchołków, do których się znajdują. To obliczenie powoduje, że wszystkie kwerendy kierunku `out()` zawsze są zapytaniem z podziałem na partycje, a nie z nieślepą kwerendą między partycjami. 

- **Zapytania programu Graph muszą określać klucz partycji**. Aby można było w pełni korzystać z partycjonowania poziomego w usłudze Azure Cosmos DB, klucza partycji należy podać w przypadku wybrania jednego wierzchołka, jeśli to możliwe. Zapytania do wybierania jednego lub wielu wierzchołków w grafie podzielonym na partycje są następujące:

    - `/id` i `/label` nie są obsługiwane jako klucze partycji dla kontenera w interfejsie API Gremlin.


    - Wybierając wierzchołka według identyfikatora, **Użyj kroku `.has()`, aby określić właściwość klucza partycji**: 
    
        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Wybieranie wierzchołka przez **określenie krotki, w tym wartości klucza partycji i identyfikatora**: 
    
        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Określanie **tablicy spójnych wartości kluczy partycji i identyfikatorów**:
    
        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Wybieranie zestawu wierzchołków z ich identyfikatorami i **określanie listy wartości kluczy partycji**: 
    
        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - Użycie **strategii partycji** na początku zapytania i określenie partycji dla zakresu pozostałej części zapytania Gremlin: 
    
        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Najlepsze rozwiązania związane z używaniem wykres podzielonym na partycje

Skorzystaj z poniższych wskazówek, aby zapewnić wydajność i skalowalność podczas wykresach podzielonym na partycje za pomocą nieograniczone kontenery:

- **Zawsze określaj wartość klucza partycji podczas wykonywania zapytania w wierzchołku**. Pobieranie wierzchołka ze znanych partycji jest to sposób osiągnięcia wydajności. Wszystkie kolejne operacje sąsiedztwa zawsze zostaną ograniczone do zakresu partycji, ponieważ krawędzie zawierają identyfikator odwołania i klucz partycji do ich wierzchołków docelowych.

- **Użyj kierunku wychodzącego podczas wykonywania zapytania o krawędzi wszędzie tam, gdzie jest to możliwe**. Jak wspomniano powyżej, krawędzie są przechowywane z ich źródła wierzchołki, w kierunku wychodzącego. Dlatego szanse konieczności uciekania się do zapytań między partycjami są zminimalizowane, gdy dane i zapytania, które są skonstruowane z tego wzorca należy pamiętać. W przeciwieństwie do tego zapytanie `in()` będzie zawsze kosztowną kwerendą wentylatorów.

- **Wybierz klucz partycji, który będzie równomiernie dystrybuowany dane między partycjami**. Ta decyzja silnie zależy od modelu danych rozwiązania. Przeczytaj więcej na temat tworzenia odpowiedniego klucza partycji na potrzeby [partycjonowania i skalowania w Azure Cosmos DB](partition-data.md).

- **Optymalizuj zapytania, aby uzyskać dane w granicach partycji**. Optymalnej strategii partycjonowania byłaby wyrównana do wzorców zapytań. Zapytania, które pobierają dane z jednej partycji zapewnia maksymalną wydajność.

## <a name="next-steps"></a>Następne kroki

Następnie możesz przejść do przeczytaj następujące artykuły:

* Dowiedz się więcej [na temat partycji i skalowania w Azure Cosmos DB](partition-data.md).
* Dowiedz się więcej o [obsłudze Gremlin w interfejsie API Gremlin](gremlin-support.md).
* Dowiedz się więcej o [wprowadzeniu do interfejsu API Gremlin](graph-introduction.md).
