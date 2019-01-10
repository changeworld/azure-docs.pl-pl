---
title: Aprowizowanie przepływności bazy danych w usłudze Azure Cosmos DB
description: Dowiedz się, jak aprowizować przepływność na poziomie bazy danych w usłudze Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 7c253141e0e6e76f845d08e68a1d79949fe811e8
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034237"
---
# <a name="provision-throughput-for-a-database-in-azure-cosmos-db"></a>Aprowizowanie przepływności dla bazy danych w usłudze Azure Cosmos DB

W tym artykule wyjaśniono, jak aprowizować przepływność dla bazy danych w usłudze Azure Cosmos DB. Przepływność możesz aprowizować dla jednego [kontenera](how-to-provision-container-throughput.md) lub dla bazy danych i udostępniać ją dla kontenerów w tej bazie danych. Przepływność na poziomie bazy danych możesz aprowizować za pomocą witryny Azure Portal lub zestawów SDK usługi Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Aprowizowanie przepływności przy użyciu witryny Azure Portal

### <a id="portal-sql"></a>Interfejs API SQL (podstawowy)

1. Zaloguj się w [portalu Azure](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) lub wybierz istniejące.

1. Otwórz okienko **Eksplorator danych** i wybierz pozycję **Nowa baza danych**. Następnie wypełnij formularz przy użyciu następujących informacji:

   * Wprowadź identyfikator bazy danych. 
   * Wybierz pozycję Zaaprowizuj przepływność.
   * Wprowadź przepływność, na przykład 1000 jednostek RU.
   * Kliknij przycisk **OK**.

![Aprowizowanie przepływności bazy danych za pomocą interfejsu API SQL](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-net-sdk"></a>Aprowizowanie przepływności przy użyciu zestawu .NET SDK

> [!Note]
> Użyj interfejsu API SQL, aby aprowizować przepływność dla wszystkich interfejsów API. Poniższego przykładu możesz też opcjonalnie użyć w przypadku interfejsu API rozwiązania Cassandra.

### <a id="dotnet-all"></a>Wszystkie interfejsy API

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 10000
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a id="dotnet-cassandra"></a>Interfejs API rozwiązania Cassandra

```csharp
// Create a Cassandra keyspace and provision throughput of 10000 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=10000);
```

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat aprowizowania przepływności w usłudze Cosmos DB:

* [How to provision throughput for a container (Jak aprowizować przepływność dla kontenera)](how-to-provision-container-throughput.md)
* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)
