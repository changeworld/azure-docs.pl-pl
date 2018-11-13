---
title: Tworzenie kontenera w usłudze Azure Cosmos DB
description: Dowiedz się, jak utworzyć kontener w usłudze Azure Cosmos DB
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: d22103a049d62f48e8b9b9d3f71138842fbfaa89
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262492"
---
# <a name="create-a-container-in-azure-cosmos-db"></a>Tworzenie kontenera w usłudze Azure Cosmos DB

W tym artykule omówiono różne sposoby tworzenia kontenera (kolekcja, tabela, graf). Kontener można utworzyć za pomocą witryny Azure Portal, interfejsu wiersza polecenia platformy Azure bądź obsługiwanych zestawów SDK. W tym artykule pokazano, jak utworzyć kontener, określić klucz partycji i aprowizować przepływność.

## <a name="create-a-container-using-azure-portal"></a>Tworzenie kontenera przy użyciu witryny Azure Portal

### <a id="portal-sql"></a>Interfejs API SQL (podstawowy)

1. Zaloguj się w [portalu Azure](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) lub wybierz istniejące.

1. Otwórz okienko **Eksplorator danych** i wybierz pozycję **Nowa kolekcja**. Następnie wypełnij formularz przy użyciu następujących informacji:

   * Utwórz nową bazę danych lub wybierz istniejącą.
   * Wprowadź identyfikator kolekcji.
   * Wybierz pojemność magazynu **Bez ograniczeń**.
   * Wprowadź klucz partycji.
   * Wprowadź przepływność, na przykład 1000 jednostek RU.
   * Kliknij przycisk **OK**.

![Interfejs API SQL tworzy kolekcję](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb">Interfejs API usługi MongoDB</a>

1. Zaloguj się w [portalu Azure](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Cosmos DB](create-mongodb-dotnet.md#create-a-database-account) lub wybierz istniejące.

1. Otwórz okienko **Eksplorator danych** i wybierz pozycję **Nowa kolekcja**. Następnie wypełnij formularz przy użyciu następujących informacji:

   * Utwórz nową bazę danych lub wybierz istniejącą.
   * Wprowadź identyfikator kolekcji.
   * Wybierz pojemność magazynu **Bez ograniczeń**.
   * Wprowadź klucz fragmentu.
   * Wprowadź przepływność, na przykład 1000 jednostek RU.
   * Kliknij przycisk **OK**.

![Interfejs API usługi MongoDB tworzy kolekcję](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>Interfejs API rozwiązania Cassandra

1. Zaloguj się w [portalu Azure](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Cosmos DB](create-cassandra-dotnet.md#create-a-database-account) lub wybierz istniejące.

1. Otwórz okienko **Eksplorator danych** i wybierz pozycję **Nowa tabela**. Następnie wypełnij formularz przy użyciu następujących informacji:

   * Utwórz nową przestrzeń kluczy lub wybierz istniejącą.
   * Wprowadź nazwę tabeli.
   * Wprowadź właściwości i określ klucz podstawowy.
   * Wprowadź przepływność, na przykład 1000 jednostek RU.
   * Kliknij przycisk **OK**.

![Interfejs API rozwiązania Cassandra tworzy kolekcję](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> W przypadku interfejsu API rozwiązania Cassandra jako klucz partycji jest używany klucz podstawowy.

### <a id="portal-gremlin"></a>Interfejs API języka Gremlin

1. Zaloguj się w [portalu Azure](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Cosmos DB](create-graph-dotnet.md#create-a-database-account) lub wybierz istniejące.

1. Otwórz okienko **Eksplorator danych** i wybierz pozycję **Nowy graf**. Następnie wypełnij formularz przy użyciu następujących informacji:

   * Utwórz nową bazę danych lub wybierz istniejącą.
   * Wprowadź identyfikator grafu.
   * Wybierz pojemność magazynu **Bez ograniczeń**.
   * Wprowadź klucz partycji dla wierzchołków.
   * Wprowadź przepływność, na przykład 1000 jednostek RU.
   * Kliknij przycisk **OK**.

![Interfejs API języka Gremlin tworzy kolekcję](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>Interfejs API tabel

1. Zaloguj się w [portalu Azure](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Cosmos DB](create-table-dotnet.md#create-a-database-account) lub wybierz istniejące.

1. Otwórz okienko **Eksplorator danych** i wybierz pozycję **Nowa tabela**. Następnie wypełnij formularz przy użyciu następujących informacji:

   * Wprowadź identyfikator tabeli.
   * Wybierz pojemność magazynu **Bez ograniczeń**.
   * Wprowadź przepływność, na przykład 1000 jednostek RU.
   * Kliknij przycisk **OK**.

![Interfejs API tabel tworzy kolekcję](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> W przypadku interfejsu API tabel klucz partycji jest określany każdorazowo podczas dodawania nowego wiersza.

## <a name="create-a-container-using-azure-cli"></a>Tworzenie kontenera przy użyciu interfejsu wiersza polecenia platformy Azure

### <a id="cli-sql"></a>Interfejs API SQL (podstawowy)

```azurecli-interactive
# Create a container with a partition key and provision 1000 RU/s throughput.

az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-mongodb"></a>Interfejs API usługi MongoDB

```azurecli-interactive
# Create a collection with a shard key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $collectionName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myShardKey \
    --throughput 1000
```

### <a id="cli-cassandra"></a>Interfejs API rozwiązania Cassandra

```azurecli-interactive
# Create a table with a partition/primary key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $keyspaceName \
    --partition-key-path /myPrimaryKey \
    --throughput 1000
```

### <a id="cli-gremlin"></a>Interfejs API języka Gremlin

```azurecli-interactive
# Create a graph with a partition key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $graphName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-table"></a>Interfejs API tabel

```azurecli-interactive
# Create a table with 1000 RU/s
# Note: you don't need to specify partition key in the following command because the partition key is set on each row.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $databaseName \
    --throughput 1000
```

## <a name="create-a-container-using-net-sdk"></a>Tworzenie kontenera przy użyciu zestawu .NET SDK

### <a id="dotnet-sql-graph"></a>Interfejs API SQL i interfejs API języka Gremlin

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-mongodb"></a>Interfejs API usługi MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> W usłudze MongoDB nie funkcjonuje pojęcie jednostek żądań. Aby utworzyć nową kolekcję z odpowiednią przepływnością, skorzystaj z witryny Azure Portal lub interfejsu API SQL, jak pokazano w poprzednich przykładach.

### <a id="dotnet-cassandra"></a>Interfejs API rozwiązania Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat partycjonowania w usłudze Cosmos DB:

- [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partitioning-overview.md)
