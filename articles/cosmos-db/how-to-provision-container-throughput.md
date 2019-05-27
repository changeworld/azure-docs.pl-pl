---
title: Aprowizowanie przepływności kontenera w usłudze Azure Cosmos DB
description: Dowiedz się, jak aprowizować przepływność na poziomie kontenera w usłudze Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: dec6d0a65e556e0572bd030617e5b2c30078fce8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243829"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Aprowizowanie przepływności dla kontenera usługi Azure Cosmos

W tym artykule wyjaśniono, jak aprowizować przepływność na kontenerze (kolekcja, wykres lub tabelę) w usłudze Azure Cosmos DB. Użytkownik może aprowizować przepływność mierzoną w jednym kontenerze, lub [aprowizowanie przepływności w bazie danych](how-to-provision-database-throughput.md) i udostępniać je między kontenerów w bazie danych. Możesz aprowizować przepływność na kontenerze przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub zestawów SDK usługi Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Aprowizowanie przepływności przy użyciu witryny Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account), lub wybierz istniejące konto usługi Azure Cosmos.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowa kolekcja**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową bazę danych, czy używasz istniejącej.
   * Wprowadź identyfikator kontenera (tabela lub wykres).
   * Wprowadź wartość klucza partycji (na przykład `/userid`).
   * Wprowadź przepływność, którą chcesz aprowizować (na przykład 1000 jednostek).
   * Kliknij przycisk **OK**.

![Zrzut ekranu okienka usługi Data Explorer z wyróżnioną pozycją Nowa kolekcja](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

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

W przypadku udostępniania przepływność na kontenerze na koncie usługi Azure Cosmos skonfigurowany z interfejsem API usługi Azure Cosmos DB dla bazy danych MongoDB, użyj `/myShardKey` dla ścieżki klucza partycji. W przypadku udostępniania przepływność na kontenerze na koncie usługi Azure Cosmos skonfigurowane za pomocą interfejsu Cassandra API użyj `/myPrimaryKey` dla ścieżki klucza partycji.

## <a name="provision-throughput-by-using-net-sdk"></a>Aprowizowanie przepływności przy użyciu zestawu .NET SDK

> [!Note]
> Umożliwia Cosmos zestawy SDK dla interfejsu API SQL aprowizowanie przepływności dla wszystkich interfejsów API usługi DB Cosmos, z wyjątkiem interfejsu API rozwiązania Cassandra.

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

## <a name="next-steps"></a>Kolejne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat aprowizowania przepływności w usłudze Azure Cosmos DB:

* [Jak aprowizować przepływność mierzoną w bazie danych](how-to-provision-database-throughput.md)
* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)
