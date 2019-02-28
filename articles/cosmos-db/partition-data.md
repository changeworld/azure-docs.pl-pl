---
title: Partycjonowanie i skalowanie w poziomie w usłudze Azure Cosmos DB
description: Więcej informacji na temat działa jak partycjonowania w usługi Azure Cosmos DB, jak skonfigurować partycje i klucze partycji oraz sposobu wybierania klucza partycji odpowiednie dla twojej aplikacji.
ms.author: mjbrown
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 4c6847d8f870c02228aa14ab9e11c85b091ec48b
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959956"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partycjonowanie i skalowanie w poziomie w usłudze Azure Cosmos DB

W tym artykule opisano fizycznie i logicznie partycji w usłudze Azure Cosmos DB. Omawia także najlepsze rozwiązania dotyczące skalowania i partycjonowania. 

## <a name="logical-partitions"></a>Partycje logiczne

Partycja logiczna zawiera zestaw elementów, które mają ten sam klucz partycji. Na przykład w kontenerze, gdzie zawierać wszystkie elementy `City` właściwości, można użyć `City` jako klucza partycji dla kontenera. Grupy elementów, które mają określone wartości dla `City`, takich jak `London`, `Paris`, i `NYC`, tworzą oddzielnej partycji logicznej. Nie trzeba martwić się o usunięcie partycji, po usunięciu danych bazowych.

W usłudze Azure Cosmos DB kontener jest podstawową jednostką skalowalności. Dane, które jest dodawany do kontenera i przepływność, którą można aprowizować w kontenerze są automatycznie (w poziomie) podzielonej na partycje w określonym zestawie partycji logicznej. Danych i przepływności są partycjonowane na podstawie określonego klucza partycji dla kontenera usługi Azure Cosmos DB. Aby uzyskać więcej informacji, zobacz [utworzyć kontener usługi Azure Cosmos DB](how-to-create-container.md).

Partycja logiczna definiuje zakres transakcji bazy danych. Należy zaktualizować elementów w ramach partycji logicznej przy użyciu transakcji z użyciem izolacji migawki. Kiedy nowe elementy są dodawane do kontenera, nowych partycji logicznej przezroczyste są tworzone przez system.

## <a name="physical-partitions"></a>Partycje fizyczne

Kontener usługi Azure Cosmos DB jest skalowana przez dystrybucję dużej liczby partycji logicznej danych i przepływności. Wewnętrznie, co najmniej jedną partycję logiczne są mapowane na fizyczną partycję, która składa się z zestawu replik, nazywana także *zestawu replik*. Każdego elementu zestawu replik hosty wystąpienie aparatu bazy danych Azure Cosmos DB. Zestawu replik sprawia, że dane są przechowywane w partycję fizyczną trwałych, wysoce dostępny i spójne. Fizyczną partycję obsługuje maksymalną liczbę jednostek magazynu i żądań (ru). Każdej repliki, który tworzy partycję fizyczną dziedziczy przydział magazynowania partycji. Wszystkie repliki partycji fizycznej obsługuje zbiorczo przepływność, którą jest przydzielany do fizyczną partycję. 

Na poniższej ilustracji przedstawiono, jak logicznej partycji są mapowane na partycje fizyczne, które są globalnie rozproszone:

![Obraz, który pokazuje partycjonowanie w usłudze Azure Cosmos DB](./media/partition-data/logical-partitions.png)

Przepływnością aprowizowaną dla kontenera jest równomiernie podzielone między partycje fizyczne. Projekt klucza partycji, który nie równomierne rozłożenie żądania przepływności może tworzyć partycje "gorącymi". Gorąca partycji może spowodować, ograniczania szybkości i nieefektywne użycie aprowizowanej przepływności.

W odróżnieniu od partycjami logicznymi partycje fizyczne są wewnętrznej implementacji systemu. Nie można kontrolować, rozmiar, położenie lub liczbę partycji fizycznych, a nie może kontrolować mapowanie między partycjami logicznymi i partycje fizyczne. Jednak można kontrolować liczbę partycji logicznej i dystrybucję danych i przepływności, wybierając klawisz prawo partycji.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [wybór klucza partycji](partitioning-overview.md#choose-partitionkey).
* Dowiedz się więcej o [aprowizowanej przepływności w usłudze Azure Cosmos DB](request-units.md).
* Dowiedz się więcej o [dystrybucję globalną w usłudze Azure Cosmos DB](distribute-data-globally.md).
* Dowiedz się, jak [aprowizować przepływność w kontenerze usługi Azure Cosmos DB](how-to-provision-container-throughput.md).
* Dowiedz się, jak [aprowizowanie przepływności w bazie danych Azure Cosmos DB](how-to-provision-database-throughput.md).
