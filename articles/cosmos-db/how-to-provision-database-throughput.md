---
title: Aprowizowanie przepływności bazy danych w usłudze Azure Cosmos DB
description: Dowiedz się, jak aprowizować przepływność na poziomie bazy danych w usłudze Azure Cosmos DB przy użyciu witryny Azure portal, cli, powershell i różnych innych zestawów SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: ef7d06dfb074a3453f5589284cbdaf079c48d111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933773"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Aprowizuj przepływność bazy danych w usłudze Azure Cosmos DB

W tym artykule wyjaśniono, jak aprowizować przepływność w bazie danych w usłudze Azure Cosmos DB. Można aprowizować przepływność dla pojedynczego [kontenera](how-to-provision-container-throughput.md)lub bazy danych i współużytkować przepływność między kontenerami w nim. Aby dowiedzieć się, kiedy używać poziomu kontenera i przepływności na poziomie bazy danych, zobacz [Use cases for inicjowania obsługi administracyjnej przepływności w kontenerach i bazach danych](set-throughput.md) artykułu. Przepływność na poziomie bazy danych możesz aprowizować za pomocą witryny Azure Portal lub zestawów SDK usługi Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Aprowizowanie przepływności przy użyciu witryny Azure Portal

### <a name="sql-core-api"></a><a id="portal-sql"></a>Interfejs API SQL (podstawowy)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account)lub wybierz istniejące konto usługi Azure Cosmos.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowa baza danych**. Podaj następujące szczegóły:

   * Wprowadź identyfikator bazy danych.
   * Wybierz pozycję **Zaaprowizuj przepływność**.
   * Wprowadź przepływność (na przykład 1000 jednostek RU).
   * Kliknij przycisk **OK**.

    ![Zrzut ekranu okna dialogowego Nowa baza danych](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Aprowizuj przepływność przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell

Aby utworzyć bazę danych z udostępnioną przepływnością zobacz,

* [Tworzenie bazy danych przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Tworzenie bazy danych przy użyciu programu Powershell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Aprowizowanie przepływności przy użyciu zestawu .NET SDK

> [!Note]
> Można użyć pakietów SDK usługi Cosmos dla interfejsu API SQL do inprowizowania przepływności dla wszystkich interfejsów API. Poniższego przykładu możesz też opcjonalnie użyć w przypadku interfejsu API Cassandra.

### <a name="all-apis"></a><a id="dotnet-all"></a>Wszystkie interfejsy API

### <a name="net-v2-sdk"></a>Plik SDK .Net V2

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a name="net-v3-sdk"></a>.Net V3 SDK

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra API
Podobne polecenie można wykonać za pomocą dowolnego sterownika zgodnego ze standardem CQL. 
```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```
 
## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej o aprowizowanej przepływności w usłudze Azure Cosmos DB:

* [Globalne skalowanie aprowizowanej przepływności](scaling-throughput.md)
* [Aprowizacja przepływności kontenerów i baz danych](set-throughput.md)
* [How to provision throughput for a container (Jak aprowizować przepływność dla kontenera)](how-to-provision-container-throughput.md)
* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)
