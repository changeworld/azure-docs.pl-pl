---
title: Aprowizowanie przepływności kontenera w usłudze Azure Cosmos DB
description: Dowiedz się, jak aprowizować przepływność na poziomie kontenera w usłudze Azure Cosmos DB przy użyciu witryny Azure portal, cli, powershell i różnych innych zestawów SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mjbrown
ms.openlocfilehash: e416501cb3c532b3ba0a262442b35b236875a463
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273292"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Aprowizowanie przepływności dla kontenera usługi Azure Cosmos

W tym artykule wyjaśniono, jak aprowizować przepływność w kontenerze (kolekcja, wykres lub tabela) w usłudze Azure Cosmos DB. Można aprowizować przepływność w jednym kontenerze lub [aprowizować przepływność w bazie danych](how-to-provision-database-throughput.md) i udostępnić ją między kontenerami w bazie danych. Przepływność w kontenerze można aprowizować przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub zestawów SDK usługi Azure Cosmos DB.

## <a name="azure-portal"></a>Portal Azure

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account)lub wybierz istniejące konto usługi Azure Cosmos.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowa kolekcja**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową bazę danych, czy używasz istniejącej.
   * Wprowadź identyfikator kontenera (lub tabeli lub wykresu).
   * Wprowadź wartość klucza partycji (na przykład `/userid`).
   * Wprowadź przepływność, którą chcesz aprowizować (na przykład 1000 procesorów RU).
   * Kliknij przycisk **OK**.

    ![Zrzut ekranu okienka usługi Data Explorer z wyróżnioną pozycją Nowa kolekcja](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="azure-cli-or-powershell"></a>Narzędzie interfejsu wiersza polecenia platformy Azure lub programu PowerShell

Aby utworzyć kontener z dedykowaną przepływnością zobacz,

* [Tworzenie kontenera przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#create-a-container)
* [Tworzenie kontenera przy użyciu programu Powershell](manage-with-powershell.md#create-container)

> [!Note]
> Jeśli inicjujesz przepływność obsługi administracyjnej w kontenerze na koncie usługi Azure Cosmos `/myShardKey` skonfigurowanym za pomocą interfejsu API usługi Azure Cosmos DB dla usługi MongoDB, użyj ścieżki klucza partycji. Jeśli inicjujesz przepływność obsługi administracyjnej w kontenerze na koncie `/myPrimaryKey` usługi Azure Cosmos skonfigurowanym za pomocą interfejsu API Cassandra, użyj ścieżki klucza partycji.

## <a name="net-sdk"></a>Zestaw SDK .NET

> [!Note]
> Użyj pakietów SDK usługi Cosmos dla interfejsu API SQL do aprowizowania przepływności dla wszystkich interfejsów API bazy danych usługi Cosmos, z wyjątkiem interfejsu API Cassandra.

### <a name="sql-mongodb-gremlin-and-table-apis"></a><a id="dotnet-most"></a>Język SQL, usługa MongoDB, język Gremlin oraz interfejsy API tabel
### <a name="net-v2-sdk"></a>Plik SDK .Net V2

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

### <a name="net-v3-sdk"></a>.Net V3 SDK

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

## <a name="javascript-sdk"></a>Zestaw SDK dla języka JavaScript

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra API

Podobne polecenia mogą być wydawane za pośrednictwem dowolnego sterownika zgodnego z CQL.

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>Zmienianie lub zmienianie przepływności dla tabeli Cassandra

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat aprowizowania przepływności w usłudze Azure Cosmos DB:

* [Jak aprowizować przepływność w bazie danych](how-to-provision-database-throughput.md)
* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)
