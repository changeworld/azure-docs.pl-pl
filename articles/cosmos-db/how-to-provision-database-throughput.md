---
title: Aprowizowanie przepływności bazy danych w usłudze Azure Cosmos DB
description: Dowiedz się, jak aprowizować przepływność na poziomie bazy danych w usłudze Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: rimman
ms.openlocfilehash: 29bc65c8afaa1fe4bdc39923bd2219184e8b3a96
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092997"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Zapewnianie przepływności dla bazy danych w Azure Cosmos DB

W tym artykule wyjaśniono, jak zainicjować przepływność dla bazy danych w Azure Cosmos DB. Przepływność możesz aprowizować dla jednego [kontenera](how-to-provision-container-throughput.md) lub dla bazy danych i udostępniać ją dla kontenerów w tej bazie danych. Aby dowiedzieć się, kiedy korzystać z przepływności na poziomie kontenera i na poziomie bazy danych, zobacz artykuł [dotyczący użycia dotyczącego przepływności dla kontenerów i baz danych](set-throughput.md) . Przepływność na poziomie bazy danych możesz aprowizować za pomocą witryny Azure Portal lub zestawów SDK usługi Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Aprowizowanie przepływności przy użyciu witryny Azure Portal

### <a id="portal-sql"></a>Interfejs API SQL (podstawowy)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account)lub Wybierz istniejące konto usługi Azure Cosmos.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowa baza danych**. Podaj następujące szczegóły:

   * Wprowadź identyfikator bazy danych. 
   * Wybierz pozycję **Zaaprowizuj przepływność**.
   * Wprowadź przepływność (na przykład 1000 jednostek RU).
   * Kliknij przycisk **OK**.

![Zrzut ekranu okna dialogowego Nowa baza danych](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)


## <a name="provision-throughput-using-azure-cli"></a>Aprowizowanie przepływności przy użyciu interfejsu wiersza polecenia

```azcli-interactive
az cosmosdb database create --db-name
                            [--key]
                            [--name]
                            [--resource-group-name]
                            [--subscription]
                            [--throughput]
                            [--url-connection]
```




## <a name="provision-throughput-using-powershell"></a>Obsługa przepływności przy użyciu programu PowerShell

```azurepowershell-interactive
# Create a database and provision throughput of 400 RU/s
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$databaseResourceName = $accountName + "/sql/" + $databaseName

$databaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"= 400 }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseResourceName -PropertyObject $databaseProperties
```

## <a name="provision-throughput-using-net-sdk"></a>Aprowizowanie przepływności przy użyciu zestawu .NET SDK

> [!Note]
> Za pomocą zestawów SDK Cosmos dla interfejsu API SQL można zainicjować obsługę przepływności dla wszystkich interfejsów API. Poniższego przykładu możesz też opcjonalnie użyć w przypadku interfejsu API Cassandra.

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

### <a id="dotnet-cassandra"></a>Interfejs API rozwiązania Cassandra

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej o aprowizacji przepływności w Azure Cosmos DB:

* [Globalnie skalowanie przepływności aprowizacji](scaling-throughput.md)
* [Obsługa przepływności na kontenerach i bazach danych](set-throughput.md)
* [How to provision throughput for a container (Jak aprowizować przepływność dla kontenera)](how-to-provision-container-throughput.md)
* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)
