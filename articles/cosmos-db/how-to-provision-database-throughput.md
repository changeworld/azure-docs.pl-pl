---
title: Aprowizowanie przepływności bazy danych w usłudze Azure Cosmos DB
description: Dowiedz się, jak aprowizować przepływność na poziomie bazy danych w usłudze Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 8c2fe8d42ded5a072a2211b102736326554bc4f4
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2019
ms.locfileid: "57239890"
---
# <a name="provision-throughput-for-a-database-in-azure-cosmos-db"></a>Aprowizowanie przepływności dla bazy danych w usłudze Azure Cosmos DB

W tym artykule wyjaśniono, jak aprowizować przepływność dla bazy danych w usłudze Azure Cosmos DB. Przepływność możesz aprowizować dla jednego [kontenera](how-to-provision-container-throughput.md) lub dla bazy danych i udostępniać ją dla kontenerów w tej bazie danych. Aby dowiedzieć się, kiedy należy używać przepływności kontenerów i na poziomie bazy danych, zobacz [przypadki użycia usługi aprowizowania przepływności na kontenerach i baz danych](set-throughput.md) artykułu. Przepływność na poziomie bazy danych możesz aprowizować za pomocą witryny Azure Portal lub zestawów SDK usługi Azure Cosmos DB.

## <a name="provision-throughput-by-using-azure-portal"></a>Aprowizowanie przepływności przy użyciu witryny Azure Portal

### <a id="portal-sql"></a>Interfejs API SQL (podstawowy)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) lub wybierz istniejące konto.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowa baza danych**. Podaj następujące szczegóły:

   * Wprowadź identyfikator bazy danych. 
   * Wybierz pozycję **Zaaprowizuj przepływność**.
   * Wprowadź przepływność (na przykład 1000 jednostek RU).
   * Kliknij przycisk **OK**.

![Zrzut ekranu okna dialogowego Nowa baza danych](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-by-using-net-sdk"></a>Aprowizowanie przepływności przy użyciu zestawu .NET SDK

> [!Note]
> Użyj interfejsu API SQL, aby aprowizować przepływność dla wszystkich interfejsów API. Poniższego przykładu możesz też opcjonalnie użyć w przypadku interfejsu API Cassandra.

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

## <a name="next-steps"></a>Kolejne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat aprowizowania przepływności w usłudze Azure Cosmos DB:

* [How to provision throughput for a container (Jak aprowizować przepływność dla kontenera)](how-to-provision-container-throughput.md)
* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)
