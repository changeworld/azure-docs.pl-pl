---
title: Aprowizowanie przepływności bazy danych w usłudze Azure Cosmos DB
description: Dowiedz się, jak aprowizować przepływność na poziomie bazy danych w usłudze Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: d73dd5ffe8cc8ed00288209b628d7175b795b335
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243769"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Aprowizowanie przepływności w bazie danych w usłudze Azure Cosmos DB

W tym artykule wyjaśniono, jak aprowizować przepływność mierzoną w bazie danych w usłudze Azure Cosmos DB. Przepływność możesz aprowizować dla jednego [kontenera](how-to-provision-container-throughput.md) lub dla bazy danych i udostępniać ją dla kontenerów w tej bazie danych. Aby dowiedzieć się, kiedy należy używać przepływności kontenerów i na poziomie bazy danych, zobacz [przypadki użycia usługi aprowizowania przepływności na kontenerach i baz danych](set-throughput.md) artykułu. Przepływność na poziomie bazy danych możesz aprowizować za pomocą witryny Azure Portal lub zestawów SDK usługi Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Aprowizowanie przepływności przy użyciu witryny Azure Portal

### <a id="portal-sql"></a>Interfejs API SQL (podstawowy)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account), lub wybierz istniejące konto usługi Azure Cosmos.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowa baza danych**. Podaj następujące szczegóły:

   * Wprowadź identyfikator bazy danych. 
   * Wybierz pozycję **Zaaprowizuj przepływność**.
   * Wprowadź przepływność (na przykład 1000 jednostek RU).
   * Kliknij przycisk **OK**.

![Zrzut ekranu okna dialogowego Nowa baza danych](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-net-sdk"></a>Aprowizowanie przepływności przy użyciu zestawu .NET SDK

> [!Note]
> Umożliwia Cosmos zestawy SDK dla interfejsu API SQL aprowizowanie przepływności dla wszystkich interfejsów API. Poniższego przykładu możesz też opcjonalnie użyć w przypadku interfejsu API Cassandra.

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

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat aprowizowanej przepływności w usłudze Azure Cosmos DB:

* [Skaluj globalnie aprowizowana przepływność](scaling-throughput.md)
* [Aprowizowanie przepływności na kontenerach i baz danych](set-throughput.md)
* [How to provision throughput for a container (Jak aprowizować przepływność dla kontenera)](how-to-provision-container-throughput.md)
* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)