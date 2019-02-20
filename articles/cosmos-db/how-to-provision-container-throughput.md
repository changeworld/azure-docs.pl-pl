---
title: Aprowizowanie przepływności kontenera w usłudze Azure Cosmos DB
description: Dowiedz się, jak aprowizować przepływność na poziomie kontenera w usłudze Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 4df8a12581b5d71a76964ca1e3d40c6c53185f67
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860324"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Aprowizowanie przepływności dla kontenera usługi Azure Cosmos

W tym artykule wyjaśniono, jak aprowizować przepływność dla kontenera (kolekcji, grafu lub tabeli) w usłudze Azure Cosmos DB. Przepływność można aprowizować dla jednego kontenera lub [dla bazy danych](how-to-provision-database-throughput.md) oraz udostępniać ją w ramach kontenerów w bazie danych. Przepływność dla kontenera można aprowizować za pomocą witryny Azure Portal, interfejsu wiersza polecenia platformy Azure lub zestawów SDK usługi Azure Cosmos DB.

## <a name="provision-throughput-by-using-azure-portal"></a>Aprowizowanie przepływności przy użyciu witryny Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) lub wybierz istniejące konto.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowa kolekcja**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową bazę danych, czy używasz istniejącej.
   * Wprowadź identyfikator kolekcji (lub tabeli albo grafu).
   * Wprowadź wartość klucza partycji (na przykład `/userid`).
   * Wprowadź przepływność (na przykład 1000 jednostek RU).
   * Kliknij przycisk **OK**.

![Zrzut ekranu okienka usługi Data Explorer z wyróżnioną pozycją Nowa kolekcja](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-by-using-azure-cli"></a>Aprowizowanie przepływności przy użyciu interfejsu wiersza polecenia platformy Azure

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

W przypadku aprowizowania przepływności dla konta usługi Azure Cosmos DB skonfigurowanego przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB użyj wartości `/myShardKey` jako ścieżki klucza partycji. W przypadku aprowizowania przepływności dla konta usługi Azure Cosmos DB skonfigurowanego przy użyciu interfejsu API Cassandra użyj wartości `/myPrimaryKey` jako ścieżki klucza partycji.

## <a name="provision-throughput-by-using-net-sdk"></a>Aprowizowanie przepływności przy użyciu zestawu .NET SDK

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

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat aprowizowania przepływności w usłudze Azure Cosmos DB:

* [How to provision throughput for a database (Jak aprowizować przepływność danego kontenera)](how-to-provision-database-throughput.md)
* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)
