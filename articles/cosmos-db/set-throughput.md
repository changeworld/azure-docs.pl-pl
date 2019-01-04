---
title: Aprowizowanie przepływności usługi Azure Cosmos DB
description: Dowiedz się, jak ustawić aprowizowanej przepływności baz danych i kontenerów usługi Azure Cosmos DB.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: andrl
ms.openlocfilehash: 5b2847914007df414c37397d61632567c277d1b2
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2019
ms.locfileid: "53999429"
---
# <a name="provision-throughput-on-azure-cosmos-containers-and-databases"></a>Aprowizowanie przepływności na kontenerach Azure Cosmos i bazy danych

Bazy danych Azure Cosmos jest jednostką zarządzania w usłudze zestaw kontenerów. Bazy danych zawiera zestaw kontenerów niezależnej od schematu. Kontener usługi Azure Cosmos jest jednostką skalowalności, zarówno dla przepływności i magazynu. Kontener w poziomie jest podzielona na partycje w zestawie maszyn w obrębie regionu platformy Azure i są rozproszone we wszystkich regionach platformy Azure skojarzony z Twoim kontem usługi Azure Cosmos.

Usługa Azure Cosmos DB pozwala na skonfigurowanie przepływność w stopniach dwóch - **kontenerów w usłudze Azure Cosmos** i **baz danych Azure Cosmos**.

## <a name="setting-throughput-on-a-container"></a>Ustawienie przepływność na kontenerze  

Przepływność, którego obsługę zainicjowano na kontenerze usługi Azure Cosmos jest zastrzeżone wyłącznie dla kontenera. Kontener odbiera aprowizowanej przepływności przez cały czas. Aprowizowana przepływność na kontenerze jest finansowo przez umowy SLA. Aby skonfigurować przepływność na kontenerze, zobacz [jak aprowizować przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).

Ustawienie aprowizowaną przepływność na kontenerze jest powszechnie używaną opcją. Chociaż można elastycznie skalować przepływność dla kontenera, aprowizując dowolnej ilości przepływność (ru), nie można określić selektywnie przepływność dla partycje logiczne. Gdy obciążenie pracą partycji logicznej zużywa więcej niż przepływność, która została przydzielona do określonej partycji logicznej, operacje otrzyma współczynnik limited. Sytuacji ograniczania szybkości, można zwiększyć przepływność dla całego kontenera lub spróbuj ponownie wykonać operację. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [partycjami logicznymi](partition-data.md).

Zalecane jest, można skonfigurować przepływność na poziomie szczegółowości kontenera podczas mają gwarancji wydajności dla kontenera.

Przepływność aprowizowana w kontenerze usługi Azure Cosmos jest równomiernie rozłożone między partycjami logicznymi kontenera. Ponieważ jedną lub więcej partycji logicznej kontenera są hostowane przez partycję fizyczną, partycje fizyczne należeć wyłącznie do kontenera i obsługuje przepływnością aprowizowaną dla kontenera. Na poniższej ilustracji przedstawiono, jak fizyczną partycję hostuje jedną lub więcej partycji logicznej kontenera:

![Partycję fizyczną](./media/set-throughput/resource-partition.png)

## <a name="setting-throughput-on-a-database"></a>Ustawienie przepływności w bazie danych

Podczas aprowizowania przepływności w bazie danych Azure Cosmos przepływność jest współużytkowana przez wszystkie kontenery w bazie danych, chyba że został określony aprowizowaną przepływność w określonych kontenerach. Udostępnianie przepływności bazy danych między jego kontenerów jest analogiczne do hostowania bazy danych w klastrze maszyn. Ponieważ wszystkie kontenery w bazie danych są współdzielone zasoby, które są dostępne na maszynie, naturalny nie uzyskasz przewidywalną wydajność na dowolnym określonym kontenerze. Aby skonfigurować przepływność w bazie danych, zobacz [sposobu konfigurowania aprowizowanej przepływności w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).

Ustawienie przepływności na bazie danych Azure Cosmos gwarantuje otrzymywać aprowizowanej przepływności przez cały czas. Od wszystkich kontenerów w ramach udziału aprowizowanej przepływności bazy danych usługi Azure Cosmos DB nie zapewnia żadnych przewidywalnej przepływności gwarancje dla danego kontenera w tej bazie danych. Część przepływność, którą może odbierać określonego kontenera jest zależna od:

* Liczba kontenerów
* Wybór kluczy partycji dla różnych kontenerów i
* Rozkład obciążenia między różne partycje logiczne kontenerów. 

Zaleca się skonfigurować przepływność w bazie danych, podczas udostępniania informacji o przepływności w wielu kontenerach, ale nie chcesz przeznaczyć przepływność dowolnego określonego kontenera. Poniżej przedstawiono kilka przykładów, w których jest preferowana względem aprowizowanie przepływności na poziomie bazy danych:

* Udostępnianie aprowizowanej przepływności bazy danych w zestawie kontenerów jest przydatne w przypadku aplikacji z wieloma dzierżawami. Każdy użytkownik może być reprezentowany przez różne kontenera usługi Azure Cosmos.

* Udostępnianie aprowizowanej przepływności bazy danych przez zestaw kontenerów jest przydatne w przypadku, gdy w przypadku migracji z bazy danych NoSQL (np. bazy danych MongoDB, bazy danych Cassandra) hostowanego w klastrze składającym się z maszynami wirtualnymi lub z poziomu lokalnych serwerów fizycznych do usługi Azure Cosmos DB. Można potraktować aprowizowanej przepływności skonfigurowany w bazie danych Azure Cosmos jako odpowiednik logiczny (ale bardziej ekonomiczne i elastycznych), moc obliczeniową bazy danych MongoDB lub bazy danych Cassandra klastra.  

Wszystkie kontenery utworzone w bazie danych z aprowizowaną przepływnością musi zostać utworzona z kluczem partycji. W dowolnym czasie, przepływność przydzielanych do kontenera w bazie danych jest rozłożona na wszystkie partycje logiczne tego kontenera. W przypadku kontenerów udostępnianie aprowizowaną przepływność w bazie danych nie umożliwiają selektywne stosowanie przepływności do określonego kontenera lub partycji logicznej. Jeśli obciążenie na partycji logicznej zużywa więcej niż z przepływnością, którą jest przydzielany do określonej partycji logicznej, operacji będzie ograniczone szybkości. Sytuacji ograniczania szybkości, można zwiększyć przepływność dla całego kontenera lub spróbuj ponownie wykonać operację. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [partycjami logicznymi](partition-data.md).

Wiele partycji logicznej udostępnianie przepływnością aprowizowaną do bazy danych mogą być hostowane na jednej partycji fizycznych. Podczas jednej partycji logicznej kontenera zawsze ma zakres partycji fizycznych, partycjami logicznymi oznaczonym literą "L", "C" kontenery udostępnianie aprowizowanej przepływności bazy danych można mapowane i hostowane na partycje fizyczne "R". Na poniższej ilustracji przedstawiono, jak fizyczną partycję można hostować jeden lub więcej partycjami logicznymi, które należą do różnych kontenerów w bazie danych:

![Partycję fizyczną](./media/set-throughput/resource-partition2.png)

## <a name="setting-throughput-on-a-database-and-a-container"></a>Ustawianie przepływności na bazę danych i kontener

Możesz połączyć dwa modele, aprowizowania przepływności na bazę danych i kontener jest dozwolone. Poniższy przykład pokazuje, jak aprowizować przepływność mierzoną w bazie danych Azure Cosmos i kontener:

* Można utworzyć bazy danych programu Azure Cosmos o nazwie "Z" z aprowizowaną przepływnością RUs 'K'. 
* Następnie należy utworzyć pięć kontenerów o nazwie A, B, C, D i E w bazie danych.
* Można jawnie skonfigurować "P" (RUS) z aprowizowaną przepływność w kontenerze "B".
* Przepływność (RUS) 'K' jest współużytkowany przez cztery kontenery — A "," C "," D "i" E. Dokładne zalecenia dotyczące ilości przepływności dostępne a, C, D lub E różnią się wiąże się nie umowy SLA dla każdego kontenera poszczególnych przepływności.
* Kontener "B" jest gwarantowane do uzyskiwania informacji o przepływności "P" (RUS) przez cały czas i jest wspierana przez umowy SLA.

## <a name="comparison-of-models"></a>Porównanie modeli

|**Limit przydziału**  |**Przepływność aprowizowana w bazie danych**  |**Przepływność aprowizowana w kontenerze**|
|---------|---------|---------|
|Minimalny (RUS) |400 (po pierwsze cztery kontenery każdego kontenera dodatkowe wymaga co najmniej 100 jednostek RU/s). |400|
|Minimalna jednostek żądań na kontener|100|400|
|Minimalny (RUS), które są wymagane do korzystania z 1 GB miejsca do magazynowania|40|40|
|Maksymalna (RUS)|Bez ograniczeń w bazie danych|Bez ograniczeń w kontenerze|
|Jednostki zarezerwowane przypisane/dostępne do określonego kontenera|Nie gwarancji. Przypisane do danego kontenera (RUS) są zależne od właściwości, takie jak — wybór kontenery, które współużytkują przepustowość, dystrybucji obciążenia, liczba kontenerów z kluczami partycji. |Wszystkie jednostki zarezerwowane skonfigurowane w kontenerze są przeznaczone wyłącznie do kontenera.|
|Maksymalny rozmiar magazynu dla kontenera|Nieograniczona liczba|Nieograniczona liczba|
|Maksymalna przepływność na partycji logicznej kontenera|10K (RUS)|10K (RUS)|
|Maksymalna pojemność (danych + indeksu) w jednej partycji logicznej kontenera|10 GB|10 GB|

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [partycjami logicznymi](partition-data.md)
* Dowiedz się, [jak aprowizować przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md)
* Dowiedz się, [jak aprowizować przepływność mierzoną w bazie danych Azure Cosmos](how-to-provision-database-throughput.md)

