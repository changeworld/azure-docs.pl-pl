---
title: 'Azure Cosmos DB: przykłady kodu Java dla interfejsu API SQL'
description: Znajdź w witrynie GitHub przykłady kodu w języku Java służące do wykonywania typowych zadań przy użyciu interfejsu API SQL w usłudze Azure Cosmos DB, w tym operacji CRUD.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: sample
ms.date: 02/08/2019
ms.author: sngun
ms.openlocfilehash: 8b133f0044bdf8f99fdee657177d561ef5bb406b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238482"
---
# <a name="azure-cosmos-db-java-examples-for-the-sql-api"></a>Azure Cosmos DB: przykłady kodu Java dla interfejsu API SQL

> [!div class="op_single_selector"]
> * [Przykłady SDK .NET V2](sql-api-dotnet-samples.md)
> * [Przykłady SDK .NET V3](sql-api-dotnet-v3sdk-samples.md)
> * [Przykłady kodu Java](sql-api-java-samples.md)
> * [Przykłady asynchronicznego kodu Java](sql-api-async-java-samples.md)
> * [Przykłady dla platformy Node.js](sql-api-nodejs-samples.md)
> * [Przykłady Pythona](sql-api-python-samples.md)
> * [Galeria przykładów kodu dla platformy Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Najnowsze przykładowe aplikacje do wykonywania operacji CRUD i innych typowych działań na zasobach usługi Azure Cosmos DB można znaleźć w repozytorium [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-java) w witrynie GitHub. Ten artykuł zawiera:

* Linki do zadań w poszczególnych plikach projektów przykładowych w języku Java. 
* Linki do powiązanej dokumentacji interfejsu API.

**Wymagania wstępne**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Możesz [aktywować korzyści subskrybenta programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): w ramach subskrypcji programu Visual Studio co miesiąc otrzymasz środki, które można przeznaczyć na płatne usługi platformy Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Do uruchomienia tej aplikacji przykładowej potrzebne są następujące elementy:

* Zestaw Java Development Kit 7
* Zestaw SDK usługi Microsoft Azure DocumentDB dla języka Java

Opcjonalnie możesz pobrać najnowsze pliki binarne zestawu SDK usługi Microsoft Azure DocumentDB dla języka Java do użycia w projekcie za pomocą narzędzia Maven. Rozwiązanie Maven automatycznie dodaje wszystkie wymagane zależności. Innym sposobem jest bezpośrednie pobranie zależności wymienionych w pliku pom.xml i dodanie ich do ścieżki kompilacji.

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-documentdb</artifactId>
    <version>LATEST</version>
</dependency>
```

**Uruchamianie aplikacji przykładowych**

Sklonuj repozytorium przykładowe:
```bash
$ git clone https://github.com/Azure/azure-documentdb-java.git

$ cd azure-documentdb-java
```

Przykłady można uruchamiać przy użyciu programu Eclipse lub wiersza polecenia i rozwiązania Maven.

Aby uruchomić aplikację z poziomu programu Eclipse:
* Załaduj plik pom.xml głównego projektu nadrzędnego w programie Eclipse. Plik documentdb-examples powinien zostać załadowany automatycznie.
* Aby uruchomić przykłady, potrzebujesz prawidłowego punktu końcowego usługi Azure Cosmos DB. Punkty końcowe są odczytywane z pliku `src/test/java/com/microsoft/azure/documentdb/examples/AccountCredentials.java`.
* Możesz przekazać poświadczenia punktu końcowego jako argumenty maszyny wirtualnej w elemencie JUnit Run Config programu Eclipse lub umieścić je w pliku AccountCredentials.java.
    ```bash
    -DACCOUNT_HOST="https://REPLACE_THIS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS"
    ```
* Teraz możesz uruchamiać przykłady jako testy JUnit w programie Eclipse.

Aby uruchomić aplikację z poziomu wiersza polecenia:
* Innym sposobem na uruchomienie przykładów jest użycie narzędzia Maven:
* Uruchom rozwiązanie Maven i przekaż poświadczenia punktu końcowego usługi Azure Cosmos DB:
    ```bash
    mvn test -DACCOUNT_HOST="https://REPLACE_THIS_WITH_YOURS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS_WITH_YOURS"
    ```

   > [!NOTE]
   > Przykłady są niezależne — każdy z nich jest automatycznie konfigurowany i automatycznie czyszczony po użyciu. Próbki wydają wiele wywołań [documentclient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection). Za każdym razem w ramach subskrypcji jest naliczana opłata za 1 godzinę użycia warstwy wydajności, w której jest tworzona kolekcja. 
   > 
   > 

## <a name="database-examples"></a>Przykłady dotyczące baz danych
[Plik DatabaseCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java) pokazuje sposób wykonywania następujących zadań. Aby dowiedzieć się więcej o bazach danych usługi Azure Cosmos przed uruchomieniem następujących przykładów, zobacz [Praca z bazami danych, kontenerami i elementami](databases-containers-items.md) artykułu koncepcyjnego. 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie i odczytywanie bazy danych](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L64-L79) | [DocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createdatabase)<br>[DocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdatabase)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resource.setid) |
| [Tworzenie i usuwanie bazy danych](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L82-L93) | [DocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.deletedatabase) |
| [Tworzenie bazy danych i wykonywanie w niej zapytań](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L96-L111) | [DocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.querydatabases) |

## <a name="collection-examples"></a>Przykłady dotyczące kolekcji
Plik [CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) pokazuje sposób wykonywania następujących zadań. Aby dowiedzieć się więcej o kolekcji usługi Azure Cosmos przed uruchomieniem następujących przykładów, zobacz [Praca z bazami danych, kontenerami i elementami](databases-containers-items.md) artykułu koncepcyjnego.

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie kolekcji z jedną partycją](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L74-L84) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) |
| [Tworzenie kolekcji niestandardowej z wieloma partycjami](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L103-L155) | [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentcollection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.partitionkeydefinition)<br>[RequestOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.requestoptions) |
| [Usuwanie kolekcji](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L97-L99) | [DocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.deletecollection) |

## <a name="document-examples"></a>Przykłady dotyczące dokumentów
[Plik DocumentCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) pokazuje sposób wykonywania następujących zadań. Aby dowiedzieć się więcej o dokumentach usługi Azure Cosmos przed uruchomieniem następujących przykładów, zobacz [Praca z bazami danych, kontenerami i elementami](databases-containers-items.md) artykułu koncepcyjnego.

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie, odczytywanie i usuwanie dokumentu](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L84-L122) | [DocumentClient.createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createdocument)<br>[DocumentClient.readDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdocument)<br>[DocumentClient.deleteDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.deletedocument) |
| [Tworzenie dokumentu z programowalną definicją dokumentu](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L126-L147) | [Dokumentu](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.document)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resource.setid) |

## <a name="indexing-examples"></a>Przykłady dotyczące indeksowania
Plik [CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) pokazuje sposób wykonywania następujących zadań. Aby dowiedzieć się więcej o indeksowaniu w usłudze Azure Cosmos DB przed uruchomieniem następujących przykładów, zobacz [zasady indeksowania,](index-policy.md) [typy indeksowania](index-types.md)i [indeksowanie ścieżek](index-paths.md) artykułów koncepcyjnych. 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie indeksu i konfigurowanie zasad indeksowania](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L125-L141) | [Indeks](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.index)<br>[IndexingPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy) |

Aby uzyskać więcej informacji na temat indeksowania, zobacz [Zasady indeksowania w usłudze Azure Cosmos DB](index-policy.md).

## <a name="query-examples"></a>Przykłady zapytań
[Plik DocumentQuerySamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java) pokazuje sposób wykonywania następujących zadań. Aby dowiedzieć się więcej o odwołaniu do kwerendy SQL w usłudze Azure Cosmos DB przed uruchomieniem następujących przykładów, zobacz artykuł koncepcyjny [przykładów zapytań SQL.](how-to-sql-query.md) 


| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Wykonywanie prostego zapytania dotyczącego dokumentu, obejmującego wiele partycji](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L108-L129) | [DocumentClient.queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.querydocuments)<br>[FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setenablecrosspartitionquery) |
| [Wykonywanie zapytania z instrukcją „order by”](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L132-L154) | [FeedResponse\<T>.getQueryIterator](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedresponse.getqueryiterator) |

Aby uzyskać więcej informacji o pisaniu zapytań, zobacz [SQL query within Azure Cosmos DB (Zapytania SQL w usłudze Azure Cosmos DB)](how-to-sql-query.md).

## <a name="offer-examples"></a>Przykłady dotyczące ofert
W pliku [OfferCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) przedstawiono sposób wykonywania następujących zadań:

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie kolekcji i ustawianie przepływności](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L76-L102) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection)<br>[RequestOptions.setOfferThroughput](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.requestoptions.setofferthroughput) |
| [Odczytywanie kolekcji w celu znalezienia powiązanej oferty](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L108-L132) | [Offer.getContent](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.offer.getcontent)<br>[DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.replaceoffer)<br>[DocumentClient.readCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readcollection)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.queryoffers) |

## <a name="partition-key-examples"></a>Przykłady dotyczące klucza partycji
[Plik SinglePartitionCollectionDocumentCrudSample](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java) pokazuje sposób wykonywania następujących zadań. Aby dowiedzieć się więcej o partycjonowaniu i klucze partycji w usłudze Azure Cosmos DB przed uruchomieniem następujących przykładów, zobacz [partycjonowanie](partitioning-overview.md) artykuł koncepcyjny. 


| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie kolekcji z jedną partycją](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L164-L207) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) |
| [Zmienianie oferty przepływności dla kolekcji z jedną partycją](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L209-L223) | [DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.replaceoffer) |

## <a name="stored-procedure-examples"></a>Przykłady dotyczące procedur składowanych
[Plik StoredProcedureSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java) pokazuje sposób wykonywania następujących zadań. Aby dowiedzieć się więcej o programowaniu po stronie serwera w usłudze Azure Cosmos DB przed uruchomieniem następujących przykładów, zobacz [Procedury przechowywane, wyzwalacze i zdefiniowane przez użytkownika funkcje](stored-procedures-triggers-udfs.md) koncepcyjne artykułu. 


| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie procedury składowanej](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L85-L118) | [StoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.storedprocedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createstoredprocedure) |
| [Uruchamianie procedury składowanej z użyciem argumentów](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L121-L144) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.executestoredprocedure) |
| [Uruchamianie procedury składowanej z argumentem obiektu](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L147-L177) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.executestoredprocedure) |
