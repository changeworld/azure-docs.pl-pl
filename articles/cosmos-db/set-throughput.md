---
title: Aprowizowanie przepływności na kontenerach Azure Cosmos i bazy danych
description: Dowiedz się, jak ustawić zainicjowaną przepływność dla kontenerów i baz danych platformy Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2019
ms.openlocfilehash: 236ae017832d5d613d0bf9fc948d16a7218d2269
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77621944"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Aprowizacja przepływności kontenerów i baz danych

Bazy danych Azure Cosmos jest jednostką zarządzania w usłudze zestaw kontenerów. Bazy danych zawiera zestaw kontenerów niezależnej od schematu. Kontener usługi Azure Cosmos jest jednostką skalowalności, zarówno dla przepływności i magazynu. Kontener w poziomie jest podzielona na partycje w zestawie maszyn w obrębie regionu platformy Azure i są rozproszone we wszystkich regionach platformy Azure skojarzony z Twoim kontem usługi Azure Cosmos.

Za pomocą Azure Cosmos DB można zainicjować przepływność przy dwóch stopniach szczegółowości:
 
- Kontenery usługi Azure Cosmos
- Bazy danych usługi Azure Cosmos

## <a name="set-throughput-on-a-container"></a>Ustawianie przepływności dla kontenera  

Przepływność obsługiwana w kontenerze usługi Azure Cosmos jest zarezerwowana wyłącznie dla tego kontenera. Kontener odbiera aprowizowanej przepływności przez cały czas. Aprowizowana przepływność na kontenerze jest finansowo przez umowy SLA. Aby dowiedzieć się, jak skonfigurować przepływność na kontenerze, zobacz temat [udostępnianie przepływności w kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).

Ustawienie przedziału czasu obsługi administracyjnej w kontenerze to najczęściej używana opcja. Można elastycznie skalować przepływność dla kontenera przez zainicjowanie dowolnej ilości przepływności przy użyciu [jednostek żądań (jednostek ru)](request-units.md). 

Przepływność obsługiwana dla kontenera jest równomiernie dystrybuowana między partycjami fizycznymi, przy założeniu, że dobrym kluczem partycji, który rozdziela partycje logiczne równomiernie między partycjami fizycznymi, przepływność jest również dystrybuowana równomiernie we wszystkich logiczne partycje kontenera. Nie można selektywnie określić przepływności dla partycji logicznych. Ponieważ co najmniej jedna partycja logiczna kontenera jest hostowana przez partycję fizyczną, partycje fizyczne należą wyłącznie do kontenera i obsługują przepływność zainicjowaną na tym kontenerze. 

Jeśli obciążenie uruchomione na partycji logicznej zużywa więcej niż przepływność, która została przypisana do tej partycji logicznej, liczba operacji jest ograniczona. Po wystąpieniu ograniczenia szybkości można zwiększyć zainicjowaną przepływność dla całego kontenera lub wykonać operację ponownie. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [partycje logiczne](partition-data.md).

Zalecamy skonfigurowanie przepływności na poziomie szczegółowości kontenera, gdy potrzebna jest gwarantowana wydajność dla kontenera.

Na poniższej ilustracji przedstawiono, w jaki sposób partycja fizyczna hostuje co najmniej jedną partycję logiczną kontenera:

![Partycja fizyczna](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Ustawianie przepływności dla bazy danych

W przypadku aprowizacji przepływności w bazie danych Azure Cosmos przepływność jest udostępniana dla wszystkich kontenerów (nazywanych kontenerami udostępnionej bazy danych) w bazie danych programu. Wyjątek polega na tym, że określona przepływność została zainicjowana w określonych kontenerach w bazie danych. Udostępnianie Przełożonej przepływności na poziomie bazy danych między kontenerami jest analogiczne do hostowania bazy danych w klastrze maszyn. Ze względu na to, że wszystkie kontenery w ramach bazy danych współużytkują zasoby dostępne na komputerze, nie ma możliwości przewidywalnej wydajności w żadnym konkretnym kontenerze. Aby dowiedzieć się, jak skonfigurować zainicjowaną przepływność dla bazy danych, zobacz [Konfigurowanie aprowizacji przepływności w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).

Ustawienie przepływności w bazie danych usługi Azure Cosmos gwarantuje, że dla tej bazy danych jest odbierana przepustowość. Ponieważ wszystkie kontenery w ramach bazy danych współużytkują przepływność, Azure Cosmos DB nie zapewnia żadnych przewidywalnych gwarancji przepływności dla określonego kontenera w tej bazie danych. Część przepływność, którą może odbierać określonego kontenera jest zależna od:

* Liczba kontenerów.
* Wybór kluczy partycji dla różnych kontenerów.
* Rozkład obciążenia między różne partycje logiczne kontenerów. 

Zalecamy skonfigurowanie przepływności dla bazy danych, jeśli chcesz udostępnić przepływność w wielu kontenerach, ale nie chcesz przeznaczyć przepływności do żadnego konkretnego kontenera. 

W poniższych przykładach pokazano, gdzie preferowany jest udostępnienie przepływności na poziomie bazy danych:

* Udostępnienie przepływności aprowizacji bazy danych w zestawie kontenerów jest przydatne w przypadku aplikacji wielodostępnych. Każdy użytkownik może być reprezentowany przez różne kontenera usługi Azure Cosmos.

* Udostępnianie przeprowadzonej przepływności bazy danych w zestawie kontenerów jest przydatne w przypadku migrowania bazy danych NoSQL, takiej jak MongoDB lub Cassandra, hostowanej w klastrze maszyn wirtualnych lub lokalnych serwerów fizycznych do Azure Cosmos DB. Zastanów się o zainicjowanej przepływności skonfigurowanej w bazie danych Azure Cosmos jako logiczne równoważne, ale tańsze i elastyczne, do tej pojemności obliczeniowej klastra MongoDB lub Cassandra.  

Wszystkie kontenery utworzone w bazie danych z zainicjowaną przepływność muszą zostać utworzone za pomocą [klucza partycji](partition-data.md). W dowolnym momencie przepływność przydzielona do kontenera w ramach bazy danych jest dystrybuowana do wszystkich partycji logicznych tego kontenera. W przypadku kontenerów, które współużytkują zainicjowaną przepływność skonfigurowaną w bazie danych, nie można wybiórczo zastosować przepływności do określonego kontenera lub partycji logicznej. 

Jeśli obciążenie partycji logicznej zużywa więcej niż przepływność przydzieloną do określonej partycji logicznej, operacje są ograniczone proporcjonalnie. W przypadku wystąpienia ograniczenia szybkości można zwiększyć przepływność całej bazy danych lub wykonać operację ponownie. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [partycje logiczne](partition-data.md).

Kontenery w udostępnionej bazie danych przepływności współdzielą przepływność (RU/s) przydzieloną do tej bazy danych. W bazie danych można korzystać z maksymalnie czterech kontenerów z co najmniej 400 RU/s. Każdy nowy kontener po pierwszym czwartym będzie wymagał dodatkowej 100 RU/s. Na przykład jeśli masz udostępnioną bazę danych przepływności z ośmioma kontenerami, minimalna wartość RU/s w bazie danych będzie 800 RU/s.

> [!NOTE]
> W udostępnionej bazie danych przepływności można mieć maksymalnie 25 kontenerów w bazie danych. Jeśli masz już więcej niż 25 kontenerów w udostępnionej bazie danych przepływności, nie będzie można tworzyć dodatkowych kontenerów, dopóki liczba kontenerów nie będzie mniejsza niż 25.

Jeśli Twoje obciążenia wymagają usunięcia i ponownego utworzenia wszystkich kolekcji w bazie danych, zaleca się porzucenie pustej bazy danych i ponowne utworzenie nowej bazy danych przed utworzeniem kolekcji. Na poniższej ilustracji przedstawiono, w jaki sposób partycja fizyczna może hostować co najmniej jedną partycję logiczną, która należy do różnych kontenerów w bazie danych:

![Partycja fizyczna](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Ustawianie przepływności dla bazy danych i kontenera

Można połączyć te dwa modele. Przepływność aprowizacji zarówno dla bazy danych, jak i kontenera jest dozwolona. Poniższy przykład pokazuje, jak aprowizować przepływność mierzoną w bazie danych Azure Cosmos i kontener:

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
|Minimalny (RUS) |400 (po pierwszych czterech kontenerach każdy dodatkowy kontener wymaga co najmniej 100 jednostek ru na sekundę). |400|
|Minimalna jednostek żądań na kontener|100|400|
|Maksymalna (RUS)|Bez ograniczeń, w bazie danych.|Bez ograniczeń, w kontenerze.|
|Jednostek ru przypisane lub dostępne dla określonego kontenera|Nie gwarancji. Jednostek ru przypisane do danego kontenera zależą od właściwości. Właściwościami mogą być wybór kluczy partycji kontenerów, które współdzielą przepływność, rozkład obciążenia oraz liczbę kontenerów. |Wszystkie jednostki zarezerwowane skonfigurowane w kontenerze są przeznaczone wyłącznie do kontenera.|
|Maksymalny rozmiar magazynu dla kontenera|Nieograniczona liczba.|Nieograniczona liczba.|
|Maksymalna przepływność na partycji logicznej kontenera|10K (RUS)|10K (RUS)|
|Maksymalna pojemność (danych + indeksu) w jednej partycji logicznej kontenera|20 GB|20 GB|

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [partycji logicznych](partition-data.md).
* Dowiedz się, jak [zainicjować przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* Dowiedz się, jak [udostępnić przepływność w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).

