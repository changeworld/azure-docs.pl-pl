---
title: Aprowizowanie przepływności kontenera w usłudze Azure Cosmos DB
description: Dowiedz się, jak aprowizować przepływność na poziomie kontenera w usłudze Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: eb34385087118614f8d7057c2229bc3c9e8d1ae4
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039490"
---
# <a name="provision-throughput-for-an-azure-cosmos-db-container"></a>Aprowizowanie przepływności dla kontenera usługi Azure Cosmos DB

W tym artykule wyjaśniono, jak aprowizować przepływność dla kontenera (kolekcji, grafu, tabeli) w usłudze Azure Cosmos DB. Przepływność można aprowizować dla jednego kontenera lub [dla bazy danych](how-to-provision-database-throughput.md) oraz udostępniać ją w ramach kontenerów w bazie danych. Przepływność dla kontenera można aprowizować za pomocą witryny Azure Portal, interfejsu wiersza polecenia platformy Azure lub zestawów SDK usługi Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Aprowizowanie przepływności przy użyciu witryny Azure Portal

1. Zaloguj się w [portalu Azure](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) lub wybierz istniejące.

1. Otwórz okienko **Eksplorator danych** i wybierz pozycję **Nowa kolekcja**. Następnie wypełnij formularz przy użyciu następujących informacji:

   * Utwórz nową bazę danych lub wybierz istniejącą.
   * Wprowadź identyfikator kolekcji (lub tabeli albo grafu).
   * Wprowadź wartość klucza partycji, na przykład `/userid`.
   * Wprowadź przepływność, na przykład 1000 jednostek RU.
   * Kliknij przycisk **OK**.

![Aprowizowanie przepływności kontenera — interfejs API SQL](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli"></a>Aprowizowanie przepływności przy użyciu interfejsu wiersza polecenia

```azurecli-interactive
# Create a container with a partition key and provision throughput of 1000 RU/s
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

W przypadku aprowizowania przepływności dla konta Cosmos skonfigurowanego za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB, użyj ścieżki klucza partycji „/myShardKey”, a podczas aprowizowania przepływności dla konta Cosmos skonfigurowanego dla interfejsu API rozwiązania Cassandra użyj ścieżki klucza partycji „/myPrimaryKey”.

## <a name="provision-throughput-using-net-sdk"></a>Aprowizowanie przepływności przy użyciu zestawu .NET SDK

> [!Note]
> Użyj interfejsu API SQL, aby aprowizować przepływność dla wszystkich interfejsów API z wyjątkiem interfejsu API rozwiązania Cassandra.

### <a id="dotnet-most"></a>Język SQL, usługa MongoDB, język Gremlin oraz interfejsy API tabel

```csharp
// Create a container with a partition key and provision throughput of 1000 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-cassandra"></a>Interfejs API rozwiązania Cassandra

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 1000 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat aprowizowania przepływności w usłudze Cosmos DB:

* [How to provision throughput for a database (Jak aprowizować przepływność danego kontenera)](how-to-provision-database-throughput.md)
* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)
