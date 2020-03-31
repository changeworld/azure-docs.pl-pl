---
title: Tworzenie kontenera w usłudze Azure Cosmos DB
description: Dowiedz się, jak utworzyć kontener w usłudze Azure Cosmos DB przy użyciu witryny Azure portal, .Net, Java, Python, Node.js i innych zestawów SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 4eaa2974817bfcd8bef83e5139d75a2d4c2ec107
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873713"
---
# <a name="create-an-azure-cosmos-container"></a>Tworzenie kontenera usługi Azure Cosmos

W tym artykule opisano różne sposoby tworzenia kontenera usługi Azure Cosmos (kolekcja, tabela lub wykres). W tym celu można użyć witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub obsługiwanych zestawów SDK. W tym artykule pokazano, jak utworzyć kontener, określić klucz partycji i aprowizować przepływność.

## <a name="create-a-container-using-azure-portal"></a>Tworzenie kontenera przy użyciu witryny Azure Portal

### <a name="sql-api"></a><a id="portal-sql"></a>Interfejs API SQL

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account)lub wybierz istniejące konto.

1. Otwórz okienko **Eksploratora danych** i wybierz pozycję **Nowy kontener**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową bazę danych, czy używasz istniejącej.
   * Wprowadź identyfikator kontenera.
   * Wprowadź klucz partycji.
   * Wprowadź przepływność, która ma być aprowizowana (na przykład 1000 procesorów RU).
   * Kliknij przycisk **OK**.

    ![Zrzut ekranu przedstawiający okienko Eksploratora danych z wyróżnioną pojednawką Nowy kontener](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="portal-mongodb"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account)lub wybierz istniejące konto.

1. Otwórz okienko **Eksploratora danych** i wybierz pozycję **Nowy kontener**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową bazę danych, czy używasz istniejącej.
   * Wprowadź identyfikator kontenera.
   * Wprowadź klucz fragmentu.
   * Wprowadź przepływność, która ma być aprowizowana (na przykład 1000 procesorów RU).
   * Kliknij przycisk **OK**.

    ![Zrzut ekranu przedstawiający interfejs API bazy danych usługi Azure Cosmos DB dla usługi MongoDB, okno dialogowe Dodawanie kontenera](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a name="cassandra-api"></a><a id="portal-cassandra"></a>Cassandra API

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-cassandra-dotnet.md#create-a-database-account)lub wybierz istniejące konto.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowa tabela**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową przestrzeń kluczy, czy używasz istniejącej.
   * Wprowadź nazwę tabeli.
   * Wprowadź właściwości i określ klucz podstawowy.
   * Wprowadź przepływność, która ma być aprowizowana (na przykład 1000 procesorów RU).
   * Kliknij przycisk **OK**.

    ![Zrzut ekranu interfejsu API Cassandra, okno dialogowe Dodawanie tabeli](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> W przypadku interfejsu API rozwiązania Cassandra jako klucz partycji jest używany klucz podstawowy.

### <a name="gremlin-api"></a><a id="portal-gremlin"></a>Interfejs API języka Gremlin

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-graph-dotnet.md#create-a-database-account)lub wybierz istniejące konto.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowy graf**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową bazę danych, czy używasz istniejącej.
   * Wprowadź identyfikator wykresu.
   * Wybierz pojemność magazynu **Bez ograniczeń**.
   * Wprowadź klucz partycji dla wierzchołków.
   * Wprowadź przepływność, która ma być aprowizowana (na przykład 1000 procesorów RU).
   * Kliknij przycisk **OK**.

    ![Zrzut ekranu interfejsu API Gremlin, okno dialogowe Dodawanie grafu](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a name="table-api"></a><a id="portal-table"></a>Interfejs API tabel

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-table-dotnet.md#create-a-database-account)lub wybierz istniejące konto.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowa tabela**. Następnie podaj następujące szczegóły:

   * Wprowadź identyfikator tabeli.
   * Wprowadź przepływność, która ma być aprowizowana (na przykład 1000 procesorów RU).
   * Kliknij przycisk **OK**.

    ![Zrzut ekranu interfejsu API tabel, okno dialogowe Dodawanie tabeli](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> W przypadku interfejsu API tabel klucz partycji jest określany każdorazowo podczas dodawania nowego wiersza.

## <a name="create-a-container-using-azure-cli"></a>Tworzenie kontenera przy użyciu interfejsu wiersza polecenia platformy Azure<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

Poniższe łącza pokazują, jak utworzyć zasoby kontenera dla usługi Azure Cosmos DB przy użyciu interfejsu wiersza polecenia platformy Azure.

Aby uzyskać listę wszystkich przykładów interfejsu wiersza polecenia platformy Azure we wszystkich interfejsach API usługi Azure Cosmos DB, zobacz [INTERFEJS API SQL](cli-samples.md), interfejs API [Cassandra](cli-samples-cassandra.md), [interfejs API mongodb,](cli-samples-mongodb.md) [interfejs API gremlin](cli-samples-gremlin.md)i [interfejs API tabeli](cli-samples-table.md)

* [Tworzenie kontenera za pomocą interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#create-a-container)
* [Tworzenie kolekcji dla usługi Azure Cosmos DB dla interfejsu API usługi MongoDB za pomocą interfejsu wiersza polecenia platformy Azure](./scripts/cli/mongodb/create.md)
* [Tworzenie tabeli Cassandra za pomocą interfejsu wiersza polecenia platformy Azure](./scripts/cli/cassandra/create.md)
* [Tworzenie wykresu Gremlin za pomocą interfejsu wiersza polecenia platformy Azure](./scripts/cli/gremlin/create.md)
* [Tworzenie tabeli interfejsu API tabeli za pomocą interfejsu wiersza polecenia platformy Azure](./scripts/cli/table/create.md)

## <a name="create-a-container-using-powershella-idps-mongodba-idps-gremlin"></a>Tworzenie kontenera przy użyciu programu PowerShell<a id="ps-sql"></a><a id="ps-mongodb"><a id="ps-cassandra"></a><a id="ps-gremlin"><a id="ps-table"></a>

Poniższe łącza pokazują, jak utworzyć zasoby kontenera dla usługi Azure Cosmos DB przy użyciu programu PowerShell.

Aby uzyskać listę wszystkich przykładów interfejsu wiersza polecenia platformy Azure we wszystkich interfejsach API usługi Azure Cosmos DB, zobacz [INTERFEJS API SQL](powershell-samples-sql.md), interfejs API [Cassandra](powershell-samples-cassandra.md), [interfejs API mongodb,](powershell-samples-mongodb.md) [interfejs API gremlin](powershell-samples-gremlin.md)i [interfejs API tabeli](powershell-samples-table.md)

* [Tworzenie kontenera za pomocą programu Powershell](manage-with-powershell.md#create-container)
* [Tworzenie kolekcji dla usługi Azure Cosmos DB dla interfejsu API mongodb za pomocą programu Powershell](./scripts/powershell/mongodb/ps-mongodb-create.md)
* [Tworzenie tabeli Cassandra za pomocą programu Powershell](./scripts/powershell/cassandra/ps-cassandra-create.md)
* [Tworzenie wykresu Gremlin za pomocą programu Powershell](./scripts/powershell/gremlin/ps-gremlin-create.md)
* [Tworzenie tabeli interfejsu API tabeli za pomocą programu PowerShell](./scripts/powershell/table/ps-table-create.md)

## <a name="create-a-container-using-net-sdk"></a>Tworzenie kontenera przy użyciu zestawu .NET SDK

### <a name="sql-api-and-gremlin-api"></a><a id="dotnet-sql-graph"></a>Interfejs API SQL i interfejs API języka Gremlin

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

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="dotnet-mongodb"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> Protokół przewodowy MongoDB nie rozumie pojęcia [jednostek żądających](request-units.md). Aby utworzyć nową kolekcję z aprowizowaną przepływnością, użyj witryny Azure portal lub zestawów SDK usługi Cosmos DB dla interfejsu API SQL.

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Następne kroki

* [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partitioning-overview.md)
* [Jednostki żądania w usłudze Azure Cosmos DB](request-units.md)
* [Aprowizacja przepływności kontenerów i baz danych](set-throughput.md)
* [Praca z kontem usługi Azure Cosmos](account-overview.md)
