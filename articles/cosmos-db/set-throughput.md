---
title: Aprowizowanie przepływności na kontenerach Azure Cosmos i bazy danych
description: Dowiedz się, jak ustawić aprowizowanej przepływności baz danych i kontenerów usługi Azure Cosmos.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: andrl
ms.openlocfilehash: 8335a235de708227136400f3af8fa7b4d0a52e29
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520908"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Aprowizacja przepływności kontenerów i baz danych

Bazy danych Azure Cosmos jest jednostką zarządzania w usłudze zestaw kontenerów. Bazy danych zawiera zestaw kontenerów niezależnej od schematu. Kontener usługi Azure Cosmos jest jednostką skalowalności, zarówno dla przepływności i magazynu. Kontener w poziomie jest podzielona na partycje w zestawie maszyn w obrębie regionu platformy Azure i są rozproszone we wszystkich regionach platformy Azure skojarzony z Twoim kontem usługi Azure Cosmos.

Za pomocą usługi Azure Cosmos DB można skonfigurować przepływność w dwóch stopniach szczegółowości:
 
- Kontenery usługi Azure Cosmos
- Baz danych Azure Cosmos

## <a name="set-throughput-on-a-container"></a>Ustawianie przepływności do kontenera  

Przepływność, którego obsługę zainicjowano na kontenerze usługi Azure Cosmos jest zastrzeżone wyłącznie dla kontenera. Kontener odbiera aprowizowanej przepływności przez cały czas. Aprowizowana przepływność na kontenerze jest finansowo przez umowy SLA. Aby skonfigurować przepływność na kontenerze, zobacz [Aprowizowanie przepływności na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).

Ustawienie aprowizowaną przepływność na kontenerze jest powszechnie używaną opcją. Można elastycznie skalować przepływność dla kontenera, aprowizując dowolnej ilości przepływność przy użyciu jednostek żądań (ru). Ale selektywnie nie można określić przepustowość partycji logicznej. 

W przypadku obciążeń uruchomionych na partycji logicznej zużywa więcej niż przepływność, która została przydzielona do określonej partycji logicznej, operacji pobrać limited szybkości. Sytuacji ograniczania szybkości, można zwiększyć przepływność dla całego kontenera lub ponów próbę wykonania operacji. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [partycjami logicznymi](partition-data.md).

Firma Microsoft zaleca, można skonfigurować przepływność na poziomie szczegółowości kontenera podczas mają gwarancji wydajności dla kontenera.

Przepływność aprowizowana w kontenerze usługi Azure Cosmos jest równomiernie rozłożone między partycjami logicznymi kontenera. Ponieważ jedną lub więcej partycji logicznej kontenera są hostowane przez partycję fizyczną, partycje fizyczne należeć wyłącznie do kontenera i obsługuje przepływnością aprowizowaną dla kontenera. 

Na poniższej ilustracji przedstawiono, jak fizyczną partycję hostuje jedną lub więcej partycji logicznej kontenera:

![Partycję fizyczną](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Ustawianie przepływności w bazie danych

Podczas aprowizowania przepływności w bazie danych Azure Cosmos przepływność jest udostępniany we wszystkich kontenerach w bazie danych. Wyjątkiem jest, jeśli określono aprowizowaną przepływność w określonych kontenerach. Udostępnianie przepływności bazy danych między jego kontenerów jest analogiczne do hostowania bazy danych w klastrze maszyn. Ponieważ wszystkie kontenery w bazie danych są współdzielone zasoby, które są dostępne na maszynie, naturalny nie uzyskasz przewidywalną wydajność na dowolnym określonym kontenerze. Aby skonfigurować przepływność w bazie danych, zobacz [Konfiguruj aprowizowaną przepływność w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).

Ustawienie przepływności na bazie danych Azure Cosmos gwarantuje otrzymywać aprowizowanej przepływności przez cały czas. Ponieważ wszystkie kontenery w bazie danych mają aprowizowanej przepływności, usługi Azure Cosmos DB nie zapewnia żadnych przewidywalnej przepływności gwarancje dla danego kontenera w tej bazie danych. Część przepływność, którą może odbierać określonego kontenera jest zależna od:

* Liczba kontenerów.
* Wybór kluczy partycji dla różnych kontenerów.
* Rozkład obciążenia między różne partycje logiczne kontenerów. 

Zaleca się skonfigurować przepływność w bazie danych, podczas udostępniania informacji o przepływności w wielu kontenerach, ale nie chcesz przeznaczyć przepływność dowolnego określonego kontenera. 

W poniższych przykładach pokazano, gdzie jest preferowane zaprowizować przepływność na poziomie bazy danych:

* Udostępnianie aprowizowanej przepływności bazy danych w zestawie kontenerów jest przydatne w przypadku aplikacji wielodostępnych. Każdy użytkownik może być reprezentowany przez różne kontenera usługi Azure Cosmos.

* Udostępnianie aprowizowanej przepływności bazy danych przez zestaw kontenerów jest przydatne w przypadku, gdy migracja bazy danych NoSQL, takiego jak MongoDB lub bazy danych Cassandra hostowanych w klastrze maszyn wirtualnych lub fizycznych serwerów lokalnych do usługi Azure Cosmos DB. Pomyśl o aprowizowanej przepływności, skonfigurowanym w bazie danych Azure Cosmos jako odpowiednik logiczny, ale bardziej ekonomiczne i elastyczna, jak moc obliczeniową bazy danych MongoDB lub bazy danych Cassandra klastra.  

Wszystkie kontenery utworzone w bazie danych z aprowizowaną przepływnością musi zostać utworzona z kluczem partycji. W dowolnym czasie, przepływność przydzielanych do kontenera w bazie danych jest rozłożona na wszystkie partycje logiczne tego kontenera. W przypadku kontenerów, które współużytkują aprowizowaną przepływność w bazie danych nie umożliwiają selektywne stosowanie przepływności do określonego kontenera lub partycji logicznej. 

Jeśli obciążenie na partycji logicznej zużywa więcej niż z przepływnością, którą jest przydzielany do określonej partycji logicznej, operacji są ograniczone szybkości. Sytuacji ograniczania szybkości, można zwiększyć przepływność dla całego kontenera lub ponów próbę wykonania operacji. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [partycjami logicznymi](partition-data.md).

Wiele partycji logicznych, które współużytkują przepływnością aprowizowaną do bazy danych mogą być hostowane na jednej partycji fizycznych. Podczas jednej partycji logicznej kontenera zawsze ma zakres partycji fizycznych, partycji logicznej "L" różnych kontenerów "C", które można udostępniać aprowizowanej przepływności bazy danych można mapowane i hostowane na partycje fizyczne "R". 

Na poniższej ilustracji przedstawiono, jak fizyczną partycję można hostować jeden lub więcej partycjami logicznymi, które należą do różnych kontenerów w bazie danych:

![Partycję fizyczną](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Ustawianie przepływności na bazę danych i kontener

Możesz połączyć dwa modele. Przepływność inicjowania obsługi administracyjnej, zarówno w bazie danych, jak i w kontenerze jest dozwolone. Poniższy przykład pokazuje, jak aprowizować przepływność mierzoną w bazie danych Azure Cosmos i kontener:

* Można utworzyć bazy danych Azure Cosmos o nazwie Z aprowizowaną przepływność "K" (RUS). 
* Następnie należy utworzyć pięć kontenerów o nazwie A, B, C, D i E w bazie danych.
* Jawnie konfigurowalnych RUs "P" z aprowizowaną przepływność w kontenerze o nazwie B.
* Przepływność (RUS) "K" jest współużytkowany przez cztery kontenery, A "," C "," D "i" E. Dokładne zalecenia dotyczące ilości przepływności można A, C, D lub E różni się. Nie istnieją żadne umowy SLA dla każdego kontenera poszczególnych przepływności.
* Kontener o nazwie B jest gwarantowane do uzyskiwania informacji o przepływności "P" (RUS) przez cały czas. Jest wspierana przez umowy SLA.

## <a name="update-throughput-on-a-database-or-a-container"></a>Przepływność aktualizacji w bazie danych lub kontener

Po utworzeniu kontenera usługi Azure Cosmos lub bazy danych, możesz zaktualizować aprowizowanej przepływności. Nie ma żadnego limitu z maksymalną przepływnością na skonfigurowanym w bazie danych lub kontenera. Minimalnej aprowizowanej przepływności zależy od następujących czynników: 

* Rozmiar maksymalny danych, które nigdy nie są przechowywane w kontenerze
* Maksymalna przepływność, które kiedykolwiek aprowizować w kontenerze
* Maksymalna liczba kontenerów Azure Cosmos, które warto utworzyć w bazie danych z udostępnionej przepływności. 

Można programowo pobrać minimalna przepływność w kontenerze lub bazy danych za pomocą zestawów SDK lub Wyświetl wartości w witrynie Azure portal. Korzystając z zestawu .NET SDK [DocumentClient.ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) metoda umożliwia skalowanie wartość aprowizowanej przepływności. Podczas korzystania z zestawu SDK Java [RequestOptions.setOfferThroughput](sql-api-java-samples.md#offer-examples) metoda umożliwia skalowanie wartość aprowizowanej przepływności. 

Korzystając z zestawu .NET SDK [DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) metoda pozwala pobrać minimalna przepływność w kontenerze lub bazy danych. 

Aprowizowana przepływność w kontenerze lub bazy danych można skalować w dowolnym momencie. Można uruchomić operacji skalowania w dół po okresie bezczynności 4 godziny. Okres bezczynności jest zdefiniowany jako czas okresie nie było żadnych oferty zamienianie (co obejmuje skalowanie w górę i w dół) w kontenerze lub bazy danych. 

## <a name="comparison-of-models"></a>Porównanie modeli

|**Limit przydziału**  |**Przepływność aprowizowana w bazie danych**  |**Przepływność aprowizowana w kontenerze**|
|---------|---------|---------|
|Minimalny (RUS) |400 (po pierwsze cztery kontenery każdego kontenera dodatkowe wymaga co najmniej 100 jednostek ru na sekundę). |400|
|Minimalna jednostek żądań na kontener|100|400|
|Minimalny (RUS), które są wymagane do korzystania z 1 GB miejsca do magazynowania|40|40|
|Maksymalna (RUS)|Bez ograniczeń w bazie danych.|Bez ograniczeń w kontenerze.|
|Jednostki zarezerwowane przypisane lub będą dostępne do określonego kontenera|Nie gwarancji. Przypisane do danego kontenera (RUS) są zależne od właściwości. Właściwości mogą być wybór kontenery, które współużytkują przepływność z kluczami partycji, dystrybucji obciążenia i liczbę kontenerów. |Wszystkie jednostki zarezerwowane skonfigurowane w kontenerze są przeznaczone wyłącznie do kontenera.|
|Maksymalny rozmiar magazynu dla kontenera|Bez ograniczeń.|Bez ograniczeń.|
|Maksymalna przepływność na partycji logicznej kontenera|10K (RUS)|10K (RUS)|
|Maksymalna pojemność (danych + indeksu) w jednej partycji logicznej kontenera|10 GB|10 GB|

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [partycjami logicznymi](partition-data.md).
* Dowiedz się, jak [aprowizowanie przepływności na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* Dowiedz się, jak [aprowizowanie przepływności na bazie danych Azure Cosmos](how-to-provision-database-throughput.md).

