---
title: Partycjonowanie w usłudze Azure Cosmos DB Gremlin API | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można użyć partycjonowane programu Graph w usłudze Azure Cosmos DB.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: conceptual
ms.date: 02/28/2018
ms.author: lbosq
ms.openlocfilehash: a513d186f895c33ef9329ce0464c70de5884330d
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2018
ms.locfileid: "48891061"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Przy użyciu wykresu podzielonym na partycje w usłudze Azure Cosmos DB

Jedną z kluczowych funkcji interfejsu API języka Gremlin w usłudze Azure Cosmos DB jest zdolność do obsługi dużych wykresów za pomocą poziomy skalowalności. Ten proces odbywa się za pośrednictwem [partycjonowanie możliwości w usłudze Azure Cosmos DB](partition-data.md#how-does-partitioning-work), którego należy używać kontenerów, które można skalować niezależnie pod względem magazynu i przepływności. Usługa Azure Cosmos DB obsługuje następujące typy kontenerów wszystkich interfejsów API:

- **Kontener stały**: te kontenery można przechowywać wykres bazy danych do 10 GB w rozmiarze maksymalnie 10 000 jednostek żądań na sekundę, przydzielone do niego. Aby utworzyć kontener stały nie jest konieczne określenie właściwości klucza partycji danych.

- **Nieograniczonego kontenera**: tych kontenerów może automatycznie skalować do przechowywania wykresu powyżej limitu 10 GB za pośrednictwem partycjonowanie poziome. Każda partycja będzie przechowywać 10 GB i dane zostaną automatycznie rozmieszczane na podstawie **klucza partycji określonym**, który będzie wymagany parametr, korzystając z nieograniczonego kontenera. Tego typu kontener może przechowywać rozmiar danych praktycznie nieograniczonych możliwości korzystania i umożliwia maksymalnie 100 000 jednostek żądań na sekundę lub więcej [, kontaktując się z pomocą techniczną](https://aka.ms/cosmosdbfeedback?subject=Cosmos%20DB%20More%20Throughput%20Request).

W tym dokumencie są opisane szczegółowych informacji o jak baz danych programu graph są podzielone na partycje, wraz z jego wpływ na zarówno wierzchołków (lub węzły) i krawędzie.

## <a name="requirements-for-partitioned-graph"></a>Wymagania dotyczące partycjonowane wykresu

Poniżej przedstawiono szczegółowe informacje, które należy zrozumieć podczas tworzenia kontenera grafu podzielonym na partycje:

- **Konfigurowanie partycjonowanie konieczne będzie** Jeśli powinien być większy niż 10 GB, rozmiar kontenera i/lub jeśli przydzielanie ponad 10 000 jednostek żądań na sekundę (RU/s) ma być wymagane.

- **Wierzchołki i krawędzie, które są przechowywane jako dokumenty JSON** w zapleczu kontenera usługi Azure Cosmos DB — interfejs API Gremlin.

- **Wierzchołki wymagają klucza partycji**. Ten klucz określi, w której partycji będą przechowywane wierzchołka za pomocą algorytmu wyznaczania wartości skrótu. Nazwa tego klucza partycji jest jednowyrazowej ciągu bez spacji ani znaków specjalnych i jest zdefiniowany, tworząc nowy kontener przy użyciu formatu `/partitioning-key-name` w portalu.

- **Krawędzie będą przechowywane przy użyciu ich wierzchołka źródłowego**. Innymi słowy dla każdego wierzchołka kluczu partycji określi, gdzie będą przechowywane wraz z jego krawędzi wychodzących. Ten sposób można uniknąć zapytań między partycjami, używając `out()` Kardynalność zapytania programu graph.

- **Zapytania programu Graph, muszą określać klucz partycji**. Aby można było w pełni korzystać z partycjonowania poziomego w usłudze Azure Cosmos DB, klucza partycji należy podać w przypadku wybrania jednego wierzchołka, jeśli to możliwe. Zapytania do wybierania jednego lub wielu wierzchołków w grafie podzielonym na partycje są następujące:

    - Obecnie nie można użyć `/id` jako klucza partycji dla kontenera w interfejsie API języka Gremlin.


    - Wybraniu wierzchołka według identyfikatorów, a następnie **przy użyciu `.has()` krok, aby określić właściwości klucza partycji**: 
    
        ```
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Wybieranie wierzchołka przez **Określanie krotki wartości klucza partycji i Identyfikatora**: 
    
        ```
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Określanie **tablicy krotek identyfikatorów i wartości klucza partycji**:
    
        ```
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Wybierając zestaw wierzchołków i **określania listy wartości klucza partycji**: 
    
        ```
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Najlepsze rozwiązania związane z używaniem wykres podzielonym na partycje

Poniżej znajdują się wytyczne, które powinien być używany do zapewnienia najbardziej efektywny sposób wydajność i skalowalność w przypadku wykresach podzielonym na partycje w nieograniczone kontenery:

- **Zawsze określać wartości klucza partycji podczas wykonywania zapytań dotyczących wierzchołek**. Uzyskiwanie wierzchołek z znanych partycji jest najbardziej wydajny sposób pod względem wydajności.

- **Podczas wykonywania zapytań dotyczących krawędzi, jeśli to możliwe, użyj kierunku wychodzącego**. Jak wspomniano powyżej, krawędzie są przechowywane z ich źródła wierzchołki, w kierunku wychodzącego. Oznacza to, że prawdopodobieństwo konieczności uciekania się do zapytań między partycjami są zminimalizowane, gdy danych i zapytania, które są skonstruowane z tego wzorca należy pamiętać.

- **Wybierz klucz partycji służący do równomiernego dystrybuowania danych między partycjami**. Ta decyzja silnie zależy od modelu danych rozwiązania. Przeczytaj więcej na temat tworzenia klucza odpowiednich partycji w [partycjonowanie i skalowanie w usłudze Azure Cosmos DB](partition-data.md).

- **Optymalizowanie zapytań, aby uzyskać dane w granicach partycji, jeśli jest to możliwe**. Optymalnej strategii partycjonowania byłaby wyrównana do wzorców zapytań. Zapytania, które pobierają dane z jednej partycji zapewnia maksymalną wydajność.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule podano omówienie pojęć i najlepsze rozwiązania dotyczące partycjonowania przy użyciu interfejsu API języka Gremlin usługi Azure Cosmos DB. 

* Dowiedz się więcej o [partycji i skali w usłudze Azure Cosmos DB](partition-data.md).
* Dowiedz się więcej o [Obsługa języka Gremlin w interfejsie API języka Gremlin](gremlin-support.md).
* Dowiedz się więcej o [wprowadzenie do interfejsu API języka Gremlin](graph-introduction.md).