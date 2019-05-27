---
title: Ograniczenia w usłudze Azure Cosmos DB
description: W tym artykule opisano limity w usłudze Azure Cosmos DB.
author: arramac
ms.author: arramac
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2019
ms.openlocfilehash: 0086327661df637dc0ae60208ed9424b4610ef0e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969495"
---
# <a name="limits-in-azure-cosmos-db"></a>Ograniczenia w usłudze Azure Cosmos DB

Ten artykuł zawiera omówienie limitów w usłudze Azure Cosmos DB.

## <a name="storage-and-throughput"></a>Magazynu i przepływności

Po utworzeniu konta usługi Azure Cosmos w ramach Twojej subskrypcji, możesz zarządzać danymi w ramach Twojego konta przez [Tworzenie baz danych, kontenerów i elementy](databases-containers-items.md). Możesz aprowizować przepływność na poziomie kontenera lub na poziomie bazy danych pod względem [jednostki (RU/s lub RUs) żądania](request-units.md). W poniższej tabeli wymieniono limity dla magazynu i przepływność na kontenerze/bazy danych.

| Resource | Limit domyślny |
| --- | --- |
| Maksymalne jednostek żądań na kontener ([dedykowana przepływność aprowizowana tryb](databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 domyślnie. Możesz zwiększyć jej [wypełniając bilet pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) lub skontaktować się z nami za pomocą [poproś Cosmos DB](mailto:askcosmosdb@microsoft.com) |
| Maksymalna (RUS) dla bazy danych ([trybie aprowizowanej przepływności udostępnionego](databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 domyślnie. Możesz zwiększyć jej [wypełniając bilet pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) lub skontaktować się z nami za pomocą [poproś Cosmos DB](mailto:askcosmosdb@microsoft.com) |
| Maksymalne jednostek żądania na klucz partycji (logiczny) | 10 000 |
| Maksymalna miejsca do magazynowania we wszystkich elementów na klucz partycji (logiczny)| 10 GB |
| Maksymalna liczba kluczy w oddzielnej partycji (logiczny) | Nieograniczone |
| Maksymalna pojemność jednej kontenera | Nieograniczone |
| Maksymalna pojemność jednej bazy danych | Nieograniczone |

> [!NOTE]
> Najlepsze rozwiązania dotyczące obciążeń, które mają klucze partycji, które wymagają wyższych limitów w celu przechowywania lub przepływność, zobacz [projektowanie pod kątem klawisze partycji](synthetic-partition-keys.md)
>

Kontener Cosmos (lub bazy danych z udostępnionej przepływności) musi mieć minimalną przepływność z 400 jednostek żądania. Wraz z rozwojem kontenera minimalna obsługiwana przepływność również zależy od następujących czynników:

* Maksymalny rozmiar magazynu, które są używane w kontenerze jest mierzony w przyrostach 40 jednostek żądania na GB użytego miejsca do magazynowania. Na przykład jeśli kontener zawiera 100 GB danych, przepływność musi być co najmniej 4000 jednostek żądań
* Maksymalna przepływność nigdy nie zainicjowano obsługi administracyjnej w kontenerze. Usługa obsługuje obniżenie przepływności kontenera do 10% aprowizowanego maksymalnej. Na przykład jeśli przepływność został zwiększony do 10000 (RUS), najniższe możliwe aprowizowanej przepływności będzie 1000 jednostek żądań
* Całkowita liczba kontenerów, które nigdy nie utworzono w bazie danych udostępnionych przepływność jest mierzona w 100 jednostek żądań na kontener. Na przykład jeśli utworzono pięć kontenerów w bazie danych udostępnionej przepływności przepływność musi być co najmniej 500 (RUS)

Bieżące i minimalna przepływność kontenera lub bazy danych mogą być pobierane z witryny Azure portal lub zestawów SDK. Aby uzyskać więcej informacji, zobacz [Aprowizowanie przepływności na kontenerach i baz danych](set-throughput.md). W podsumowaniu poniżej przedstawiono minimalne elastycznie ograniczenia jednostek RU. 

| Resource | Limit domyślny |
| --- | --- |
| Minimalna jednostek żądań na kontener ([dedykowana przepływność aprowizowana tryb](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Minimalny (RUS) dla bazy danych ([trybie aprowizowanej przepływności udostępnionego](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Minimalna jednostek żądań na kontener w ramach udostępnionej przepływności bazy danych | 100 |
| Minimalna RUs za GB użytego miejsca do magazynowania | 40 |

Usługa cosmos DB obsługuje elastyczne skalowanie przepływności (ru) na kontener lub bazy danych za pośrednictwem zestawów SDK lub portalu. Każdy kontener można skalować synchronicznie i od razu w zakresie skalowania od 10 do 100 razy między minimalną i maksymalną. Jeśli wartość żądanej przepływności znajduje się poza zakresem, skalowanie jest wykonywane asynchronicznie. Skalowanie asynchronicznej może potrwać do godziny w zależności od żądanej przepływności i rozmiaru magazynu danych w kontenerze.  

## <a name="control-plane-operations"></a>Operacje warstwy kontroli

Możesz [obsługi administracyjnej i zarządzanie kontem usługi Azure Cosmos](how-to-manage-database-account.md) przy użyciu witryny Azure portal, programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure i szablonów usługi Azure Resource Manager. W poniższej tabeli wymieniono limity dla subskrypcji, kont i liczbę operacji.

| Resource | Limit domyślny |
| --- | --- |
| Konta maksymalna bazy danych na subskrypcję | 50 domyślnie. Możesz zwiększyć jej [wypełniając bilet pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) lub skontaktować się z nami za pomocą [poproś Cosmos DB](mailto:askcosmosdb@microsoft.com)|
| Maksymalna liczba wywoływania regionalnego trybu failover | 1/godz. domyślnie. Możesz zwiększyć jej [wypełniając bilet pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) lub skontaktować się z nami za pomocą [poproś Cosmos DB](mailto:askcosmosdb@microsoft.com)|

> [!NOTE]
> Wywoływania regionalnego trybu failover dotyczą tylko konta zapisu w jednym regionie. Konta zapisu w wielu regionach nie wymagają ani nie ma żadnych ograniczeń dotyczących zmiany regionu zapisu.

Usługa cosmos DB automatycznie wykonuje kopie zapasowe danych w regularnych odstępach czasu. Aby uzyskać szczegółowe informacje w odstępach czasu przechowywania kopii zapasowych i windows, zobacz [kopii zapasowej Online i danych na żądanie przywrócenia w usłudze Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-container-limits"></a>Limity dla kontenera

Zależności od tego, w których możesz użyć interfejsu API kontener usługi Azure Cosmos kolekcję, tabeli lub wykresu. Kontenery obsługują konfiguracje [unikatowych ograniczeń klucza](unique-keys.md), [procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika](stored-procedures-triggers-udfs.md), i [zasad indeksowania](how-to-manage-indexing-policy.md). W poniższej tabeli wymieniono limity specyficzne dla konfiguracji w ramach kontenera. 

| Resource | Limit domyślny |
| --- | --- |
| Maksymalna długość nazwy bazy danych lub kontenera | 255 |
| Maksymalna procedur składowanych na kontener | 100 <sup>*</sup>|
| Maksymalna funkcje zdefiniowane przez użytkownika na kontener | 25 <sup>*</sup>|
| Maksymalna liczba ścieżek w zasad indeksowania| 100 <sup>*</sup>|
| Maksymalna liczba kluczy unikatowych na kontener|10 <sup>*</sup>|
| Maksymalna liczba ścieżek na unikatowego ograniczenia klucza|16 <sup>*</sup>|

<sup>*</sup> Dowolne z tych limitów-container można zwiększyć, kontaktując się z pomocą techniczną platformy Azure, lub skontaktować się z nami za pomocą [poproś Cosmos DB](mailto:askcosmosdb@microsoft.com).

## <a name="per-item-limits"></a>Ograniczenia elementu

Zależności od tego, w których możesz użyć interfejsu API element Azure Cosmos może reprezentować albo dokumentów w kolekcji, wiersz w tabeli, lub węzła lub krawędzi na wykresie. W poniższej tabeli przedstawiono limity dla elementu w usłudze Cosmos DB. 

| Resource | Limit domyślny |
| --- | --- |
| Maksymalny rozmiar elementu | 2 MB (UTF-8 długość reprezentacji JSON) |
| Maksymalna długość wartości klucza partycji | 2048 bajtów |
| Maksymalna długość wartości identyfikatora | 1024 bajty |
| Maksymalna liczba właściwości dla elementu | Brak limitu praktyczne |
| Maksymalna głębokość zagnieżdżenia | Brak limitu praktyczne |
| Maksymalna długość nazwy właściwości | Brak limitu praktyczne |
| Maksymalna długość wartości właściwości | Brak limitu praktyczne |
| Maksymalna długość ciągu wartości właściwości | Brak limitu praktyczne |
| Maksymalna długość wartości właściwości liczbowe | Ieee754 o podwójnej precyzji 64-bitowych |

Nie ma żadnych ograniczeń dotyczących ładunków elementów takich jak numer głębokości właściwości i zagnieżdżenia, z wyjątkiem ograniczenia długości klucza partycji i identyfikatora wartości i ogólny rozmiar ograniczenie 2 MB. Należy skonfigurować zasady indeksowania dla kontenerów z elementu dużych lub złożonych struktur zmniejszanie zużycia jednostek. Zobacz [modelowania elementów w usłudze Cosmos DB](how-to-model-partition-example.md) na przykład rzeczywistych i wzorców w celu zarządzania elementami dużych.

## <a name="per-request-limits"></a>Limity żądań

Usługa cosmos DB obsługuje [operacji CRUD i zapytania](https://docs.microsoft.com/rest/api/cosmos-db/) względem zasobów, takich jak kontenery, elementy i baz danych.  

| Resource | Limit domyślny |
| --- | --- |
| Maksymalny czas wykonywania dla pojedynczej operacji (np. wykonywanie procedury składowanej lub pobierania strony pojedynczego zapytania)| 5 s |
| Rozmiar maksymalny żądania (procedury składowanej, CRUD)| 2 MB |
| Maksymalny rozmiar odpowiedzi (na przykład, zapytanie z podziałem na strony) | 4 MB |

Po operacji takich jak kwerenda osiągnie limit rozmiaru limitu czasu lub odpowiedzi wykonywania, zwraca stronę wyników i token kontynuacji do klienta, aby wznowić wykonywanie. Nie ma żadnych praktyczne limitu na czas, przez jaki pojedynczego zapytania można uruchamiać na stronach/kontynuacji.

Usługa cosmos DB używa HMAC na potrzeby autoryzacji. Można użyć z klucza głównego, lub [tokenów zasobów](secure-access-to-data.md) kontroli dostępu w zakresie do zasobów takich jak kontenery, partycje kluczy lub elementów. Poniższa tabela zawiera listę limitów dotyczących tokenach autoryzacji w usłudze Cosmos DB.

| Resource | Limit domyślny |
| --- | --- |
| Maksymalny wzorca tokenu czas wygaśnięcia | 15 min  |
| Czas wygaśnięcia tokenu minimalne zasobu | 10 min  |
| Czas wygaśnięcia tokenu zasobu Maksymalna | 24 godziny domyślnie. Możesz zwiększyć jej [wypełniając bilet pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) lub skontaktować się z nami za pomocą [poproś Cosmos DB](mailto:askcosmosdb@microsoft.com)|
| Maksymalna zegara token autoryzacji| 15 min |

Usługa cosmos DB obsługuje wykonywanie wyzwalaczy podczas zapisu. Usługa obsługuje maksymalnie jeden wyzwalacz wstępnej i jeden wyzwalacz po operacji zapisu. 

## <a name="sql-query-limits"></a>Limity zapytania SQL

Usługa cosmos DB obsługuje wykonywanie zapytań za pomocą elementów [SQL](how-to-sql-query.md). W poniższej tabeli opisano ograniczenia określone w instrukcji zapytań, na przykład pod względem liczby klauzul lub długość zapytania.

| Resource | Limit domyślny |
| --- | --- |
| Maksymalna długość zapytania SQL| 256 KB <sup>*</sup>|
| Maksymalna sprzężeń na zapytanie| 5 <sup>*</sup>|
| Maksymalna ANDs na zapytanie| 2000 <sup>*</sup>|
| Maksymalna ORs na zapytanie| 2000 <sup>*</sup>|
| Maksymalna funkcje zdefiniowane przez użytkownika na zapytanie| 10 <sup>*</sup>|
| Maksymalną liczbą argumentów w wyrażeniu| 6000 <sup>*</sup>|
| Maksymalna liczba punktów na Wielokąt| 4096 <sup>*</sup>|

<sup>*</sup> Żadnego z tych limitów zapytań SQL można zwiększyć, kontaktując się z pomocą techniczną platformy Azure, lub skontaktować się z nami za pomocą [poproś Cosmos DB](mailto:askcosmosdb@microsoft.com).

## <a name="mongodb-api-specific-limits"></a>Limity specyficzne dla interfejsu API usługi MongoDB

Usługa cosmos DB obsługuje protokół przewodowy MongoDB dla aplikacji napisanych dla bazy danych MongoDB. Możesz znaleźć obsługiwanych poleceń i protokołu wersji w [bazy danych MongoDB z obsługiwanych funkcji i składni](mongodb-feature-support.md).

W poniższej tabeli wymieniono limity dotyczące obsługi różnych funkcji bazy danych MongoDB. Inne limity usługi wymienionych dla programu SQL (rdzenie) interfejsu API, która jest również zastosowanie do interfejsu API usługi MongoDB.

| Resource | Limit domyślny |
| --- | --- |
| Maksymalny rozmiar pamięci kwerendy bazy danych MongoDB | 40 MB |
| Maksymalny czas wykonywania operacji bazy danych MongoDB| 30 sekund |

## <a name="try-cosmos-db-free-limits"></a>Spróbuj Cosmos DB bezpłatnych limitów

W poniższej tabeli przedstawiono limity dotyczące [Wypróbuj bezpłatnie usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) wersji próbnej.

| Resource | Limit domyślny |
| --- | --- |
| Podczas okresu próbnego | 30 dni (mogą być odnawiane dowolną liczbę razy) |
| Maksymalna liczba kontenerów w jednej subskrypcji (interfejs API tabeli SQL, Gremlin) | 1 |
| Maksymalna liczba kontenerów w jednej subskrypcji (interfejs API usługi MongoDB) | 3 |
| Maksymalna przepływność danego kontenera | 5000 |
| Maksymalna przepływność na udostępniony przepływności bazy danych | 20000 |
| Maksymalna całkowita ilość miejsca na konto | 10 GB |

Wypróbuj usługi Cosmos DB obsługuje dystrybucji globalnej tylko regiony środkowe stany USA, Europa Północna i Azja południowo-wschodnia. Nie można utworzyć biletów pomocy technicznej platformy Azure dla kont Wypróbuj usługę Azure Cosmos DB. Jednak obsługiwane dla subskrybentów przy użyciu istniejących planów pomocy technicznej.

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat usługi Cosmos DB podstawowe pojęcia dotyczące [globalną dystrybucję](distribute-data-globally.md) i [partycjonowanie](partitioning-overview.md) i [aprowizowanej przepływności](request-units.md).

Rozpocznij pracę z usługą Azure Cosmos DB, korzystając z jednego z naszych przewodników:

* [Rozpoczynanie pracy z interfejsem SQL usługi Azure Cosmos DB](create-sql-api-dotnet.md)
* [Rozpoczynanie pracy z interfejsem MongoDB usługi Azure Cosmos DB](create-mongodb-nodejs.md)
* [Rozpoczynanie pracy z interfejsem API rozwiązania Cassandra usługi Azure Cosmos DB](create-cassandra-dotnet.md)
* [Rozpoczynanie pracy z interfejsem API programu Gremlin usługi Azure Cosmos DB](create-graph-dotnet.md)
* [Rozpoczynanie pracy z interfejsem API tabeli usługi Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Wypróbuj usługę Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/)
