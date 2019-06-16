---
title: Przed migracją kroki migracji danych z bazy danych MongoDB do interfejsu API usługi Azure Cosmos DB, bazy danych mongodb
description: Ten dokument zawiera omówienie wymagań wstępnych dotyczących migracji danych z bazy danych MongoDB do usługi Cosmos DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 476a143555323bbb5058541000a5b1a26d23b71a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61330871"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Przed migracją kroki migracji danych z bazy danych MongoDB do interfejsu API usługi Azure Cosmos DB, bazy danych mongodb

Przed przeprowadzeniem migracji danych z bazy danych MongoDB (lokalnie lub w chmurze (IaaS)) do interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB, należy:

1. [Tworzenie konta usługi Azure Cosmos DB](#create-account)
2. [Oszacować przepływność potrzebne dla swoich obciążeń](#estimate-throughput)
3. [Wybierz klucz optymalne partycji dla danych](#partitioning)
4. [Omówienie zasad indeksowania, którego można ustawić na podstawie danych](#indexing)

Jeśli zostały już wykonane powyższe wymagania wstępne dotyczące migracji, zobacz [danych migracji bazy danych MongoDB do interfejsu API usługi Azure Cosmos DB, bazy danych mongodb](../dms/tutorial-mongodb-cosmos-db.md) instrukcje migracji rzeczywistych danych. W przeciwnym razie ten dokument zawiera instrukcje, aby obsługiwać te wymagania wstępne. 

## <a id="create-account"></a> Tworzenie konta usługi Azure Cosmos DB 

Przed rozpoczęciem procesu migracji, należy [Tworzenie konta usługi Azure Cosmos przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB](create-mongodb-dotnet.md). 

Podczas tworzenia konta można wybrać ustawienia [globalnie Dystrybuuj](distribute-data-globally.md) danych. Istnieje również możliwość zapisu multiregionalne Włącz (lub Konfiguracja wielu wzorców) umożliwiający swoje regionach zarówno zapisu i odczytu z regionu.

![Account-Creation](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a> Oszacować przepływność potrzebę obciążeń

Przed rozpoczęciem migracji za pomocą [Database Migration Service (DMS)](../dms/dms-overview.md), powinien oszacować ilość przepływności do aprowizowania dla usługi Azure Cosmos baz danych i kolekcji.

Przepływność mogą być udostępniane w dowolnym:

- Collection

- Database (Baza danych)

> [!NOTE]
> Może również mieć kombinację powyżej, gdzie niektórych kolekcjach w bazie danych może być wyposażone w dedykowane aprowizowana przepływność i inne osoby mogą udostępniać informacji o przepływności. Aby uzyskać więcej informacji, zobacz [Ustawianie przepływności na bazę danych i kontener](set-throughput.md) strony.
>

Należy najpierw zdecyduj, czy chcesz aprowizować bazy danych lub przepływność na poziomie kolekcji lub jako kombinację obu tych. Ogólnie rzecz biorąc zalecane jest skonfigurowanie dedykowanej przepływności na poziomie kolekcji. Aprowizowania przepływności na poziomie bazy danych umożliwia kolekcji w bazie danych, aby udostępnić aprowizowanej przepływności. Za pomocą udostępnionej przepływności jednak nie ma żadnej gwarancji dla określonych przepływności na każdej poszczególnych kolekcji i przewidywalnej wydajności nie uzyskasz na żadnej określonej kolekcji.

Jeśli nie masz pewności, o ile przepływności powinny być dedykowane do każdej poszczególnych kolekcji, można wybrać przepływność na poziomie bazy danych. Można potraktować aprowizowanej przepływności skonfigurowanym w bazie danych Azure Cosmos jako odpowiednik logiczny, moc obliczeniową bazy danych MongoDB maszyny Wirtualnej lub serwer fizyczny, ale bardziej ekonomiczne rozwiązanie o możliwość elastycznego skalowania. Aby uzyskać więcej informacji, zobacz [Aprowizowanie przepływności na kontenerach Azure Cosmos i bazy danych](set-throughput.md).

Jeśli możesz zaprowizować przepływność na poziomie bazy danych, wszystkie kolekcje utworzone w ramach tej bazy danych musi zostać utworzony przy użyciu klucza partycji/fragmentu. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [partycjonowanie i skalowanie w poziomie w usłudze Azure Cosmos DB](partition-data.md). Jeśli nie określisz klucza partycji/fragmentu podczas migracji, Azure Database Migration Service automatycznie wypełni pola klucza fragmentu, za pomocą *_identyfikator* atrybut, który jest automatycznie generowany dla każdego dokumentu.

### <a name="optimal-number-of-request-units-rus-to-provision"></a>Optymalną liczbę jednostek żądań (ru) do aprowizowania

W usłudze Azure Cosmos DB przepływność jest aprowizowany z wyprzedzeniem i jest mierzona w jednostkach żądań (RU) na sekundę. W przypadku obciążeń korzystających z bazy danych MongoDB na maszynie Wirtualnej lub w środowisku lokalnym traktować RU firmy jako prosty abstrakcji fizycznych zasobów, takich jak dla rozmiaru maszyny Wirtualnej lub -na lokalny serwer i zasoby posiadają, np. pamięci, procesora CPU, operacje We/Wy. 

Inaczej niż w przypadku maszyn wirtualnych lub na serwerach lokalnych (RUS) można łatwo skalować w górę i w dół w dowolnym momencie. Liczba zainicjowanych jednostek zarezerwowanych można zmienić w ciągu kilku sekund, a opłaty są naliczane tylko za maksymalną liczbę jednostek żądań, które zarezerwować dla danego okresu jednej godziny. Aby uzyskać więcej informacji, zobacz [jednostki żądań w usłudze Azure Cosmos DB](request-units.md).

Poniżej przedstawiono kluczowe czynniki, które wpływają na liczbę wymaganych jednostek zarezerwowanych:
- **Rozmiar elementu (tzn. dokument)** : W miarę zwiększania rozmiaru elementu/dokumentu, liczbę jednostek zarezerwowanych używane do odczytu lub zapisu elementu/dokumentu również się zwiększa.
- **Element właściwości liczba**: Zakładając, że [indeksowania domyślne](index-overview.md) wszystkich właściwości, liczbę jednostek zarezerwowanych używane do zapisania elementu zwiększa się wraz ze wzrostem liczby właściwości elementu. Użycie jednostek żądania dla operacji zapisu, można zmniejszyć [ograniczenie liczby właściwości indeksowanych](index-policy.md).
- **Równoczesne wykonywanie operacji**: Żądanie zużywanych jednostek zależy również od częstotliwość, z których różnych operacji CRUD (na przykład operacje zapisu, operacji odczytu, aktualizacji i usuwa) i bardziej złożone zapytania są wykonywane. Możesz użyć [mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/) służący do wypełniania wyjściowego na potrzeby współbieżności bieżących danych bazy danych MongoDB.
- **Zapytanie wzorców**: Złożoność zapytania ma wpływ na liczbę jednostek żądania są używane przez zapytanie.

W przypadku eksportowania plików JSON za pomocą [mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/) i zrozumieć, ile zapisy, operacje odczytu, aktualizowanie i usuwanie tego miejsce na sekundę, można użyć [planisty wydajności usługi Azure Cosmos DB](https://www.documentdb.com/capacityplanner) do szacowania początkowa liczba jednostek żądań do aprowizowania. Capacity planner nie wziąć pod uwagę koszt bardziej złożone zapytania. Dlatego jeśli masz złożone zapytania na danych, będzie możliwe dodatkowych jednostek RU. Kalkulator przyjęto założenie, że wszystkie pola są indeksowane i spójność sesji jest używana. Najlepszym sposobem zrozumienia, koszt zapytania jest przeprowadzenie migracji danych (lub przykładowe dane) do usługi Azure Cosmos DB [nawiązać połączenie z punktu końcowego usługi Cosmos DB](connect-mongodb-account.md) i uruchomić przykładowe zapytanie z poziomu powłoki bazy danych MongoDB przy użyciu `getLastRequestStastistics` polecenie, aby pobrać Opłata za żądanie wyrażana, który zwróci liczbę ru:

`db.runCommand({getLastRequestStatistics: 1})`

To polecenie zwróci dokument JSON podobne do następujących:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Po poznać liczbę ru zużywanych przez zapytanie i współbieżność wymaganych dla tego zapytania, można dostosować Liczba zainicjowanych jednostek zarezerwowanych. Optymalizacja (RUS) nie jest to operacja jednorazowa — należy stale Optymalizuj lub skalować jednostki zarezerwowane aprowizowane w zależności od tego, czy nie ma oczekiwano duży ruch, w przeciwieństwie do duże obciążenie ani importowania danych.

## <a id="partitioning"></a>Wybierz klucz partycji
Partycjonowanie jest kluczowym elementem brany pod uwagę przed migracją na globalnie rozproszonej bazy danych, takich jak usługi Azure Cosmos DB. Usługa Azure Cosmos DB używa partycjonowanie skalowania poszczególnych kontenerów w bazie danych na potrzeby skalowalności i wydajności aplikacji. W przypadku użycia partycjonowania elementów w kontenerze są podzielone na różne podzbiory o nazwie partycjami logicznymi. Aby uzyskać szczegółowe informacje i zalecenia dotyczące wybierania klucza partycji odpowiednie dla Twoich danych, zobacz [wybór klucza partycji sekcji](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Indeksowanie danych
Domyślnie usługa Azure Cosmos DB indeksuje wyświetlania pól danych podczas wprowadzania. Możesz zmodyfikować [zasad indeksowania](index-policy.md) w usłudze Azure Cosmos DB w dowolnym momencie. W rzeczywistości często zaleca się wyłączyć indeksowanie, podczas migracji danych, a następnie włączyć ją ponownie gdy dane są już w usłudze Cosmos DB. Aby uzyskać więcej informacji na temat indeksowania, możesz dowiedzieć się więcej o nim w [indeksowanie w usłudze Azure Cosmos DB](index-overview.md) sekcji. 

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) automatycznie migruje kolekcji bazy danych MongoDB przy użyciu indeksy unikatowe. Jednak indeksy unikatowe należy utworzyć przed rozpoczęciem migracji. Usługa Azure Cosmos DB nie obsługuje tworzenia indeksów unikatowych, gdy istnieje już dane w kolekcji. Aby uzyskać więcej informacji, zobacz [unikatowe klucze w usłudze Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Kolejne kroki
* [Migruj dane MongoDB do usługi Cosmos DB przy użyciu usługi migracji bazy danych.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Aprowizowanie przepływności na kontenerach Azure Cosmos i bazy danych](set-throughput.md)
* [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partition-data.md)
* [Dystrybucja globalna w usłudze Azure Cosmos DB](distribute-data-globally.md)
* [Indeksowanie w usłudze Azure Cosmos DB](index-overview.md)
* [Jednostki żądań w usłudze Azure Cosmos DB](request-units.md)
