---
title: Kroki poprzedzające migrację migracji danych do interfejsu API Azure Cosmos DB MongoDB
description: Ten dokument zawiera omówienie wymagań wstępnych dotyczących migracji danych z programu MongoDB do Cosmos DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 4dc7038d0ff5180f15a43268fd3f3aa0cbb0c7a0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445200"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Kroki poprzedzające migrację dla migracji danych z MongoDB do interfejsu API Azure Cosmos DB dla MongoDB

Przed przeprowadzeniem migracji danych z MongoDB (lokalnie lub w chmurze) do Azure Cosmos DB interfejsu API dla MongoDB, należy:

1. [Tworzenie konta usługi Azure Cosmos DB](#create-account)
2. [Oszacowanie przepływności wymaganej dla obciążeń](#estimate-throughput)
3. [Wybierz optymalny klucz partycji dla danych](#partitioning)
4. [Informacje na temat zasad indeksowania, które można ustawić dla danych](#indexing)

Jeśli zostały już wykonane powyższe wymagania wstępne dotyczące migracji, zapoznaj się z instrukcją [Migrowanie danych MongoDB Azure Cosmos DB do interfejsu API usługi MongoDB](../dms/tutorial-mongodb-cosmos-db.md) , aby poznać rzeczywiste instrukcje dotyczące migracji danych. W przeciwnym razie ten dokument zawiera instrukcje obsługi tych wymagań wstępnych. 

## <a id="create-account"></a>Utwórz konto Azure Cosmos DB 

Przed rozpoczęciem migracji należy [utworzyć konto usługi Azure Cosmos za pomocą interfejsu API Azure Cosmos DB dla MongoDB](create-mongodb-dotnet.md). 

Przy tworzeniu konta możesz wybrać opcję Ustawienia do [dystrybuowania danych globalnie](distribute-data-globally.md) . Dostępna jest również opcja włączenia zapisu w wielu regionach (lub konfiguracji z wieloma wzorcami), która umożliwia każdemu regionowi zarówno zapis, jak i odczyt.

![Tworzenie konta](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a>Oszacowanie potrzeby przepływności dla obciążeń

Przed rozpoczęciem migracji przy użyciu [Database Migration Service (DMS)](../dms/dms-overview.md)należy oszacować ilość przepływności do aprowizacji dla baz danych i kolekcji usługi Azure Cosmos.

Obsługa przepływności może być inicjowana na jeden z tych:

- Collection

- baza danych

> [!NOTE]
> Istnieje również możliwość połączenia powyższych elementów, w przypadku których niektóre kolekcje w bazie danych mogą mieć dedykowaną przepływność, a inne mogą korzystać z przepływności. Szczegółowe informacje można znaleźć na stronie [Ustawianie przepływności na bazie danych i kontenera](set-throughput.md) .
>

Najpierw należy zdecydować, czy chcesz zainicjować obsługę przepływności bazy danych lub kolekcji, czy też kombinację obu tych elementów. Ogólnie rzecz biorąc, zaleca się skonfigurowanie dedykowanej przepływności na poziomie kolekcji. Przepływność aprowizacji na poziomie bazy danych umożliwia zbieranie danych w ramach udostępnionej przepływności. W przypadku udostępnionej przepływności nie ma jednak gwarancji dla określonej przepływności dla każdej kolekcji i nie jest możliwe przewidywalna wydajność w żadnej określonej kolekcji.

Jeśli nie masz pewności, jaka ilość przepływności powinna być dedykowana dla każdej z nich, możesz wybrać przepływność na poziomie bazy danych. Zainicjowaną przepływność skonfigurowaną w bazie danych usługi Azure Cosmos można traktować jako logiczną równoważną wydajności obliczeniowej maszyny wirtualnej MongoDB lub serwera fizycznego, ale bardziej ekonomiczną i elastycznie skalować. Aby uzyskać więcej informacji, zobacz temat [Obsługa przepływności w kontenerach i bazach danych platformy Azure Cosmos](set-throughput.md).

Jeśli zainicjujesz przepływność na poziomie bazy danych, wszystkie kolekcje utworzone w tej bazie danych muszą zostać utworzone za pomocą klucza partycji/fragmentu. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [partycjonowanie i skalowanie w poziomie w Azure Cosmos DB](partition-data.md). Jeśli nie określisz klucza partycji/fragmentu podczas migracji, Azure Database Migration Service automatycznie wypełni pole klucza fragmentu z atrybutem *_id* , który jest generowany automatycznie dla każdego dokumentu.

### <a name="optimal-number-of-request-units-rus-to-provision"></a>Optymalna liczba jednostek żądań (jednostek ru) do aprowizacji

W Azure Cosmos DB przepływność jest inicjowana z wyprzedzeniem i mierzona w jednostkach żądań (RU) na sekundę. Jeśli masz obciążenia, które działają MongoDB na maszynie wirtualnej lub lokalnie, pomyśl, że jest to proste Abstrakcja zasobów fizycznych, takie jak rozmiar maszyny wirtualnej lub serwera lokalnego oraz posiadane przez nie zasoby, na przykład pamięć, procesor, operacje we/wy. 

W przeciwieństwie do maszyn wirtualnych lub serwerów lokalnych, jednostek ru można łatwo skalować w górę i w dół w dowolnym momencie. Można zmienić liczbę zainicjowanych jednostek RU w ciągu kilku sekund, a opłaty są naliczane tylko za maksymalną dozwoloną liczbę jednostek ru dla danego okresu. Aby uzyskać więcej informacji, zobacz [jednostki żądań w Azure Cosmos DB](request-units.md).

Poniżej przedstawiono kluczowe czynniki wpływające na liczbę wymaganych jednostek ru:
- **Rozmiar elementu (tj. dokumentu)** : w miarę wzrostu rozmiaru elementu/dokumentu, liczba jednostek ru zużytych do odczytu lub zapisu elementu/dokumentu również rośnie.
- **Liczba właściwości elementów**: przy założeniu, że [domyślne indeksowania](index-overview.md) wszystkich właściwości, liczba jednostek ru zużytych do napisania elementu zwiększa się w miarę wzrostu liczby właściwości elementu. Można zmniejszyć użycie jednostek żądania dla operacji zapisu, [ograniczając liczbę indeksowanych właściwości](index-policy.md).
- **Operacje współbieżne**: Zużyte jednostki żądań są również zależne od częstotliwości, w której są wykonywane różne operacje CRUD (takie jak operacje zapisu, odczyty, aktualizacje, usunięcia) i bardziej złożone zapytania. Możesz użyć [mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/) , aby wyprowadzić potrzeby współbieżności bieżących danych MongoDB.
- **Wzorce zapytań**: złożoność zapytania wpływa na liczbę jednostek żądań używanych przez zapytanie.

Jeśli eksportujesz pliki JSON przy użyciu programu [mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/) i zrozumiesz, ile zapisów, odczytów, aktualizacji i usunięć odbywa się na sekundę, możesz użyć [planisty wydajności Azure Cosmos DB](https://www.documentdb.com/capacityplanner) , aby oszacować początkową liczbę jednostek ru do aprowizacji. Planista wydajności nie jest czynnikiem związanym z bardziej złożonymi zapytaniami. Tak więc, jeśli masz złożone zapytania dotyczące danych, zostanie użyta dodatkowa jednostek ru. Kalkulator zakłada również, że wszystkie pola są indeksowane i jest używana spójność sesji. Najlepszym sposobem na zrozumienie kosztów zapytań jest przeprowadzenie migracji danych (lub przykładowych danych) do Azure Cosmos DB, [nawiązanie połączenia z punktem końcowym Cosmos DB](connect-mongodb-account.md) i uruchomienie przykładowego zapytania z powłoki MongoDB przy użyciu polecenia `getLastRequestStastistics` w celu uzyskania opłaty za żądanie, która będzie wyprowadzać liczbę użytych jednostek ru:

`db.runCommand({getLastRequestStatistics: 1})`

To polecenie spowoduje wyjście dokumentu JSON podobnego do poniższego:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Po zrozumieniu liczby jednostek ru zużytych przez zapytanie i potrzeby współbieżności dla tej kwerendy można dostosować liczbę zainicjowanych jednostek ru. Optymalizacja jednostek ru nie jest zdarzeniem jednorazowym — należy stale optymalizować lub skalować w górę jednostek ru, w zależności od tego, czy ruch nie jest oczekiwany, w przeciwieństwie do dużego obciążenia lub importowania danych.

## <a id="partitioning"></a>Wybierz klucz partycji
Partycjonowanie to kluczowy punkt rozważania przed migracją do globalnie rozproszonej bazy danych, takiej jak Azure Cosmos DB. Azure Cosmos DB używa partycjonowania do skalowania poszczególnych kontenerów w bazie danych, aby sprostać wymaganiom skalowalności i wydajności aplikacji. W przypadku partycjonowania elementy w kontenerze są podzielone na odrębne podzestawy o nazwie partycje logiczne. Aby uzyskać szczegółowe informacje i zalecenia dotyczące wybierania odpowiedniego klucza partycji dla danych, zobacz [sekcję Wybieranie klucza partycji](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Indeksowanie danych
Domyślnie Azure Cosmos DB indeksować wszystkie pola danych podczas pozyskiwania. [Zasady indeksowania](index-policy.md) można modyfikować w Azure Cosmos DB w dowolnym momencie. W rzeczywistości często zaleca się wyłączenie indeksowania podczas migrowania danych, a następnie włączenie go ponownie, gdy dane są już w Cosmos DB. Więcej informacji na temat indeksowania można znaleźć w sekcji [indeksowania w Azure Cosmos DB](index-overview.md) . 

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) automatycznie migruje kolekcje MongoDB z unikatowymi indeksami. Jednak przed migracją należy utworzyć unikatowe indeksy. Azure Cosmos DB nie obsługuje tworzenia unikatowych indeksów, gdy w kolekcjach znajdują się już dane. Aby uzyskać więcej informacji, zobacz [unikalne klucze w Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Następne kroki
* [Przeprowadź migrację danych MongoDB do Cosmos DB przy użyciu Database Migration Service.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Obsługa przepływności w kontenerach i bazach danych platformy Azure Cosmos](set-throughput.md)
* [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partition-data.md)
* [Dystrybucja globalna w Azure Cosmos DB](distribute-data-globally.md)
* [Indeksowanie w usłudze Azure Cosmos DB](index-overview.md)
* [Jednostki żądania w usłudze Azure Cosmos DB](request-units.md)
