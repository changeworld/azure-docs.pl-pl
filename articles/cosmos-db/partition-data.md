---
title: Partycjonowanie i skalowanie w poziomie w usłudze Azure Cosmos DB
description: Dowiedz się, jak działa partycjonowanie w usłudze Azure Cosmos DB, jak skonfigurować partycjonowanie i klucze partycji oraz jak wybrać odpowiedni klucz partycji dla aplikacji.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: cbd171e10cc1a8b27de98d9d4d779f345ac5a3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246619"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partycjonowanie i skalowanie w poziomie w usłudze Azure Cosmos DB

W tym artykule opisano partycje fizyczne i logiczne w usłudze Azure Cosmos DB. Omówiono w nim również najlepsze rozwiązania dotyczące skalowania i partycjonowania. 

## <a name="logical-partitions"></a>Partycje logiczne

Partycja logiczna składa się z zestawu elementów, które mają ten sam klucz partycji. Na przykład w kontenerze, gdzie `City` wszystkie elementy `City` zawierają właściwość, można użyć jako klucz partycji dla kontenera. Grupy elementów, które `City`mają określone `London` `Paris`wartości `NYC`dla , takich jak , i , tworzą różne partycje logiczne. Nie musisz się martwić o usunięcie partycji po usunięciu danych źródłowych.

W usłudze Azure Cosmos DB kontener jest podstawową jednostką skalowalności. Dane, które są dodawane do kontenera i przepływności, które można aprowizować w kontenerze są automatycznie (poziomo) podzielone na partycje w zestawie partycji logicznych. Dane i przepływność są podzielone na partycje na podstawie klucza partycji, który określisz dla kontenera usługi Azure Cosmos. Aby uzyskać więcej informacji, zobacz [Tworzenie kontenera usługi Azure Cosmos](how-to-create-container.md).

Partycja logiczna definiuje również zakres transakcji bazy danych. Elementy w obrębie partycji logicznej można aktualizować przy użyciu [transakcji z izolacją migawki](database-transactions-optimistic-concurrency.md). Gdy nowe elementy są dodawane do kontenera, nowe partycje logiczne są tworzone w sposób przezroczysty przez system.

## <a name="physical-partitions"></a>Partycje fizyczne

Kontener usługi Azure Cosmos jest skalowany przez dystrybucję danych i przepływności na dużej liczbie partycji logicznych. Wewnętrznie jedna lub więcej partycji logicznych jest mapowanych na partycję fizyczną, która składa się z zestawu replik, określanego również jako [*zestaw replik*](global-dist-under-the-hood.md). Każdy zestaw replik obsługuje wystąpienie aparatu bazy danych usługi Azure Cosmos. Zestaw replik sprawia, że dane przechowywane w partycji fizycznej trwałe, wysoce dostępne i spójne. Partycja fizyczna obsługuje maksymalną ilość jednostek magazynu i żądań (RUs). Każda replika, która tworzy partycję fizyczną dziedziczy przydział magazynu partycji. Wszystkie repliki partycji fizycznej łącznie obsługują przepływność, która jest przydzielona do partycji fizycznej. 

Na poniższej ilustracji pokazano, jak partycje logiczne są mapowane na partycje fizyczne, które są dystrybuowane globalnie:

![Obraz przedstawiający partycjonowanie usługi Azure Cosmos DB](./media/partition-data/logical-partitions.png)

Przepływność aprowizowana dla kontenera jest podzielona równomiernie między partycje fizyczne. Projekt klucza partycji, który nie rozdziela żądań przepływności równomiernie może utworzyć "gorące" partycje. Partycje gorące może spowodować ograniczenie szybkości i nieefektywne użycie aprowizowanej przepływności i wyższe koszty.

W przeciwieństwie do partycji logicznych partycje fizyczne są wewnętrzną implementacją systemu. Nie można kontrolować rozmiaru, rozmieszczenia lub liczby partycji fizycznych i nie można kontrolować mapowania między partycjami logicznymi a partycjami fizycznymi. Można jednak kontrolować liczbę partycji logicznych i dystrybucję danych, obciążenia i przepływności, [wybierając odpowiedni logiczny klucz partycji](partitioning-overview.md#choose-partitionkey).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [wyborze klucza partycji](partitioning-overview.md#choose-partitionkey).
* Dowiedz się więcej o [aprowizowanej przepływności w usłudze Azure Cosmos DB.](request-units.md)
* Dowiedz się więcej o [dystrybucji globalnej w usłudze Azure Cosmos DB](distribute-data-globally.md).
* Dowiedz się, jak [aprowizować przepływność w kontenerze usługi Azure Cosmos.](how-to-provision-container-throughput.md)
* Dowiedz się, jak [aprowizować przepływność w bazie danych usługi Azure Cosmos.](how-to-provision-database-throughput.md)
