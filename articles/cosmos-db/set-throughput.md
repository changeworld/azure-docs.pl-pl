---
title: Obsługa przepływności w kontenerach i bazach danych platformy Azure Cosmos
description: Dowiedz się, jak ustawić zainicjowaną przepływność dla kontenerów i baz danych platformy Azure Cosmos.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: rimman
ms.openlocfilehash: 0f906cc6b62252d8d94498ed22e22a5cab12765e
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298353"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Aprowizacja przepływności kontenerów i baz danych

Baza danych usługi Azure Cosmos jest jednostką zarządzania dla zestawu kontenerów. Baza danych składa się z zestawu kontenerów schematów niezależny od. Kontenerem usługi Azure Cosmos jest jednostka skalowalności zarówno dla przepływności, jak i magazynu. Kontener jest podzielony w poziomie na zestaw maszyn w regionie świadczenia usługi Azure i dystrybuowany do wszystkich regionów świadczenia usługi Azure skojarzonych z Twoim kontem usługi Azure Cosmos.

Za pomocą Azure Cosmos DB można zainicjować przepływność przy dwóch stopniach szczegółowości:
 
- Kontenery usługi Azure Cosmos
- Bazy danych Azure Cosmos

## <a name="set-throughput-on-a-container"></a>Ustawianie przepływności dla kontenera  

Przepływność obsługiwana w kontenerze usługi Azure Cosmos jest zarezerwowana wyłącznie dla tego kontenera. Kontener otrzymuje zainicjowaną przepływność przez cały czas. Tymczasowa przepływność na kontenerze jest obsługiwana finansowo przez umowy SLA. Aby dowiedzieć się, jak skonfigurować przepływność na kontenerze, zobacz temat [udostępnianie przepływności w kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).

Ustawienie przedziału czasu obsługi administracyjnej w kontenerze to najczęściej używana opcja. Można elastycznie skalować przepływność dla kontenera przez zainicjowanie dowolnej ilości przepływności przy użyciu [jednostek żądań (jednostek ru)](request-units.md). 

Przepływność obsługiwana w kontenerze usługi Azure Cosmos jest równomiernie rozłożona na wszystkie partycje logiczne kontenera. Nie można selektywnie określić przepływności dla partycji logicznych. Ponieważ co najmniej jedna partycja logiczna kontenera jest hostowana przez partycję fizyczną, partycje fizyczne należą wyłącznie do kontenera i obsługują przepływność zainicjowaną na tym kontenerze. 

Jeśli obciążenie uruchomione na partycji logicznej zużywa więcej niż przepływność, która została przypisana do tej partycji logicznej, liczba operacji jest ograniczona. Po wystąpieniu ograniczenia szybkości można zwiększyć zainicjowaną przepływność dla całego kontenera lub wykonać operację ponownie. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [partycje logiczne](partition-data.md).

Zalecamy skonfigurowanie przepływności na poziomie szczegółowości kontenera, gdy potrzebna jest gwarantowana wydajność dla kontenera.

Na poniższej ilustracji przedstawiono, w jaki sposób partycja fizyczna hostuje co najmniej jedną partycję logiczną kontenera:

![Partycja fizyczna](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Ustawianie przepływności dla bazy danych

W przypadku aprowizacji przepływności w bazie danych Azure Cosmos przepływność jest udostępniana dla wszystkich kontenerów (nazywanych kontenerami udostępnionej bazy danych) w bazie danych programu. Wyjątek polega na tym, że określona przepływność została zainicjowana w określonych kontenerach w bazie danych. Udostępnianie Przełożonej przepływności na poziomie bazy danych między kontenerami jest analogiczne do hostowania bazy danych w klastrze maszyn. Ze względu na to, że wszystkie kontenery w ramach bazy danych współużytkują zasoby dostępne na komputerze, nie ma możliwości przewidywalnej wydajności w żadnym konkretnym kontenerze. Aby dowiedzieć się, jak skonfigurować zainicjowaną przepływność dla bazy danych, zobacz [Konfigurowanie aprowizacji przepływności w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).

Ustawienie przepływności w bazie danych usługi Azure Cosmos gwarantuje, że dla tej bazy danych jest odbierana przepustowość. Ponieważ wszystkie kontenery w ramach bazy danych współużytkują przepływność, Azure Cosmos DB nie zapewnia żadnych przewidywalnych gwarancji przepływności dla określonego kontenera w tej bazie danych. Część przepływności, do której może odbierać określony kontener, zależy od:

* Liczba kontenerów.
* Wybór kluczy partycji dla różnych kontenerów.
* Rozkład obciążenia między różnymi partycjami logicznymi kontenerów. 

Zalecamy skonfigurowanie przepływności dla bazy danych, jeśli chcesz udostępnić przepływność w wielu kontenerach, ale nie chcesz przeznaczyć przepływności do żadnego konkretnego kontenera. 

W poniższych przykładach pokazano, gdzie preferowany jest udostępnienie przepływności na poziomie bazy danych:

* Udostępnienie przepływności aprowizacji bazy danych w zestawie kontenerów jest przydatne w przypadku aplikacji wielodostępnych. Każdy użytkownik może być reprezentowany przez odrębny kontener usługi Azure Cosmos.

* Udostępnianie przeprowadzonej przepływności bazy danych w zestawie kontenerów jest przydatne w przypadku migrowania bazy danych NoSQL, takiej jak MongoDB lub Cassandra, hostowanej w klastrze maszyn wirtualnych lub lokalnych serwerów fizycznych do Azure Cosmos DB. Zastanów się o zainicjowanej przepływności skonfigurowanej w bazie danych Azure Cosmos jako logiczne równoważne, ale tańsze i elastyczne, do tej pojemności obliczeniowej klastra MongoDB lub Cassandra.  

Wszystkie kontenery utworzone w bazie danych z zainicjowaną przepływność muszą zostać utworzone za pomocą [klucza partycji](partition-data.md). W dowolnym momencie przepływność przydzielona do kontenera w ramach bazy danych jest dystrybuowana do wszystkich partycji logicznych tego kontenera. W przypadku kontenerów, które współużytkują zainicjowaną przepływność skonfigurowaną w bazie danych, nie można wybiórczo zastosować przepływności do określonego kontenera lub partycji logicznej. 

Jeśli obciążenie partycji logicznej zużywa więcej niż przepływność przydzieloną do określonej partycji logicznej, operacje są ograniczone proporcjonalnie. W przypadku wystąpienia ograniczenia szybkości można zwiększyć przepływność całej bazy danych lub wykonać operację ponownie. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [partycje logiczne](partition-data.md).

Przepływność obsługiwana w bazie danych może być współużytkowana przez kontenery w tej bazie danych. Każdy nowy kontener w udostępnionej przepływności na poziomie bazy danych będzie wymagał 100 RU/s. W przypadku udostępniania kontenerów za pomocą oferty udostępnionej bazy danych:

* Każde 25 kontenerów jest pogrupowane w zestaw partycji, a przepływność bazy danych (D) jest współdzielona między kontenerami w zestawie partycji. Jeśli istnieje do 25 kontenerów w bazie danych i w dowolnym momencie, jeśli używasz tylko jednego kontenera, ten kontener może korzystać z maksymalnej przepływności ".

* Dla każdego nowego kontenera utworzonego po 25 kontenerach tworzony jest nowy zestaw partycji, a przepływność bazy danych jest dzielona między nowo utworzonymi zestawami partycji (czyli D/2 dla 2 zestawów partycji, D/3 dla 3 zestawów partycji...). W dowolnym momencie, jeśli używasz tylko jednego kontenera z bazy danych, można użyć maksimum (D/2, d/3, D/4... przepływność) odpowiednio. W przypadku zmniejszonej przepływności zalecamy utworzenie nie więcej niż 25 kontenerów w jednej bazie danych.

**Przykład**

* Jeśli utworzysz bazę danych o nazwie "mojabazadanych" z zainicjowaną przepływność wynoszącą 10 000 jednostek RU/s.

* W przypadku udostępniania 25 kontenerów w ramach "mojabazadanych" Wszystkie kontenery są pogrupowane w zestaw partycji. W dowolnym momencie, jeśli używasz tylko jednego kontenera z bazy danych, można użyć maksymalnie 10 000 jednostek RU/s (D).

* W przypadku udostępniania 26-kontenera tworzony jest nowy zestaw partycji, a przepływność jest równomiernie dzielona między zestawami partycji. Tak więc w dowolnym momencie, jeśli używasz tylko jednego kontenera z bazy danych, można użyć maksymalnie 5 K RU/s (D/2). Ponieważ istnieją dwa zestawy partycji, współczynnik udostępniania przepływności jest podzielony na D/2.

   Poniższa ilustracja przedstawia poprzedni przykład graficzny:

   ![Współczynnik udostępniania w przepływności na poziomie bazy danych](./media/set-throughput/database-level-throughput-shareability-factor.png)


Jeśli Twoje obciążenia wymagają usunięcia i ponownego utworzenia wszystkich kolekcji w bazie danych, zaleca się porzucenie pustej bazy danych i ponowne utworzenie nowej bazy danych przed utworzeniem kolekcji. Na poniższej ilustracji przedstawiono, w jaki sposób partycja fizyczna może hostować co najmniej jedną partycję logiczną, która należy do różnych kontenerów w bazie danych:

![Partycja fizyczna](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Ustawianie przepływności dla bazy danych i kontenera

Można połączyć te dwa modele. Przepływność aprowizacji zarówno dla bazy danych, jak i kontenera jest dozwolona. Poniższy przykład pokazuje, jak zainicjować przepływność dla bazy danych Azure Cosmos i kontenera:

* Można utworzyć bazę danych usługi Azure Cosmos o nazwie *z* z zainicjowaną przepływność *"K"* jednostek ru. 
* Następnie utwórz pięć kontenerów o nazwie *a*, *B*, *C*, *D*i *E* w ramach bazy danych. Podczas tworzenia kontenera B upewnij się, że włączono **dedykowaną przepływność dla tej opcji kontenera** , a następnie jawnie Skonfiguruj *"P"* jednostek ru na potrzeby aprowizacji dla tego kontenera. Należy pamiętać, że w przypadku tworzenia bazy danych i kontenera można skonfigurować udostępnioną i dedykowaną przepływność. 

   ![Ustawianie przepływności na poziomie kontenera](./media/set-throughput/coll-level-throughput.png)

* Przepływność jednostek ru *"K"* jest udostępniana w czterech kontenerach *a*, *C*, *D*i *E*. Dokładna ilość przepływności *dostępna dla,* *C*, *D*lub *E* jest różna. Dla każdego z przepływności poszczególnych kontenerów nie ma umowy SLA.
* Kontener o nazwie *B* jest zagwarantowany do uzyskania jednostek ru przepływności *"P"* przez cały czas. Jest ona obsługiwana przez umowy SLA.

> [!NOTE]
> Nie można przekonwertować kontenera z zainicjowaną przepływność na kontener udostępnionej bazy danych. Nie można przekształcić kontenera udostępnionej bazy danych w celu uzyskania dedykowanej przepływności.

## <a name="update-throughput-on-a-database-or-a-container"></a>Aktualizowanie przepływności dla bazy danych lub kontenera

Po utworzeniu kontenera usługi Azure Cosmos lub bazy danych można zaktualizować zainicjowaną przepływność. Nie ma żadnego limitu maksymalnej alokowanej przepływności, którą można skonfigurować dla bazy danych lub kontenera. Minimalna zainicjowana przepływność zależy od następujących czynników: 

* Maksymalny rozmiar danych, który został kiedykolwiek przechowywany w kontenerze
* Maksymalna przepływność, która została kiedykolwiek zainicjowana w kontenerze
* Maksymalna liczba kontenerów usługi Azure Cosmos, które kiedykolwiek tworzysz w bazie danych o udostępnionej przepływności. 

Minimalną przepływność kontenera lub bazy danych można pobrać programowo przy użyciu zestawów SDK lub wyświetlić wartość w Azure Portal. W przypadku korzystania z zestawu .NET SDK Metoda [DocumentClient. ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) umożliwia skalowanie wartości przepływności. W przypadku korzystania z zestawu SDK języka Java [RequestOptions. setOfferThroughput](sql-api-java-samples.md#offer-examples) Metoda pozwala skalować wartość przepływności. 

W przypadku korzystania z zestawu .NET SDK Metoda [DocumentClient. ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) umożliwia pobieranie minimalnej przepływności kontenera lub bazy danych. 

W dowolnym momencie można skalować zainicjowaną przepływność kontenera lub bazy danych. Po wykonaniu operacji skalowania w celu zwiększenia przepływności może upłynąć dłuższy czas, ponieważ zadania systemowe zainicjują wymagane zasoby. Stan operacji skalowania można sprawdzić w Azure Portal lub programowo przy użyciu zestawów SDK. Korzystając z zestawu SDK platformy .NET, można uzyskać stan operacji skalowania za pomocą metody `DocumentClient.ReadOfferAsync`.

## <a name="comparison-of-models"></a>Porównanie modeli

|**Konstruktora**  |**Przepływność obsługiwana w bazie danych**  |**Przepływność obsługiwana w kontenerze**|
|---------|---------|---------|
|Minimalna jednostek ru |400 (po pierwszych czterech kontenerach każdy dodatkowy kontener wymaga co najmniej 100 jednostek ru na sekundę). |400|
|Minimalna jednostek ru na kontener|100|400|
|Maksymalna jednostek ru|Bez ograniczeń, w bazie danych.|Bez ograniczeń, w kontenerze.|
|Jednostek ru przypisane lub dostępne dla określonego kontenera|Brak gwarancji. Jednostek ru przypisane do danego kontenera zależą od właściwości. Właściwościami mogą być wybór kluczy partycji kontenerów, które współdzielą przepływność, rozkład obciążenia oraz liczbę kontenerów. |Wszystkie jednostek ru skonfigurowane w kontenerze są zarezerwowane wyłącznie dla kontenera.|
|Maksymalna ilość miejsca w magazynie dla kontenera|Ograniczona.|Ograniczona.|
|Maksymalna przepływność na partycję logiczną kontenera|10 000 jednostek ru|10 000 jednostek ru|
|Maksymalna ilość miejsca (Data + indeks) na partycję logiczną kontenera|10 GB|10 GB|

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [partycji logicznych](partition-data.md).
* Dowiedz się, jak [zainicjować przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* Dowiedz się, jak [udostępnić przepływność w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).

