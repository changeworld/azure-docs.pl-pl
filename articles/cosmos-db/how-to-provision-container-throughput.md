---
title: Udostępnianie przepustowości kontenera w Azure Cosmos DB
description: Dowiedz się, jak udostępnić przepływność na poziomie kontenera w Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 8da27773cc74324c1dde5a95de1abef3256c1f1c
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71811680"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Obsługa przepływności w kontenerze usługi Azure Cosmos

W tym artykule wyjaśniono, jak zainicjować przepływność na kontenerze (kolekcji, grafie lub tabeli) w Azure Cosmos DB. Można zainicjować przepływność na jednym kontenerze lub [zainicjować przepływność dla bazy danych](how-to-provision-database-throughput.md) i udostępnić ją między kontenerami w bazie danych. Przepływność można zainicjować przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub Azure Cosmos DB zestawów SDK.

## <a name="provision-throughput-using-azure-portal"></a>Obsługa przepływności przy użyciu Azure Portal

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account)lub Wybierz istniejące konto usługi Azure Cosmos.

1. Otwórz okienko **Eksplorator danych** i wybierz pozycję **Nowa kolekcja**. Następnie podaj następujące informacje:

   * Wskaż, czy tworzysz nową bazę danych czy korzystasz z istniejącej.
   * Wprowadź identyfikator kontenera (lub tabeli lub grafu).
   * Wprowadź wartość klucza partycji (na przykład `/userid`).
   * Wprowadź przepływność, która ma zostać zainicjowana (na przykład 1000 jednostek ru).
   * Wybierz **przycisk OK**.

    ![Zrzut ekranu przedstawiający Eksplorator danych z wyróżnioną nową kolekcją](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Obsługa przepływności przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell

Aby utworzyć kontener z dedykowaną przepływność, zobacz

* [Tworzenie kontenera przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#create-a-container)
* [Tworzenie kontenera przy użyciu programu PowerShell](manage-with-powershell.md#create-container)

> [!Note]
> W przypadku aprowizacji przepływności w kontenerze na koncie usługi Azure Cosmos skonfigurowanym za pomocą interfejsu API Azure Cosmos DB dla MongoDB Użyj `/myShardKey` dla ścieżki klucza partycji. Jeśli przepustowość jest inicjowana w kontenerze na koncie usługi Azure Cosmos skonfigurowanym pod warunkiem interfejs API Cassandra, użyj `/myPrimaryKey` dla ścieżki klucza partycji.

## <a name="provision-throughput-by-using-net-sdk"></a>Obsługa przepływności przy użyciu zestawu .NET SDK

> [!Note]
> Użyj Cosmos SDK dla interfejsu API SQL, aby zainicjować przepływność dla wszystkich Cosmos DB interfejsów API, z wyjątkiem interfejs API Cassandra.

### <a id="dotnet-most"></a>Interfejsy API SQL, MongoDB, Gremlin i Table
### <a name="net-v2-sdk"></a>Zestaw .NET V2 SDK

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

### <a name="net-v3-sdk"></a>Zestaw SDK dla platformy .NET v3
[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

### <a id="dotnet-cassandra"></a>interfejs API Cassandra

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej o aprowizacji przepływności w Azure Cosmos DB:

* [Jak zapewnić przepustowość bazy danych](how-to-provision-database-throughput.md)
* [Jednostki żądań i przepływność w Azure Cosmos DB](request-units.md)
