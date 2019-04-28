---
title: Tworzenie kontenera w usłudze Azure Cosmos DB
description: Dowiedz się, jak utworzyć kontener w usłudze Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/17/2019
ms.author: rimman
ms.openlocfilehash: c075a801a877309709258dd6466e68e46d802eff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61056647"
---
# <a name="create-an-azure-cosmos-container"></a>Tworzenie kontenera usługi Azure Cosmos

W tym artykule opisano różne sposoby tworzenia kontenera usługi Azure Cosmos (kolekcja, tabela lub wykres). Za pomocą witryny Azure portal, interfejsu wiersza polecenia platformy Azure, lub obsługiwanych zestawów SDK, w tym. W tym artykule pokazano, jak utworzyć kontener, określić klucz partycji i aprowizować przepływność.

## <a name="create-a-container-using-azure-portal"></a>Tworzenie kontenera przy użyciu witryny Azure Portal

### <a id="portal-sql"></a>Interfejs API SQL

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account), lub wybierz istniejące konto.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowa kolekcja**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową bazę danych, czy używasz istniejącej.
   * Wprowadź identyfikator kolekcji.
   * Wprowadź klucz partycji.
   * Wprowadź przepustowość do udostępnienia (na przykład 1000 jednostek RU).
   * Kliknij przycisk **OK**.

![Zrzut ekranu okienka usługi Data Explorer z wyróżnioną pozycją Nowa kolekcja](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account), lub wybierz istniejące konto.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowa kolekcja**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową bazę danych, czy używasz istniejącej.
   * Wprowadź identyfikator kolekcji.
   * Wprowadź klucz fragmentu.
   * Wprowadź przepustowość do udostępnienia (na przykład 1000 jednostek RU).
   * Kliknij przycisk **OK**.

![Zrzut ekranu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB, okno dialogowe Dodawanie kolekcji](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>Interfejs API rozwiązania Cassandra

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-cassandra-dotnet.md#create-a-database-account), lub wybierz istniejące konto.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowa tabela**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową przestrzeń kluczy, czy używasz istniejącej.
   * Wprowadź nazwę tabeli.
   * Wprowadź właściwości i określ klucz podstawowy.
   * Wprowadź przepustowość do udostępnienia (na przykład 1000 jednostek RU).
   * Kliknij przycisk **OK**.

![Zrzut ekranu interfejsu API Cassandra, okno dialogowe Dodawanie tabeli](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> W przypadku interfejsu API rozwiązania Cassandra jako klucz partycji jest używany klucz podstawowy.

### <a id="portal-gremlin"></a>Interfejs API języka Gremlin

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-graph-dotnet.md#create-a-database-account), lub wybierz istniejące konto.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowy graf**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową bazę danych, czy używasz istniejącej.
   * Wprowadź identyfikator wykresu.
   * Wybierz pojemność magazynu **Bez ograniczeń**.
   * Wprowadź klucz partycji dla wierzchołków.
   * Wprowadź przepustowość do udostępnienia (na przykład 1000 jednostek RU).
   * Kliknij przycisk **OK**.

![Zrzut ekranu interfejsu API Gremlin, okno dialogowe Dodawanie grafu](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>Interfejs API tabel

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-table-dotnet.md#create-a-database-account), lub wybierz istniejące konto.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowa tabela**. Następnie podaj następujące szczegóły:

   * Wprowadź identyfikator tabeli.
   * Wprowadź przepustowość do udostępnienia (na przykład 1000 jednostek RU).
   * Kliknij przycisk **OK**.

![Zrzut ekranu interfejsu API tabel, okno dialogowe Dodawanie tabeli](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> W przypadku interfejsu API tabel klucz partycji jest określany każdorazowo podczas dodawania nowego wiersza.

## <a name="create-a-container-using-azure-cli"></a>Tworzenie kontenera przy użyciu interfejsu wiersza polecenia platformy Azure

### <a id="cli-sql"></a>Interfejs API SQL

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

### <a id="cli-mongodb"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB

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

### <a id="dotnet-mongodb"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> Protokół przewodowy MongoDB nie rozumiesz koncepcję [jednostek żądań](request-units.md). Aby utworzyć nową kolekcję z aprowizowaną przepływność na nim, należy użyć witryny Azure portal lub zestawów SDK, Cosmos DB dla interfejsu API SQL.

### <a id="dotnet-cassandra"></a>Interfejs API rozwiązania Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Kolejne kroki

- [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partitioning-overview.md)
- [Jednostki żądań w usłudze Azure Cosmos DB](request-units.md)
- [Aprowizowanie przepływności na kontenerach i baz danych](set-throughput.md)
- [Praca z konta usługi Azure Cosmos](account-overview.md)
