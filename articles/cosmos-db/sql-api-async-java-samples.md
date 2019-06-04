---
title: Usługa Azure Cosmos DB Przykłady asynchronicznego kodu Java dla interfejsu API SQL
description: Znajdź w witrynie GitHub przykłady asynchronicznego kodu w języku Java służące do wykonywania typowych zadań przy użyciu interfejsu API SQL w usłudze Azure Cosmos DB, w tym operacji CRUD.
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: sample
ms.date: 12/03/2018
ms.author: sngun
ms.openlocfilehash: 46858affe44d488a9ade1693713072fded18afed
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475790"
---
# <a name="azure-cosmos-db-async-java-examples-for-the-sql-api"></a>Usługa Azure Cosmos DB Przykłady asynchronicznego kodu Java dla interfejsu API SQL

> [!div class="op_single_selector"]
> * [Przykłady dla platformy .NET](sql-api-dotnet-samples.md)
> * [Przykłady kodu Java](sql-api-java-samples.md)
> * [Przykłady asynchronicznego kodu Java](sql-api-async-java-samples.md)
> * [Przykłady dla platformy Node.js](sql-api-nodejs-samples.md)
> * [Przykłady kodu Python](sql-api-python-samples.md)
> * [Galeria przykładów kodu dla platformy Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Najnowsze przykładowe aplikacje do wykonywania operacji CRUD i innych typowych działań na zasobach usługi Azure Cosmos DB można znaleźć w repozytorium [azure-cosmosdb-java](https://github.com/Azure/azure-cosmosdb-java) w witrynie GitHub. Ten artykuł zawiera:

* Linki do zadań w poszczególnych plikach projektów z przykładami asynchronicznego kodu w języku Java. 
* Linki do powiązanej dokumentacji interfejsu API.

**Wymagania wstępne**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Możesz [aktywować korzyści subskrybenta programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): W ramach subskrypcji programu Visual Studio co miesiąc otrzymasz środki, które możesz przeznaczyć na płatne usługi platformy Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Do uruchomienia tej aplikacji przykładowej potrzebne są następujące elementy:

* Zestaw Java Development Kit 8
* Zestaw Java SDK usługi Microsoft Azure Cosmos DB

Opcjonalnie możesz pobrać najnowsze pliki binarne zestawu Java SDK usługi Microsoft Azure Cosmos DB do użycia w projekcie za pomocą rozwiązania Maven. Najnowszą wersję można znaleźć [tutaj](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb). Rozwiązanie Maven automatycznie dodaje wszystkie wymagane zależności. Innym sposobem jest bezpośrednie pobranie zależności wymienionych w pliku pom.xml i dodanie ich do ścieżki kompilacji.

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-cosmosdb</artifactId>
    <version>${LATEST}</version>
</dependency>
```

**Uruchamianie aplikacji przykładowych**

Sklonuj repozytorium przykładowe:
```bash
$ git clone https://github.com/Azure/azure-cosmosdb-java.git

$ cd azure-cosmosdb-java
```

Przykłady można uruchamiać przy użyciu programu Eclipse lub wiersza polecenia i rozwiązania Maven.

Aby uruchomić aplikację z poziomu programu Eclipse:
* Załaduj plik pom.xml głównego projektu nadrzędnego w programie Eclipse. Plik azure-cosmosdb-examples powinien zostać załadowany automatycznie.
* Aby uruchomić przykłady, potrzebujesz prawidłowego punktu końcowego usługi Azure Cosmos DB. Punkty końcowe są odczytywane z pliku `src/test/java/com/microsoft/azure/cosmosdb/rx/examples/TestConfigurations.java`.
* Poświadczenia punktu końcowego można przekazać jako argumenty maszyny wirtualnej w elemencie JUnit Run Config programu Eclipse lub umieścić je w pliku TestConfigurations.java.
    ```bash
    -DACCOUNT_HOST=<Fill your Azure Cosmos DB account name> -DACCOUNT_KEY=<Fill your Azure Cosmos DB primary key>
    ```
* Teraz możesz uruchamiać przykłady jako testy JUnit w programie Eclipse.

Aby uruchomić aplikację z poziomu wiersza polecenia:
* Innym sposobem na uruchomienie przykładów jest użycie narzędzia Maven:
* Uruchom rozwiązanie Maven i przekaż poświadczenia punktu końcowego usługi Azure Cosmos DB:
    ```bash
    mvn test -DACCOUNT_HOST=<Fill your Azure Cosmos DB account name> -DACCOUNT_KEY=<Fill your Azure Cosmos DB primary key>
    ```

   > [!NOTE]
   > Przykłady są niezależne — każdy z nich jest automatycznie konfigurowany i automatycznie czyszczony po użyciu. Przykłady obejmują wiele wywołań metody [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection). Za każdym razem w ramach subskrypcji jest naliczana opłata za 1 godzinę użycia warstwy wydajności, w której jest tworzona kolekcja. 
   > 
   > 

## <a name="database-examples"></a>Przykłady dotyczące baz danych
[DatabaseCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java) plik pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej o bazach danych Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [pracy z bazami danych, kontenerów i elementy](databases-containers-items.md) artykuł koncepcyjny. 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie bazy danych](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L115-L132) | [AsyncDocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createdatabase) |
| [Nie można utworzyć bazy danych, która już istnieje](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L189-L204) | |
| [Tworzenie i odczytywanie bazy danych](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L228-L249) | [AsyncDocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readdatabase) |
| [Tworzenie i usuwanie bazy danych](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L255-L276) | [AsyncDocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletedatabase) |
| [Tworzenie bazy danych i wykonywanie w niej zapytań](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L282-L312) | [AsyncDocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydatabases) |

## <a name="collection-examples"></a>Przykłady dotyczące kolekcji
[CollectionCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java) plik pokazuje, jak wykonać poniższe zadania. Informacje na temat kolekcji usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [pracy z bazami danych, kontenerów i elementy](databases-containers-items.md) artykuł koncepcyjny. 

| Zadanie | Dokumentacja interfejsów API |
| --- | --- |
| [Tworzenie kolekcji z jedną partycją](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L134-L153) | [AsyncDocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection) |
| [Tworzenie kolekcji niestandardowej z wieloma partycjami](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L164-L184) | [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.partitionkeydefinition)<br>[RequestOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.requestoptions) |
| [Nie można utworzyć kolekcji, która już istnieje](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L241-L256) | |
| [Tworzenie i odczytywanie kolekcji](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L281-L304) | [AsyncDocumentClient.readCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readcollection) |
| [Tworzenie i usuwanie kolekcji](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L310-L333) | [AsyncDocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletecollection) |
| [Tworzenie kolekcji i wykonywanie w niej zapytań](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L339-L372) | [AsyncDocumentClient.queryCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querycollections) |

## <a name="document-examples"></a>Przykłady dotyczące dokumentów
[DocumentCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java) plik pokazuje, jak wykonać poniższe zadania. Aby uzyskać informacje o dokumentach w usłudze Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [pracy z bazami danych, kontenerów i elementy](databases-containers-items.md) artykuł koncepcyjny. 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie dokumentu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L137-L156) | [AsyncDocumentClient.createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createdocument) |
| [Tworzenie dokumentu z programowalną definicją dokumentu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L213-L242) | [Document](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.document)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.resource.setid) |
| [Tworzenie dokumentów i znajdywanie całkowitego kosztu jednostek żądań](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L248-L280) | [ResourceResponse.getRequestCharge](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.resourceresponse.getrequestcharge) |
| [Nie można utworzyć dokumentu, który już istnieje](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L316-L336) | |
| [Tworzenie i zastępowanie dokumentu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L342-L365) | [AsyncDocumentClient.replaceDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.replacedocument) |
| [Tworzenie dokumentu i stosowanie do niego akcji „upsert”](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L371-L393) | [AsyncDocumentClient.upsertDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.upsertdocument) |
| [Tworzenie i usuwanie dokumentu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L399-L431) | [AsyncDocumentClient.deleteDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletedocument) |
| [Tworzenie i odczytywanie dokumentu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L437-L458) | [AsyncDocumentClient.readDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readdocument) |

## <a name="indexing-examples"></a>Przykłady dotyczące indeksowania
[CollectionCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java) plik pokazuje, jak wykonać poniższe zadania.  Aby uzyskać informacje dotyczące indeksowania w usłudze Azure Cosmos DB, przed uruchomieniem poniższych przykładów, zobacz [zasady indeksowania](index-policy.md), [indeksowania typy](index-types.md), i [indeksowania ścieżki](index-paths.md) artykułów koncepcyjnych. 

| Zadanie | Dokumentacja interfejsów API |
| --- | --- |
| [Tworzenie indeksu i konfigurowanie zasad indeksowania](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L394-L410) | [Index](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.index)<br>[IndexingPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.indexingpolicy) |

Aby uzyskać więcej informacji na temat indeksowania, zobacz [Zasady indeksowania w usłudze Azure Cosmos DB](index-policy.md).

## <a name="query-examples"></a>Przykłady zapytań
[DocumentQuerySamples](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java) plik pokazuje, jak wykonać poniższe zadania. Aby poznać dokumentacja zapytań SQL w usłudze Azure Cosmos DB, przed uruchomieniem poniższych przykładów, zobacz temat [przykłady zapytań SQL](how-to-sql-query.md) artykuł koncepcyjny. 

| Zadanie | Dokumentacja interfejsów API |
| --- | --- |
| [Wykonywanie prostego zapytania dotyczącego dokumentu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L154-L190) | [AsyncDocumentClient.queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydocuments) |
| [Wykonywanie prostego zapytania dotyczącego dokumentu i znajdywanie całkowitego kosztu jednostek żądań](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L249-L268) | [FeedResponse.getRequestCharge](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.feedresponse.getrequestcharge) |
| [Wykonywanie prostego zapytania dotyczącego dokumentu, odczytywanie jednej strony i anulowanie subskrypcji zwróconego obserwowalnego elementu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L274-L312) | |
| [Wykonywanie prostego zapytania dotyczącego dokumentu i filtrowanie wyników](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L318-L368) | |
| [Wykonuje kwerendę dokumentu między partycjami klauzuli order by](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L410-L457) | [FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.feedoptions.setenablecrosspartitionquery) |

Aby uzyskać więcej informacji o pisaniu zapytań, zobacz [SQL query within Azure Cosmos DB (Zapytania SQL w usłudze Azure Cosmos DB)](how-to-sql-query.md).

## <a name="offer-examples"></a>Przykłady dotyczące ofert
W pliku [OfferCRUDAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java) przedstawiono sposób wykonywania następujących zadań:

| Zadanie | Dokumentacja interfejsów API |
| --- | --- |
| [Tworzenie kolekcji i ustawianie przepływności](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L106-L113) | [AsyncDocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection)<br>[RequestOptions.setOfferThroughput](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.requestoptions.setofferthroughput) |
| [Odczytywanie kolekcji w celu znalezienia powiązanej oferty](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L118-L130) | [Offer.getContent](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.offer.getContent)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.queryoffers) |
| [Aktualizowanie przepływności kolekcji poprzez zastąpienie jej oferty](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L101-L153) | [AsyncDocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.replaceoffer) |

## <a name="stored-procedure-examples"></a>Przykłady dotyczące procedur składowanych
[StoredProcedureAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java) plik pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat programowania po stronie serwera w usłudze Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika](stored-procedures-triggers-udfs.md) artykuł koncepcyjny. 

| Zadanie | Dokumentacja interfejsów API |
| --- | --- |
| [Tworzenie i uruchamianie procedury składowanej](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L108-L155) | [StoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.storedprocedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createstoredprocedure)<br>[AsyncDocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.executestoredprocedure) |
| [Tworzenie i uruchamianie procedury składowanej z użyciem argumentów](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L161-L195) | |
| [Tworzenie i uruchamianie procedury składowanej z argumentem obiektu](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L201-L241) | |

## <a name="unique-key"></a>Klucz unikatowy
[UniqueIndexAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/UniqueIndexAsyncAPITest.java) plik pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej o unikatowych kluczy w usłudze Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [unikatowych ograniczeń klucza](unique-keys.md) artykuł koncepcyjny. 

| Zadanie | Dokumentacja interfejsów API |
| --- | --- |
| [Tworzenie kolekcji z kluczem unikatowym](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/UniqueIndexAsyncAPITest.java#L65-L97) | [UniqueKey](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.uniquekey)<br>[UniqueKeyPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.uniquekeypolicy)<br>[DocumentCollection.setUniqueKeyPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection.setuniquekeypolicy) |
