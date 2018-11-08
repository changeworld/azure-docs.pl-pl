---
title: Partycjonowanie i skalowanie w poziomie w usłudze Azure Cosmos DB
description: Więcej informacji na temat sposobu partycjonowania działa w usługi Azure Cosmos DB, jak skonfigurować partycje i kluczy partycji i Wybieranie klucza partycji odpowiednie dla twojej aplikacji.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: andrl
ms.openlocfilehash: 9e75068a1e05f12c7b887b601777227902f15ed8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238579"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partycjonowanie i skalowanie w poziomie w usłudze Azure Cosmos DB

W tym artykule opisano fizycznie i logicznie partycji w usłudze Azure Cosmos DB i najlepsze rozwiązania dotyczące skalowania i partycjonowanie. 

## <a name="logical-partitions"></a>Partycjami logicznymi

Partycja logiczna składa się z zbiór elementów z tym samym kluczem partycji. Na przykład, należy wziąć pod uwagę kontenera, w którym zawierają wszystkie elementy `City` właściwość, należy użyć `City` jako klucza partycji dla kontenera. Grupy elementów z określonymi wartościami dla `City` takich jak "Londyn", "Paryż", "Warszawa" itp. będzie stanowić oddzielnej partycji logicznej.

W usłudze Azure Cosmos DB kontener jest podstawową jednostką skalowalności. Dane dodane do kontenera i przepływność, które można aprowizować w kontenerze są automatycznie (w poziomie) podzielonej na partycje w określonym zestawie partycji logicznej. Są one partycjonowane na podstawie określonego klucza partycji dla kontenera Cosmos. Aby dowiedzieć się więcej, zobacz [sposobu określania klucza partycji dla kontenera usługi Cosmos](how-to-create-container.md) artykułu.

Partycja logiczna definiuje zakres transakcji bazy danych. Należy zaktualizować elementów w ramach partycji logicznej przy użyciu transakcji z użyciem izolacji migawki.

Po dodaniu nowych elementów do kontenera lub przepływnością aprowizowaną dla kontenera jest zwiększana, nowych partycji logicznej przezroczyste są tworzone przez system.

## <a name="physical-partitions"></a>Partycje fizyczne

Kontener Cosmos skalowania przez dystrybucję dużej liczby partycji logicznej danych i przepływności. Wewnętrznie, co najmniej jedną partycję logiczne są mapowane na **partycja zasobu** składający się z zestawu replik, nazywana także zestawu replik. Każdego zestawu replik znajduje się wystąpienie aparatu bazy danych Cosmos. Zestawu replik sprawia, że dane są przechowywane w partycji zasobów trwałe, wysoce dostępny i spójne. Partycja zasobu obsługuje stały, maksymalna wielkość magazynu oraz jednostek RU. Każdej repliki partycji zasobu wchodzących w skład dziedziczy przydział magazynowania. I wszystkie repliki partycji zasobów zbiorczo obsługiwać przepływności przydzielane do partycji zasobów. Na poniższej ilustracji przedstawiono, jak logicznej partycji są mapowane na partycji zasobów, które są globalnie rozproszone:

![Partycjonowanie usługi Azure Cosmos DB](./media/partition-data/logical-partitions.png)

Przepływnością aprowizowaną dla kontenera jest równomiernie podzielone między partycjami zasobów. W związku z tym projekt klucza partycji, który nie równomierne rozłożenie żądania przepływności można utworzyć partycji "gorącymi". Gorąca partycji może spowodować ograniczanie szybkości i nieefektywne użycie aprowizowanej przepływności.

W przeciwieństwie do partycji logicznej partycji zasobów są wewnętrznej implementacji systemu. Nie można kontrolować, ich rozmiar, położenie, liczby lub mapowanie między partycjami logicznymi i partycji zasobów. Jednak można kontrolować liczbę partycji logicznej i dystrybucję danych i przepływności, wybierając klawisz prawo partycji.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule omówienie partycjonowanie danych i najlepszych rozwiązań dla skalowania i udostępniane partycjonowanie w usłudze Azure Cosmos DB. 

* Dowiedz się więcej o [aprowizowanej przepływności w usłudze Azure Cosmos DB](request-units.md)
* Dowiedz się więcej o [dystrybucję globalną w usłudze Azure Cosmos DB](distribute-data-globally.md)
* Dowiedz się więcej o [wybór klucza partycji](partitioning-overview.md#choose-partitionkey)
* Dowiedz się, [jak aprowizować przepływność na kontenerze Cosmos](how-to-provision-container-throughput.md)
* Dowiedz się, [jak aprowizować przepływność mierzoną w bazie danych Cosmos](how-to-provision-database-throughput.md)
