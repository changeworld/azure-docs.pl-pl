---
title: Aprowizuj przepływność w kontenerach i bazach danych usługi Azure Cosmos
description: Dowiedz się, jak ustawić aprowizowaną przepływność kontenerów i baz danych usługi Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2019
ms.openlocfilehash: e7a64776cba00a6840af70cecad5bf9c02b3f38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251975"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Aprowizacja przepływności kontenerów i baz danych

Baza danych usługi Azure Cosmos jest jednostką zarządzania dla zestawu kontenerów. Baza danych składa się z zestawu kontenerów niezależnych od schematu. Kontener usługi Azure Cosmos to jednostka skalowalności zarówno dla przepływności, jak i magazynu. Kontener jest podzielony poziomo na partycje zestawu maszyn w regionie platformy Azure i jest dystrybuowany we wszystkich regionach platformy Azure skojarzonych z kontem usługi Azure Cosmos.

Za pomocą usługi Azure Cosmos DB można aprowizować przepływność z dwoma ziarnistościami:
 
- Kontenery usługi Azure Cosmos
- Bazy danych usługi Azure Cosmos

## <a name="set-throughput-on-a-container"></a>Ustawianie przepływności w kontenerze  

Przepływność aprowizowana w kontenerze usługi Azure Cosmos jest zarezerwowana wyłącznie dla tego kontenera. Kontener odbiera aprowizowaną przepływność przez cały czas. Aprowizowana przepływność w kontenerze jest finansowo wspierana przez ławy instalowe. Aby dowiedzieć się, jak skonfigurować przepływność w kontenerze, zobacz [Aprowizuj przepływność w kontenerze usługi Azure Cosmos.](how-to-provision-container-throughput.md)

Ustawienie aprowizowanej przepływności w kontenerze jest najczęściej używaną opcją. Można elastycznie skalować przepływność kontenera, inicjując dowolną ilość przepływności przy użyciu [jednostek żądań (RUs)](request-units.md). 

Przepływność aprowizowana dla kontenera jest równomiernie rozłożona między jego partycje fizyczne, a przy założeniu, że dobry klucz partycji, który równomiernie rozdziela partycje logiczne między partycje fizyczne, przepływność jest również rozłożona równomiernie na wszystkie partycje logiczne kontenera. Nie można selektywnie określić przepływności dla partycji logicznych. Ponieważ co najmniej jedna partycja logiczna kontenera są obsługiwane przez partycję fizyczną, partycje fizyczne należą wyłącznie do kontenera i obsługują przepływność aprowizowaną w kontenerze. 

Jeśli obciążenie uruchomione na partycji logicznej zużywa więcej niż przepływność, która została przydzielona do tej partycji logicznej, operacje uzyskać ograniczoną szybkość. Gdy występuje ograniczenie szybkości, można zwiększyć aprowizowaną przepływność dla całego kontenera lub ponowić próbę wykonania operacji. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [Partycje logiczne](partition-data.md).

Zaleca się skonfigurowanie przepływności w szczegółowości kontenera, gdy chcesz zagwarantować wydajność kontenera.

Na poniższej ilustracji pokazano, jak partycja fizyczna obsługuje jedną lub więcej partycji logicznych kontenera:

![Partycja fizyczna](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Ustawianie przepływności w bazie danych

Podczas aprowizowania przepływności w bazie danych usługi Azure Cosmos przepływność jest współużytkowana przez wszystkie kontenery (nazywane kontenerami udostępnionej bazy danych) w bazie danych. Wyjątkiem jest, jeśli określono aprowizowaną przepływność w określonych kontenerach w bazie danych. Udostępnianie aprowizowanej przepływności na poziomie bazy danych między jego kontenerami jest analogiczne do hostowania bazy danych na klastrze maszyn. Ponieważ wszystkie kontenery w bazie danych współużytkują zasoby dostępne na komputerze, naturalnie nie można uzyskać przewidywalną wydajność w żadnym określonym kontenerze. Aby dowiedzieć się, jak skonfigurować aprowizowaną przepływność w bazie danych, zobacz [Konfigurowanie aprowizowanej przepływności w bazie danych usługi Azure Cosmos](how-to-provision-database-throughput.md).

Ustawienie przepływności w bazie danych usługi Azure Cosmos gwarantuje, że otrzymujesz aprowizowaną przepływność dla tej bazy danych przez cały czas. Ponieważ wszystkie kontenery w bazie danych współużytkują aprowizowaną przepływność, usługa Azure Cosmos DB nie zapewnia żadnych przewidywalnych gwarancji przepływności dla określonego kontenera w tej bazie danych. Część przepływności, którą może odbierać określony kontener, zależy od:

* Liczba kontenerów.
* Wybór kluczy partycji dla różnych kontenerów.
* Rozkład obciążenia między różnymi partycjami logicznymi kontenerów. 

Zaleca się skonfigurowanie przepływności w bazie danych, gdy chcesz udostępnić przepływność w wielu kontenerach, ale nie chcesz dedykować przepływności do dowolnego określonego kontenera. 

Poniższe przykłady pokazują, gdzie jest preferowane do aprowizowania przepływności na poziomie bazy danych:

* Udostępnianie aprowizowanej przepływności bazy danych w zestawie kontenerów jest przydatne dla aplikacji wielodostępnej. Każdy użytkownik może być reprezentowany przez odrębny kontener usługi Azure Cosmos.

* Udostępnianie aprowizowanej przepływności bazy danych w zestawie kontenerów jest przydatne podczas migracji bazy danych NoSQL, takiej jak MongoDB lub Cassandra, hostowanej w klastrze maszyn wirtualnych lub z lokalnych serwerów fizycznych do usługi Azure Cosmos DB. Pomyśl o aprowizowanej przepływności skonfigurowanej w bazie danych usługi Azure Cosmos jako logiczny odpowiednik, ale bardziej opłacalne i elastyczne, do pojemności obliczeniowej klastra MongoDB lub Cassandra.  

Wszystkie kontenery utworzone wewnątrz bazy danych z aprowizowaną przepływnością muszą być tworzone przy pomocą [klucza partycji.](partition-data.md) W dowolnym momencie w czasie przepływności przydzielone do kontenera w bazie danych jest rozdzielana między wszystkie partycje logiczne tego kontenera. Jeśli masz kontenery, które współużytkują aprowizowaną przepływność skonfigurowaną w bazie danych, nie można selektywnie zastosować przepływności do określonego kontenera lub partycji logicznej. 

Jeśli obciążenie na partycji logicznej zużywa więcej niż przepływność, która jest przydzielona do określonej partycji logicznej, operacje są ograniczone szybkością. Gdy występuje ograniczenie szybkości, można zwiększyć przepływność dla całej bazy danych lub ponowić próbę operacji. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [Partycje logiczne](partition-data.md).

Kontenery w udostępnionej bazie danych przepływności współużytkują przepływność (RU/s) przydzieloną do tej bazy danych. W bazie danych może być maksymalnie cztery kontenery z co najmniej 400 kami ścienne.You can have up to four containers with a minimum 400 RU/s on the database. Każdy nowy pojemnik po pierwszych czterech będzie wymagać dodatkowych 100 RU / s minimum. Na przykład jeśli masz udostępnionej bazy danych przepływności z ośmiu kontenerów, minimalne RU/s w bazie danych będzie 800 RU/s.

> [!NOTE]
> W lutym 2020 r. wprowadziliśmy zmianę, która umożliwia uzyskanie maksymalnie 25 kontenerów w udostępnionej bazie danych przepływności, co lepiej umożliwia udostępnianie przepływności w kontenerach. Po pierwszych 25 kontenerów można dodać więcej kontenerów do bazy danych tylko wtedy, gdy są one [aprowizowana z dedykowaną przepływnością](#set-throughput-on-a-database-and-a-container), która jest oddzielona od przepływności współużytkowanej bazy danych.<br>
Jeśli twoje konto usługi Azure Cosmos DB zawiera już udostępnioną bazę danych przepływności z kontenerami >=25, konto i wszystkie inne konta w tej samej subskrypcji platformy Azure są zwolnione z tej zmiany. Jeśli masz opinie lub pytania, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) produktu. 

Jeśli obciążenia obejmują usuwanie i ponowne tworzenie wszystkich kolekcji w bazie danych, zaleca się upuścić pustą bazę danych i utworzyć nową bazę danych przed utworzeniem kolekcji. Na poniższej ilustracji pokazano, jak partycja fizyczna może obsługiwać jedną lub więcej partycji logicznych należących do różnych kontenerów w bazie danych:

![Partycja fizyczna](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Ustawianie przepływności w bazie danych i kontenerze

Można połączyć dwa modele. Przepływność inicjowania obsługi administracyjnej w bazie danych i kontenera jest dozwolone. W poniższym przykładzie pokazano, jak aprowizować przepływność w bazie danych usługi Azure Cosmos i kontenerze:

* Można utworzyć bazę danych usługi Azure Cosmos o nazwie *Z* z aprowizowaną przepływnością procesorów RUs *"K".* 
* Następnie utwórz pięć kontenerów o nazwach *A*, *B*, *C*, *D*i *E* w bazie danych. Podczas tworzenia kontenera B, upewnij się, aby włączyć **aprowizowanie dedykowanej przepływności dla tej** opcji kontenera i jawnie skonfigurować *"P"* RU aprowizowanej przepływności w tym kontenerze. Należy zauważyć, że można skonfigurować przepływność współużytkowaną i dedykowaną tylko podczas tworzenia bazy danych i kontenera. 

   ![Ustawianie przepływności na poziomie kontenera](./media/set-throughput/coll-level-throughput.png)

* Przepustowość procesorów RUs *"K"* jest współdzielona przez cztery kontenery *A*, *C,* *D*i *E*. Dokładna ilość przepustowości dostępna dla *A*, *C*, *D*lub *E* jest różna. Nie ma żadnych łasli dla przepływności poszczególnych kontenerów.
* Kontener o nazwie *B* jest gwarantowane, aby uzyskać *przepływność "P"* RUs cały czas. Jest wspierany przez dla.

> [!NOTE]
> Kontener z aprowizowaną przepływnością nie można przekonwertować na kontener udostępnionej bazy danych. Z drugiej strony kontener udostępnionej bazy danych nie można przekonwertować na dedykowaną przepływność.

## <a name="update-throughput-on-a-database-or-a-container"></a>Aktualizowanie przepływności w bazie danych lub kontenerze

Po utworzeniu kontenera usługi Azure Cosmos lub bazy danych można zaktualizować aprowizowaną przepływność. Nie ma limitu maksymalnej aprowizowanej przepływności, którą można skonfigurować w bazie danych lub kontenerze. [Minimalna aprowizowana przepustowość](concepts-limits.md#storage-and-throughput) zależy od następujących czynników: 

* Maksymalny rozmiar danych, jaki kiedykolwiek był przechowywany w kontenerze
* Maksymalna przepustowość, jaką kiedykolwiek udostępnisz w kontenerze
* Bieżąca liczba kontenerów usługi Azure Cosmos, które masz w bazie danych z udostępnionej przepływności. 

Można pobrać minimalną przepływność kontenera lub bazy danych programowo przy użyciu zestawów SDK lub wyświetlić wartość w witrynie Azure portal. Podczas korzystania z .NET SDK, [DocumentClient.ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) metoda umożliwia skalowanie aprowizowanej wartości przepływności. Podczas korzystania z zestawu Java SDK, [RequestOptions.setOfferThroughput](sql-api-java-samples.md#offer-examples) metoda umożliwia skalowanie aprowizowanej wartości przepływności. 

Podczas korzystania z .NET SDK, [DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) metoda umożliwia pobieranie minimalnej przepływności kontenera lub bazy danych. 

Można skalować aprowizowaną przepływność kontenera lub bazy danych w dowolnym momencie. Po wykonaniu operacji skalowania w celu zwiększenia przepływności może upłynąć więcej czasu ze względu na zadania systemowe do aprowizowania wymaganych zasobów. Można sprawdzić stan operacji skalowania w witrynie Azure portal lub programowo przy użyciu zestawów SDK. Korzystając z pliku .Net SDK, można uzyskać stan operacji `DocumentClient.ReadOfferAsync` skalowania przy użyciu metody.

## <a name="comparison-of-models"></a>Porównanie modeli

|**Parametr**  |**Przepływność aprowizowana w bazie danych**  |**Przepływność aprowizowana w kontenerze**|
|---------|---------|---------|
|Minimalne rus |400 (Po pierwszych czterech kontenerach każdy dodatkowy kontener wymaga co najmniej 100 jednostek RR na sekundę). |400|
|Minimalne procesory RUs w pojemniku|100|400|
|Maksymalna liczba procesorów RU|Nieograniczony, w bazie danych.|Nieograniczony, na pojemniku.|
|Procesory RUs przypisane lub dostępne dla określonego kontenera|Brak gwarancji. Procesory RU przypisane do danego kontenera zależą od właściwości. Właściwości mogą być wybór kluczy partycji kontenerów, które współużytkują przepływność, dystrybucji obciążenia i liczby kontenerów. |Wszystkie procesory RU skonfigurowane w kontenerze są zarezerwowane wyłącznie dla kontenera.|
|Maksymalna ilość miejsca do przechowywania kontenera|Nieograniczony.|Nieograniczony.|
|Maksymalna przepływność na partycję logiczną kontenera|10K rus|10K rus|
|Maksymalny magazyn (dane + indeks) na partycję logiczną kontenera|20 GB|20 GB|

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [partycjach logicznych](partition-data.md).
* Dowiedz się, jak [aprowizować przepływność w kontenerze usługi Azure Cosmos.](how-to-provision-container-throughput.md)
* Dowiedz się, jak [aprowizować przepływność w bazie danych usługi Azure Cosmos.](how-to-provision-database-throughput.md)

