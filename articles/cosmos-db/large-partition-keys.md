---
title: Utwórz kontenery usługi Azure Cosmos z dużym kluczem partycji przy użyciu Azure Portal i różnych zestawów SDK.
description: Dowiedz się, jak utworzyć kontener w Azure Cosmos DB z dużym kluczem partycji przy użyciu Azure Portal i różnych zestawów SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: a1216daade2df832b606fceb648fca998c3fdec8
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300135"
---
# <a name="create-containers-with-large-partition-key"></a>Tworzenie kontenerów z dużym kluczem partycji

Azure Cosmos DB stosuje schemat partycjonowania oparty na skrócie, aby osiągnąć skalowanie danych w poziomie. Wszystkie kontenery usługi Azure Cosmos utworzone przed maja 3 2019 używają funkcji skrótu, która oblicza wartość skrótu na podstawie pierwszych 100 bajtów klucza partycji. Jeśli istnieje wiele kluczy partycji, które mają te same pierwsze 100 bajtów, te partycje logiczne są traktowane jako te same partycje logiczne przez usługę. Może to prowadzić do problemów, takich jak nieprawidłowy limit przydziału rozmiaru partycji i unikatowe indeksy stosowane w ramach kluczy partycji. Wprowadzono duże klucze partycji w celu rozwiązania tego problemu. Azure Cosmos DB teraz obsługuje duże klucze partycji z wartościami do 2 KB.

Duże klucze partycji są obsługiwane przy użyciu funkcji rozszerzonej wersji funkcji skrótu, która może generować unikatowy skrót z dużych kluczy partycji do 2 KB. Ta wersja skrótu jest również zalecana w przypadku scenariuszy o dużej kardynalności klucza partycji, niezależnie od rozmiaru klucza partycji. Kardynalność klucza partycji jest definiowana jako liczba unikatowych partycji logicznych, na przykład w kolejności ~ 30000 partycji logicznych w kontenerze. W tym artykule opisano sposób tworzenia kontenera z dużym kluczem partycji przy użyciu Azure Portal i różnych zestawów SDK. 

## <a name="create-a-large-partition-key-net-sdk"></a>Tworzenie dużego klucza partycji (zestaw SDK dla platformy .NET)

Aby utworzyć kontener z dużym kluczem partycji przy użyciu zestawu .NET SDK, określ `PartitionKeyDefinitionVersion.V2` właściwość. Poniższy przykład pokazuje, jak określić Właściwość Version w obiekcie PartitionKeyDefinition i ustawić ją na PartitionKeyDefinitionVersion. v2.

### <a name="v3-net-sdk"></a>zestaw SDK v3 .NET

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2, 
    })
```

### <a name="v2-net-sdk"></a>zestaw SDK dla systemu v2

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```

## <a name="create-a-large-partition-key-azure-portal"></a>Tworzenie dużego klucza partycji (Azure Portal) 

Aby utworzyć duży klucz partycji, podczas tworzenia nowego kontenera przy użyciu Azure Portal zaznacz opcję **mój klucz partycji jest większy niż 100-bajtów** . Domyślnie wszystkie nowe kontenery są uwzględniane przy użyciu dużych kluczy partycji. Usuń zaznaczenie pola wyboru, jeśli nie są potrzebne duże klucze partycji lub aplikacje działające w wersji SDK starszej niż 1,18.

![Tworzenie dużych kluczy partycji przy użyciu Azure Portal](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Tworzenie dużego klucza partycji (program PowerShell)

Aby utworzyć kontener z dużym kluczem partycji przy użyciu programu PowerShell, Dołącz `"version" = 2` dla tego `partitionKey` obiektu.

```azurepowershell-interactive
# Create a Cosmos SQL API container with large partition key support (version 2)
$resourceGroupName = "myResourceGroup"
$containerName = "mycosmosaccount" + "/sql/" + "myDatabase" + "/" + "myContainer"

# Container with large partition key support (version = 2)
$containerProperties = @{
  "resource"=@{
    "id"=$containerName;
    "partitionKey"=@{
        "paths"=@("/myPartitionKey");
        "kind"="Hash";
        "version" = 2
    };
    "indexingPolicy"=@{
        "indexingMode"="Consistent";
        "includedPaths"= @(@{
            "path"="/*"
        });
        "excludedPaths"= @(@{
            "path"="/myPathToNotIndex/*"
        })
    }
  }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerName -PropertyObject $containerProperties
```

## <a name="supported-sdk-versions"></a>Obsługiwane wersje zestawu SDK

Klucze dużych partycji są obsługiwane z następującymi minimalnymi wersjami zestawów SDK:

|Typ zestawu SDK  | Wersja minimalna   |
|---------|---------|
|.Net     |    1,18     |
|Synchronizacja Java     |   2.4.0      |
|Asynchroniczne środowisko Java   |  2.5.0        |
| Interfejs API REST | wersja wyższa niż `2017-05-03` przy `x-ms-version` użyciu nagłówka żądania.|

Obecnie nie można używać kontenerów z dużym kluczem partycji w ramach Power BI i Azure Logic Apps. Z tych aplikacji można korzystać z kontenerów bez dużego klucza partycji.

## <a name="next-steps"></a>Następne kroki

* [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partitioning-overview.md)
* [Jednostki żądania w usłudze Azure Cosmos DB](request-units.md)
* [Obsługa przepływności na kontenerach i bazach danych](set-throughput.md)
* [Współpraca z kontem usługi Azure Cosmos](account-overview.md)


