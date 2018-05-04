---
title: 'Azure rozwiązania Cosmos bazy danych: Przykłady Java interfejsu API SQL | Dokumentacja firmy Microsoft'
description: Znajdź przykładów w języku Java w usłudze GitHub dla typowych zadań przy użyciu rozwiązania Cosmos bazy danych SQL interfejsu API Azure, w tym operacji CRUD.
keywords: Przykład NoSQL
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: java
ms.assetid: d824d517-903e-4d82-ab0a-09fc3b984c84
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 02/08/2018
ms.author: sngun
ms.openlocfilehash: 270cebd08fb1b03851825f7678689598cb9b71b9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-cosmos-db-java-examples-for-the-sql-api"></a>Azure rozwiązania Cosmos bazy danych: Przykłady Java interfejsu API SQL

> [!div class="op_single_selector"]
> * [Przykłady .NET](sql-api-dotnet-samples.md)
> * [Przykładów w języku Java](sql-api-java-samples.md)
> * [Przykłady środowiska node.js](sql-api-nodejs-samples.md)
> * [Przykłady dla języka Python](sql-api-python-samples.md)
> * [Galeria próbki kodu platformy Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Najnowsze przykładowe aplikacje, które wykonują operacje CRUD i innymi typowymi operacjami zasobów bazy danych Azure rozwiązania Cosmos znajdują się w [azure-documentdb-java](https://github.com/Azure/azure-documentdb-java) repozytorium GitHub. Ten artykuł zawiera:

* Pliki projektu łącza do zadań w każdym przykładzie Java. 
* Linki do pokrewnych interfejsu API odwoływać się do zawartości.

**Wymagania wstępne**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Możesz [aktywować korzyści dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Your Visual Studio subskrypcji otrzymasz kredyt, co miesiąc, używanego programu płatnych usług Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Potrzebne do uruchomienia tej aplikacji przykładowej:

* Zestaw Java Development Kit 7
* Zestawu SDK Java usługi DocumentDB platformy Microsoft Azure

Można opcjonalnie użyć Maven można pobrać najnowsze pliki binarne zestaw SDK Java usługi Microsoft Azure DocumentDB do użycia w projekcie. Maven automatycznie dodaje wszystkie wymagane zależności. W przeciwnym razie bezpośrednio pobrać zależności wymienione w pliku pom.xml i dodaj je do ścieżki kompilacji.

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-documentdb</artifactId>
    <version>LATEST</version>
</dependency>
```

**Uruchamianie aplikacji przykładowych**

Klonowanie repozytorium przykładowej:
```bash
$ git clone https://github.com/Azure/azure-documentdb-java.git

$ cd azure-documentdb-java
```

Można uruchomić przykłady przy użyciu programu Eclipse lub z wiersza polecenia za pomocą programu Maven.

Aby uruchomić środowisko Eclipse:
* Załaduj plik pom.xml projektu nadrzędnego głównego w środowisku Eclipse; powinien on automatycznie załadować przykłady usługi documentdb.
* Aby uruchomić próbki, należy prawidłowy punkt końcowy programu Azure rozwiązania Cosmos bazy danych. Punkty końcowe są odczytywane z `src/test/java/com/microsoft/azure/documentdb/examples/AccountCredentials.java`.
* Poświadczenia punktu końcowego można przekazać jako argumenty maszyny Wirtualnej w konfiguracji uruchomienia JUnit Eclipse lub poświadczenia punktu końcowego można umieścić w AccountCredentials.java.
    ```bash
    -DACCOUNT_HOST="https://REPLACE_THIS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS"
    ```
* Teraz możesz uruchomić przykłady jako JUnit testów w środowisku Eclipse.

Aby uruchomić z wiersza polecenia:
* Inny sposób uruchamiania przykładów jest używanie programu maven:
* Uruchom Maven i przekazać swoje poświadczenia punktu końcowego platformy Azure rozwiązania Cosmos bazy danych:
    ```bash
    mvn test -DACCOUNT_HOST="https://REPLACE_THIS_WITH_YOURS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS_WITH_YOURS"
    ```

   > [!NOTE]
   > Każda próbka jest autonomicznym środowiskiem; konfiguruje się, a czyści po samej siebie. Przykłady wystawiać wielu wywołań [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createcollection). Zawsze, gdy zostanie to zrobione, subskrypcja jest on rozliczany przez godzinę użycia warstwę wydajności kolekcji utworzony. 
   > 
   > 

## <a name="database-examples"></a>Przykłady bazy danych
[DatabaseCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java) pliku przedstawiono sposób wykonywania następujących zadań:

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie i odczytywanie bazy danych](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L64-L79) | [DocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createdatabase)<br>[DocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdatabase)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._resource.setid) |
| [Tworzenie i usuwanie bazy danych](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L82-L93) | [DocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.deletedatabase) |
| [Tworzenie i zapytanie dotyczące bazy danych](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L96-L111) | [DocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.querydatabases) |

## <a name="collection-examples"></a>Przykłady kolekcji
[CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) pliku przedstawiono sposób wykonywania następujących zadań:

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Utwórz kolekcję jednej partycji](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L74-L84) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createcollection) |
| [Utwórz kolekcję niestandardowych wielu partycji.](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L103-L155) | [Elementu DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_collection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._partition_key_definition)<br>[RequestOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._request_options) |
| [Usuwanie kolekcji](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L97-L99) | [DocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.deletecollection) |

## <a name="document-examples"></a>Przykłady dokumentu
[DocumentCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) pliku przedstawiono sposób wykonywania następujących zadań:

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenia, odczytu i usuwania dokumentu](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L84-L122) | [DocumentClient.createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createdocument)<br>[DocumentClient.readDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdocument)<br>[DocumentClient.deleteDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.deletedocument) |
| [Utwórz dokument z definicją programowalny dokumentu](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L126-L147) | [Dokument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._resource.setid) |

## <a name="indexing-examples"></a>Przykłady indeksowania
[CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) pliku przedstawiono sposób wykonywania następujących zadań:

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie indeksu i zestaw indeksowania zasad](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L125-L141) | [Indeks](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._index)<br>[IndexingPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy) |

Aby uzyskać więcej informacji na temat indeksowania, zobacz [Azure DB rozwiązania Cosmos zasady indeksowania](indexing-policies.md).

## <a name="query-examples"></a>Przykłady zapytań
[DocumentQuerySamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java) pliku przedstawiono sposób wykonywania następujących zadań:

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Wykonania kwerendy dotyczącej prostego dokumentu granic partycji](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L108-L129) | [DocumentClient.queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.querydocuments)<br>[FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setenablecrosspartitionquery) |
| [Kolejność według zapytania](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L132-L154) | [FeedResponse<T>.getQueryIterator](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_response.getqueryiterator) |

Aby uzyskać więcej informacji o pisaniu zapytań, zobacz [zapytanie SQL w ramach bazy danych Azure rozwiązania Cosmos](sql-api-sql-query.md).

## <a name="offer-examples"></a>Przykłady oferty
[OfferCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) pliku przedstawiono sposób wykonywania następujących zadań:

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie kolekcji i ustaw przepustowość](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L76-L102) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createcollection)<br>[RequestOptions.setOfferThroughput ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._request_options.setofferthroughput) |
| [Odczytać kolekcji można znaleźć skojarzonego oferty](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L108-L132) | [Offer.getContent](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._offer.getcontent)<br>[DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.replaceoffer)<br>[DocumentClient.readCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readcollection)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.queryoffers) |

## <a name="partition-key-examples"></a>Przykłady klucza partycji
[SinglePartitionCollectionDocumentCrudSample](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java) pliku przedstawiono sposób wykonywania następujących zadań:

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Utwórz kolekcję jednej partycji](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L164-L207) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createcollection) |
| [Zmień ofertę przepływność dla kolekcji jednej partycji](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L209-L223) | [DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.replaceoffer) |

## <a name="stored-procedure-examples"></a>Przykłady procedury składowanej
[StoredProcedureSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java) pliku przedstawiono sposób wykonywania następujących zadań:

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Utwórz procedurę składowaną](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L85-L118) | [Procedura składowana](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._stored_procedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createstoredprocedure) |
| [Uruchom procedurę składowaną z argumentami](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L121-L144) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.executestoredprocedure) |
| [Uruchom procedurę składowaną z argumentem obiektu](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L147-L177) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.executestoredprocedure) |
