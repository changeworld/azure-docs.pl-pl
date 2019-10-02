---
title: Udostępnianie przepływności bazy danych w Azure Cosmos DB
description: Dowiedz się, jak udostępnić przepływność na poziomie bazy danych w Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 93961b44f1c0d063774395ab384cb84b1aa05d99
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71812494"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Zapewnianie przepływności dla bazy danych w Azure Cosmos DB

W tym artykule wyjaśniono, jak zainicjować przepływność dla bazy danych w Azure Cosmos DB. Można zainicjować przepływność dla jednego [kontenera](how-to-provision-container-throughput.md)lub dla bazy danych i udostępnić przepływność między kontenerami w tym kontenerze. Aby dowiedzieć się, kiedy korzystać z przepływności na poziomie kontenera i na poziomie bazy danych, zobacz artykuł [dotyczący użycia dotyczącego przepływności dla kontenerów i baz danych](set-throughput.md) . Przepływność na poziomie bazy danych można zainicjować przy użyciu zestawów SDK Azure Portal lub Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Obsługa przepływności przy użyciu Azure Portal

### <a id="portal-sql"></a>Interfejs API SQL (rdzeń)

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account)lub Wybierz istniejące konto usługi Azure Cosmos.

1. Otwórz okienko **Eksplorator danych** i wybierz pozycję **Nowa baza danych**. Podaj następujące informacje:

   * Wprowadź identyfikator bazy danych.
   * Wybierz pozycję **Obsługa przepływności**.
   * Wprowadź przepływność (na przykład 1000 jednostek ru).
   * Wybierz **przycisk OK**.

    ![Zrzut ekranu przedstawiający okno dialogowe Nowa baza danych](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Obsługa przepływności przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell

Aby utworzyć bazę danych o wspólnej przepływności, zobacz

* [Tworzenie bazy danych przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Tworzenie bazy danych przy użyciu programu PowerShell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Obsługa przepływności przy użyciu zestawu SDK platformy .NET

> [!Note]
> Za pomocą zestawów SDK Cosmos dla interfejsu API SQL można zainicjować obsługę przepływności dla wszystkich interfejsów API. Opcjonalnie można również użyć poniższego przykładu dla interfejs API Cassandra.

### <a id="dotnet-all"></a>Wszystkie interfejsy API

### <a name="net-v2-sdk"></a>Zestaw .NET V2 SDK

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

### <a name="net-v3-sdk"></a>Zestaw SDK dla platformy .NET v3

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

### <a id="dotnet-cassandra"></a>interfejs API Cassandra

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej o aprowizacji przepływności w Azure Cosmos DB:

* [Globalnie skalowanie przepływności aprowizacji](scaling-throughput.md)
* [Obsługa przepływności na kontenerach i bazach danych](set-throughput.md)
* [Jak zainicjować przepływność dla kontenera](how-to-provision-container-throughput.md)
* [Jednostki żądań i przepływność w Azure Cosmos DB](request-units.md)
