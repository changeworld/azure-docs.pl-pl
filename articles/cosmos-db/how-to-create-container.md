---
title: Tworzenie kontenera w Azure Cosmos DB
description: Dowiedz się, jak utworzyć kontener w Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 9805ff9aa4932c262db13c47fd2e442b3d3d676f
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71811737"
---
# <a name="create-an-azure-cosmos-container"></a>Tworzenie kontenera usługi Azure Cosmos

W tym artykule opisano różne sposoby tworzenia kontenera usługi Azure Cosmos (kolekcji, tabeli lub grafów). W tym celu można użyć Azure Portal, interfejsu wiersza polecenia platformy Azure lub obsługiwanych zestawów SDK. W tym artykule pokazano, jak utworzyć kontener, określić klucz partycji i zapewnić przepływność.

## <a name="create-a-container-using-azure-portal"></a>Tworzenie kontenera przy użyciu Azure Portal

### <a id="portal-sql"></a>INTERFEJS API SQL

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account)lub Wybierz istniejące konto.

1. Otwórz okienko **Eksplorator danych** i wybierz pozycję **nowy kontener**. Następnie podaj następujące informacje:

   * Wskaż, czy tworzysz nową bazę danych czy korzystasz z istniejącej.
   * Wprowadź identyfikator kontenera.
   * Wprowadź klucz partycji.
   * Wprowadź przepływność, która ma zostać zainicjowana (na przykład 1000 jednostek ru).
   * Wybierz **przycisk OK**.

    ![Zrzut ekranu przedstawiający okienko Eksplorator danych z wyróżnionym nowym kontenerem](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>Interfejs API Azure Cosmos DB dla MongoDB

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account)lub Wybierz istniejące konto.

1. Otwórz okienko **Eksplorator danych** i wybierz pozycję **nowy kontener**. Następnie podaj następujące informacje:

   * Wskaż, czy tworzysz nową bazę danych czy korzystasz z istniejącej.
   * Wprowadź identyfikator kontenera.
   * Wprowadź klucz fragmentu.
   * Wprowadź przepływność, która ma zostać zainicjowana (na przykład 1000 jednostek ru).
   * Wybierz **przycisk OK**.

    ![Zrzut ekranu przedstawiający interfejs Azure Cosmos DB API dla MongoDB, okno dialogowe Dodawanie kontenera](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>interfejs API Cassandra

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-cassandra-dotnet.md#create-a-database-account)lub Wybierz istniejące konto.

1. Otwórz okienko **Eksplorator danych** i wybierz pozycję **Nowa tabela**. Następnie podaj następujące informacje:

   * Wskaż, czy tworzysz nową przestrzeń kluczy lub korzystając z istniejącej.
   * Wprowadź nazwę tabeli.
   * Wprowadź właściwości i określ klucz podstawowy.
   * Wprowadź przepływność, która ma zostać zainicjowana (na przykład 1000 jednostek ru).
   * Wybierz **przycisk OK**.

    ![Zrzut ekranu przedstawiający interfejs API Cassandra, okno dialogowe Dodawanie tabeli](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> W przypadku interfejs API Cassandra klucz podstawowy jest używany jako klucz partycji.

### <a id="portal-gremlin"></a>Interfejs API Gremlin

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-graph-dotnet.md#create-a-database-account)lub Wybierz istniejące konto.

1. Otwórz okienko **Eksplorator danych** i wybierz pozycję **Nowy wykres**. Następnie podaj następujące informacje:

   * Wskaż, czy tworzysz nową bazę danych, czy korzystasz z istniejącej.
   * Wprowadź identyfikator grafu.
   * Wybierz **nieograniczoną** pojemność magazynu.
   * Wprowadź klucz partycji dla wierzchołków.
   * Wprowadź przepływność, która ma zostać zainicjowana (na przykład 1000 jednostek ru).
   * Wybierz **przycisk OK**.

    ![Zrzut ekranu przedstawiający interfejs API Gremlin, okno dialogowe Dodawanie wykresu](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>interfejs API tabel

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-table-dotnet.md#create-a-database-account)lub Wybierz istniejące konto.

1. Otwórz okienko **Eksplorator danych** i wybierz pozycję **Nowa tabela**. Następnie podaj następujące informacje:

   * Wprowadź identyfikator tabeli.
   * Wprowadź przepływność, która ma zostać zainicjowana (na przykład 1000 jednostek ru).
   * Wybierz **przycisk OK**.

    ![Zrzut ekranu przedstawiający interfejs API tabel, okno dialogowe Dodawanie tabeli](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> W przypadku interfejs API tabel klucz partycji jest określany za każdym razem, gdy dodasz nowy wiersz.

## Tworzenie kontenera przy użyciu interfejsu wiersza polecenia platformy Azure<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

Poniższe linki pokazują, jak utworzyć zasoby kontenera dla Azure Cosmos DB przy użyciu interfejsu wiersza polecenia platformy Azure.

Aby zapoznać się z listą wszystkich przykładów interfejsu wiersza polecenia platformy Azure dla wszystkich Azure Cosmos DB interfejsów API, zobacz, interfejs API [SQL](cli-samples.md), [interfejs API Cassandra](cli-samples-cassandra.md), [interfejs API MongoDB](cli-samples-mongodb.md), [interfejs API Gremlin](cli-samples-gremlin.md)i [interfejs API tabel](cli-samples-table.md)

* [Tworzenie kontenera za pomocą interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#create-a-container)
* [Tworzenie kolekcji dla Azure Cosmos DB interfejsu API MongoDB w interfejsie wiersza polecenia platformy Azure](/scripts/cli/mongodb/create.md)
* [Tworzenie tabeli Cassandra przy użyciu interfejsu wiersza polecenia platformy Azure](/scripts/cli/cassandra/create.md)
* [Tworzenie grafu Gremlin przy użyciu interfejsu wiersza polecenia platformy Azure](/scripts/cli/gremlin/create.md)
* [Tworzenie tabeli interfejs API tabel przy użyciu interfejsu wiersza polecenia platformy Azure](/scripts/cli/table/create.md)

## Tworzenie kontenera przy użyciu programu<a id="ps-sql"></a>PowerShell @no__t-<a id="ps-cassandra"></a>1 <a id="ps-gremlin"><a id="ps-table"></a>

Poniższe linki pokazują, jak utworzyć zasoby kontenera dla Azure Cosmos DB przy użyciu programu PowerShell.

Aby zapoznać się z listą wszystkich przykładów interfejsu wiersza polecenia platformy Azure dla wszystkich Azure Cosmos DB interfejsów API, zobacz, interfejs API [SQL](powershell-samples-sql.md), [interfejs API Cassandra](powershell-samples-cassandra.md), [interfejs API MongoDB](powershell-samples-mongodb.md), [interfejs API Gremlin](powershell-samples-gremlin.md)i [interfejs API tabel](powershell-samples-table.md)

* [Tworzenie kontenera przy użyciu programu PowerShell](manage-with-powershell.md#create-container)
* [Tworzenie kolekcji dla Azure Cosmos DB interfejsu API MongoDB przy użyciu programu PowerShell](/scripts/powershell/mongodb/ps-mongodb-create.md)
* [Tworzenie tabeli Cassandra przy użyciu programu PowerShell](/scripts/powershell/cassandra/ps-cassandra-create.md)
* [Tworzenie grafu Gremlin przy użyciu programu PowerShell](/scripts/powershell/gremlin/ps-gremlin-create.md)
* [Tworzenie tabeli interfejs API tabel przy użyciu programu PowerShell](/scripts/powershell/table/ps-table-create.md)

## <a name="create-a-container-using-net-sdk"></a>Tworzenie kontenera przy użyciu zestawu SDK platformy .NET

### <a id="dotnet-sql-graph"></a>Interfejsy API SQL i interfejs API Gremlin

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

### <a id="dotnet-mongodb"></a>Interfejs API Azure Cosmos DB dla MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> Protokół MongoDB Wire nie rozumie koncepcji [jednostek żądania](request-units.md). Aby utworzyć nową kolekcję z zainicjowaną przepływność, użyj Azure Portal lub Cosmos DB zestawów SDK dla interfejsu API SQL.

### <a id="dotnet-cassandra"></a>interfejs API Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Następne kroki

* [Partycjonowanie w Azure Cosmos DB](partitioning-overview.md)
* [Jednostki żądań w Azure Cosmos DB](request-units.md)
* [Obsługa przepływności na kontenerach i bazach danych](set-throughput.md)
* [Współpraca z kontem usługi Azure Cosmos](account-overview.md)
