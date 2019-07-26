---
title: Partycjonowanie i skalowanie w poziomie Azure Cosmos DB
description: Dowiedz się więcej o tym, jak partycjonowanie działa w Azure Cosmos DB, jak skonfigurować partycjonowanie i klucze partycji oraz jak wybrać właściwy klucz partycji dla aplikacji.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 1b624270fe22004a6ae64affe87b2fc22a2e9a66
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467681"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partycjonowanie i skalowanie w poziomie Azure Cosmos DB

W tym artykule opisano partycje fizyczne i logiczne w Azure Cosmos DB. Omówiono w nim również najlepsze rozwiązania dotyczące skalowania i partycjonowania. 

## <a name="logical-partitions"></a>Partycje logiczne

Partycja logiczna składa się z zestawu elementów, które mają ten sam klucz partycji. Na przykład w kontenerze, w którym wszystkie elementy zawierają `City` właściwość, można użyć `City` jako klucza partycji dla kontenera. `City`Grupy elementów, dla których określone wartości, takie jak `London`, `Paris`, i `NYC`, tworzą odrębne partycje logiczne. Nie musisz martwić się o usunięcie partycji, gdy dane podstawowe są usuwane.

W Azure Cosmos DB kontener jest podstawową jednostką skalowalności. Dane dodawane do kontenera i przepływność, które są inicjowane w kontenerze, są automatycznie (w poziomie) partycjonowane w zestawie partycji logicznych. Dane i przepływność są partycjonowane na podstawie klucza partycji określonego dla kontenera usługi Azure Cosmos. Aby uzyskać więcej informacji, zobacz [Tworzenie kontenera usługi Azure Cosmos](how-to-create-container.md).

Partycja logiczna definiuje również zakres transakcji bazy danych. Można aktualizować elementy w ramach partycji logicznej przy użyciu [transakcji z izolacją migawki](database-transactions-optimistic-concurrency.md). Gdy nowe elementy są dodawane do kontenera, nowe partycje logiczne są w sposób niewidoczny dla użytkownika tworzone przez system.

## <a name="physical-partitions"></a>Partycje fizyczne

Kontener usługi Azure Cosmos jest skalowany przez dystrybuowanie danych i przepływności na dużą liczbę partycji logicznych. Wewnętrznie, co najmniej jedna partycja logiczna jest zamapowana na partycję fizyczną, która składa się z zestawu replik, nazywanego również [*zestawem replik*](global-dist-under-the-hood.md). Każdy zestaw replik obsługuje wystąpienie aparatu bazy danych Azure Cosmos DB. Zestaw replik sprawia, że dane przechowywane w partycji fizycznej są trwałe, wysoce dostępne i spójne. Partycja fizyczna obsługuje maksymalną ilość magazynu i jednostek żądań (jednostek ru). Każda replika tworząca partycję fizyczną dziedziczy przydział magazynowania partycji. Wszystkie repliki partycji fizycznej w sposób zbiorczy obsługują przepływność przydzieloną do partycji fizycznej. 

Na poniższej ilustracji przedstawiono, jak partycje logiczne są mapowane na partycje fizyczne dystrybuowane globalnie:

![Obraz, który demonstruje Azure Cosmos DB partycjonowanie](./media/partition-data/logical-partitions.png)

Obsługa przepływności dla kontenera jest dzielona równomiernie między partycjami fizycznymi. Projekt klucza partycji, który nie dystrybuuje żądań przepływności nawet może tworzyć partycje "gorąca". Partycje gorącą mogą spowodować ograniczenie szybkości i niewydajne wykorzystanie przepływności i wyższych kosztów.

W przeciwieństwie do partycji logicznych, partycje fizyczne są wewnętrzną implementacją systemu. Nie można kontrolować rozmiaru, położenia ani liczby partycji fizycznych i nie można kontrolować mapowania między partycjami logicznymi i partycjami fizycznymi. Można jednak kontrolować liczbę partycji logicznych i dystrybucję danych, obciążenia i przepływność, [wybierając odpowiedni klucz partycji logicznej](partitioning-overview.md#choose-partitionkey).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej [na temat wybierania klucza partycji](partitioning-overview.md#choose-partitionkey).
* Informacje o [aprowizacji przepływności w Azure Cosmos DB](request-units.md).
* Informacje o [dystrybucji globalnej w Azure Cosmos DB](distribute-data-globally.md).
* Dowiedz się, jak [zainicjować przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* Dowiedz się, jak [udostępnić przepływność w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).
