---
title: Partycjonowanie i skalowanie w poziomie w usłudze Azure Cosmos DB
description: Więcej informacji na temat działa jak partycjonowania w usługi Azure Cosmos DB, jak skonfigurować partycje i klucze partycji oraz sposobu wybierania klucza partycji odpowiednie dla twojej aplikacji.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.openlocfilehash: cf454d6504f0433d7ac7ca883982ae317b14f814
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797827"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partycjonowanie i skalowanie w poziomie w usłudze Azure Cosmos DB

W tym artykule opisano fizycznie i logicznie partycji w usłudze Azure Cosmos DB. Omawia także najlepsze rozwiązania dotyczące skalowania i partycjonowania. 

## <a name="logical-partitions"></a>Partycje logiczne

Partycja logiczna zawiera zestaw elementów, które mają ten sam klucz partycji. Na przykład w kontenerze, gdzie zawierać wszystkie elementy `City` właściwości, można użyć `City` jako klucza partycji dla kontenera. Grupy elementów, które mają określone wartości dla `City`, takich jak `London`, `Paris`, i `NYC`, tworzą odrębne partycje logiczne. Nie trzeba martwić się o usunięcie partycji, po usunięciu danych bazowych.

W usłudze Azure Cosmos DB kontener jest podstawową jednostką skalowalności. Dane, które jest dodawany do kontenera i przepływność, którą można aprowizować w kontenerze są automatycznie (w poziomie) podzielonej na partycje w określonym zestawie partycji logicznej. Danych i przepływności są partycjonowane na podstawie określonego klucza partycji dla kontenera usługi Azure Cosmos. Aby uzyskać więcej informacji, zobacz [utworzyć kontener usługi Azure Cosmos](how-to-create-container.md).

Partycja logiczna definiuje również zakres transakcji bazy danych. Można zaktualizować elementy w ramach partycji logicznej przy użyciu [transakcji z użyciem izolacji migawki](database-transactions-optimistic-concurrency.md). Kiedy nowe elementy są dodawane do kontenera, nowych partycji logicznej przezroczyste są tworzone przez system.

## <a name="physical-partitions"></a>Partycje fizyczne

Kontener usługi Azure Cosmos skalowania przez dystrybucję dużej liczby partycji logicznej danych i przepływności. Wewnętrznie, co najmniej jedną partycję logiczne są mapowane na fizyczną partycję, która składa się z zestawu replik, nazywana także [ *zestawu replik*](global-dist-under-the-hood.md). Każdego elementu zestawu replik hosty wystąpienie aparatu bazy danych Azure Cosmos DB. Zestawu replik sprawia, że dane są przechowywane w partycję fizyczną trwałych, wysoce dostępny i spójne. Fizyczną partycję obsługuje maksymalną liczbę jednostek magazynu i żądań (ru). Każdej repliki, który tworzy partycję fizyczną dziedziczy przydział magazynowania partycji. Wszystkie repliki partycji fizycznej obsługuje zbiorczo przepływność, którą jest przydzielany do fizyczną partycję. 

Na poniższej ilustracji przedstawiono, jak logicznej partycji są mapowane na partycje fizyczne, które są globalnie rozproszone:

![Obraz, który pokazuje partycjonowanie w usłudze Azure Cosmos DB](./media/partition-data/logical-partitions.png)

Przepływnością aprowizowaną dla kontenera jest równomiernie podzielone między partycje fizyczne. Projekt klucza partycji, który nie równomierne rozłożenie żądania przepływności może tworzyć partycje "gorącymi". Gorąca partycji może spowodować ograniczanie szybkości i nieefektywne użycie aprowizowaną przepływność i wyższe koszty.

W odróżnieniu od partycjami logicznymi partycje fizyczne są wewnętrznej implementacji systemu. Nie można kontrolować, rozmiar, położenie lub liczbę partycji fizycznych, a nie może kontrolować mapowanie między partycjami logicznymi i partycje fizyczne. Jednak możesz kontrolować liczbę partycji logicznej i stopień rozproszenia danych, obciążenia i przepływność według [wybór klucza partycji logicznej bezpośrednio](partitioning-overview.md#choose-partitionkey).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [wybór klucza partycji](partitioning-overview.md#choose-partitionkey).
* Dowiedz się więcej o [aprowizowanej przepływności w usłudze Azure Cosmos DB](request-units.md).
* Dowiedz się więcej o [dystrybucję globalną w usłudze Azure Cosmos DB](distribute-data-globally.md).
* Dowiedz się, jak [aprowizowanie przepływności na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* Dowiedz się, jak [aprowizowanie przepływności na bazie danych Azure Cosmos](how-to-provision-database-throughput.md).
