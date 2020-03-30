---
title: Definiowanie unikatowych kluczy dla kontenera usługi Azure Cosmos
description: Dowiedz się, jak zdefiniować unikatowe klucze kontenera usługi Azure Cosmos przy użyciu witryny Azure portal, PowerShell, .Net, Java i różnych innych zestawów SDK.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: thweiss
ms.openlocfilehash: fa62495a7b51c9a06a91102299378c15e811eae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74872115"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Definiowanie unikatowych kluczy dla kontenera usługi Azure Cosmos

W tym artykule przedstawiono różne sposoby definiowania [unikatowych kluczy](unique-keys.md) podczas tworzenia kontenera usługi Azure Cosmos. Obecnie można wykonać tę operację za pomocą witryny Azure portal lub za pośrednictwem jednego z zestawów SDK.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account) lub wybierz istniejące.

1. Otwórz okienko **Eksploratora danych** i wybierz kontener, nad który chcesz pracować.

1. Kliknij **nowy kontener**.

1. W oknie dialogowym **Dodawanie kontenera** kliknij przycisk **+ Dodaj unikatowy klucz,** aby dodać unikatowy wpis klucza.

1. Wprowadź ścieżkę(-y) unikatowego ograniczenia klucza

1. W razie potrzeby dodaj więcej unikatowych wpisów kluczy, klikając **+ Dodaj unikatowy klucz**

    ![Zrzut ekranu przedstawiający unikatowy wpis ograniczenia klucza w witrynie Azure portal](./media/how-to-define-unique-keys/unique-keys-portal.png)

## <a name="use-powershell"></a>Korzystanie z programu Powershell

Aby utworzyć kontener z unikatowymi kluczami zobacz, [Utwórz kontener usługi Azure Cosmos z unikatowym kluczem i ttl](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="use-the-net-sdk-v2"></a>Korzystanie z pliku .NET SDK V2

Podczas tworzenia nowego kontenera przy użyciu [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) `UniqueKeyPolicy` obiekt może służyć do definiowania unikatowych ograniczeń klucza.

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    PartitionKey = new PartitionKeyDefinition { Paths = new Collection<string>(new List<string> { "/myPartitionKey" }) },
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "/emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

## <a name="use-the-net-sdk-v3"></a>Użyj pliku .NET SDK V3

Podczas tworzenia nowego kontenera przy użyciu [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)użyj interfejsu API SDK, aby zadeklarować unikatowe klucze w zwięzły i czytelny sposób.

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithUniqueKey()
        .Path("/firstName")
        .Path("/lastName")
        .Path("/emailAddress")
    .Attach()
    .WithUniqueKey()
        .Path("/address/zipCode")
    .Attach()
    .CreateIfNotExistsAsync();
```

## <a name="use-the-java-sdk"></a>Korzystanie z sdk Java

Podczas tworzenia nowego kontenera przy użyciu java `UniqueKeyPolicy` [SDK,](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)obiekt może służyć do definiowania unikatowych ograniczeń klucza.

```java
// create a new DocumentCollection object
DocumentCollection container = new DocumentCollection();
container.setId("container");

// create array of strings and populate them with the unique key paths
Collection<String> uniqueKey1Paths = new ArrayList<String>();
uniqueKey1Paths.add("/firstName");
uniqueKey1Paths.add("/lastName");
uniqueKey1Paths.add("/emailAddress");
Collection<String> uniqueKey2Paths = new ArrayList<String>();
uniqueKey2Paths.add("/address/zipCode");

// create UniqueKey objects and set their paths
UniqueKey uniqueKey1 = new UniqueKey();
UniqueKey uniqueKey2 = new UniqueKey();
uniqueKey1.setPaths(uniqueKey1Paths);
uniqueKey2.setPaths(uniqueKey2Paths);

// create a new UniqueKeyPolicy object and set its unique keys
UniqueKeyPolicy uniqueKeyPolicy = new UniqueKeyPolicy();
Collection<UniqueKey> uniqueKeys = new ArrayList<UniqueKey>();
uniqueKeys.add(uniqueKey1);
uniqueKeys.add(uniqueKey2);
uniqueKeyPolicy.setUniqueKeys(uniqueKeys);

// set the unique key policy
container.setUniqueKeyPolicy(uniqueKeyPolicy);

// create the container
client.createCollection(String.format("/dbs/%s", "database"), container, null);
```

## <a name="use-the-nodejs-sdk"></a>Korzystanie z sdk node.js

Podczas tworzenia nowego kontenera przy użyciu [node.js SDK](https://www.npmjs.com/package/@azure/cosmos) `UniqueKeyPolicy` obiekt może służyć do definiowania unikatowych ograniczeń klucza.

```javascript
client.database('database').containers.create({
    id: 'container',
    uniqueKeyPolicy: {
        uniqueKeys: [
            { paths: ['/firstName', '/lastName', '/emailAddress'] },
            { paths: ['/address/zipCode'] }
        ]
    }
});
```

## <a name="use-the-python-sdk"></a>Korzystanie z modułu SDK języka Python

Podczas tworzenia nowego kontenera przy użyciu [SDK języka Python,](https://pypi.org/project/azure-cosmos/)unikatowe ograniczenia klucza można określić jako część słownika przekazywane jako parametr.

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            {'paths': ['/firstName', '/lastName', '/emailAddress']},
            {'paths': ['/address/zipCode']}
        ]
    }
})
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [partycjonowaniu](partition-data.md)
- Dowiedz [się, jak działa indeksowanie](index-overview.md)
