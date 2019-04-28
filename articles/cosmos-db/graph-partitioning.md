---
title: Partycjonowanie danych w usłudze Azure Cosmos DB — interfejs API Gremlin
description: Dowiedz się, jak można użyć partycjonowane programu graph w usłudze Azure Cosmos DB. W tym artykule opisano również wymagania i najlepsze rozwiązania dotyczące wykres podzielonym na partycje.
author: rockboyfor
ms.author: v-yeche
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
origin.date: 12/06/2018
ms.date: 03/18/2019
ms.custom: seodec18
ms.openlocfilehash: f1e486a302b440d819e15ef86f8d76ea5e50d201
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888418"
---
<!--Verify sucessfully-->
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Przy użyciu wykresu podzielonym na partycje w usłudze Azure Cosmos DB

Jedną z kluczowych funkcji interfejsu API języka Gremlin w usłudze Azure Cosmos DB jest zdolność do obsługi dużych wykresów za pomocą skalowanie w poziomie. Skalowanie w poziomie odbywa się za pośrednictwem [partycjonowanie możliwości w usłudze Azure Cosmos DB](partition-data.md). Kontenery można skalować niezależnie pod względem magazynu i przepływności. Możesz tworzyć kontenery w usłudze Azure Cosmos DB, która może być automatycznie skalowana do przechowywania danych programu graph. Danych jest automatycznie rozmieszczana w oparciu o określonym **klucza partycji**.

W tym dokumencie są opisane szczegółowych informacji o jak baz danych programu graph są podzielone na partycje, wraz z jego wpływ na zarówno wierzchołków (lub węzły) i krawędzie.

## <a name="requirements-for-partitioned-graph"></a>Wymagania dotyczące partycjonowane wykresu

Poniżej przedstawiono szczegółowe informacje, które należy zrozumieć podczas tworzenia kontenera grafu podzielonym na partycje:

- **Partycjonowanie jest wymagana** Jeśli kontener może przechowywać więcej niż 10 GB, rozmiar lub jeśli chcesz przydzielić więcej niż 10 000 jednostek żądań na sekundę (ru).

- **Wierzchołki i krawędzie, które są przechowywane jako dokumenty JSON**.

- **Wierzchołki wymagają klucza partycji**. Ten klucz określi, w której partycji będą przechowywane wierzchołka za pomocą algorytmu wyznaczania wartości skrótu. Nazwa tego klucza partycji jest jednowyrazowej ciągu bez spacji ani znaków specjalnych. Klucz partycji jest definiowany podczas tworzenia nowego kontenera i ma format: `/partitioning-key-name`.

- **Krawędzie będą przechowywane przy użyciu ich wierzchołka źródłowego**. Innymi słowy dla każdego wierzchołka kluczu partycji określa, gdzie są przechowywane wraz z jego krawędzi wychodzących. Ten sposób można uniknąć zapytań między partycjami, używając `out()` Kardynalność zapytania programu graph.

- **Zapytania programu Graph, muszą określać klucz partycji**. Aby można było w pełni korzystać z partycjonowania poziomego w usłudze Azure Cosmos DB, klucza partycji należy podać w przypadku wybrania jednego wierzchołka, jeśli to możliwe. Zapytania do wybierania jednego lub wielu wierzchołków w grafie podzielonym na partycje są następujące:

    - `/id` i `/label` nie są obsługiwane jako klucze partycji dla kontenera w interfejsie API języka Gremlin.

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

Skorzystaj z poniższych wskazówek, aby zapewnić wydajność i skalowalność podczas wykresach podzielonym na partycje za pomocą nieograniczone kontenery:

- **Zawsze określać wartości klucza partycji podczas wykonywania zapytań dotyczących wierzchołek**. Pobieranie wierzchołka ze znanych partycji jest to sposób osiągnięcia wydajności.

- **Podczas wykonywania zapytań dotyczących krawędzi, jeśli to możliwe, użyj kierunku wychodzącego**. Jak wspomniano powyżej, krawędzie są przechowywane z ich źródła wierzchołki, w kierunku wychodzącego. Dlatego szanse konieczności uciekania się do zapytań między partycjami są zminimalizowane, gdy dane i zapytania, które są skonstruowane z tego wzorca należy pamiętać.

- **Wybierz klucz partycji służący do równomiernego dystrybuowania danych między partycjami**. Ta decyzja silnie zależy od modelu danych rozwiązania. Przeczytaj więcej na temat tworzenia klucza odpowiednich partycji w [partycjonowanie i skalowanie w usłudze Azure Cosmos DB](partition-data.md).

- **Optymalizowanie zapytań, aby uzyskać dane w granicach partycji**. Optymalnej strategii partycjonowania byłaby wyrównana do wzorców zapytań. Zapytania, które pobierają dane z jednej partycji zapewnia maksymalną wydajność.

## <a name="next-steps"></a>Kolejne kroki

Następnie możesz przejść do przeczytaj następujące artykuły:

* Dowiedz się więcej o [partycji i skali w usłudze Azure Cosmos DB](partition-data.md).
* Dowiedz się więcej o [Obsługa języka Gremlin w interfejsie API języka Gremlin](gremlin-support.md).
* Dowiedz się więcej o [wprowadzenie do interfejsu API języka Gremlin](graph-introduction.md).

<!--Update_Description: new articles on  -->
<!--ms.date: 03/18/2019-->