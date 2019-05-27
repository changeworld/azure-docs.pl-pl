---
title: Zdefiniuj unikatowe klucze dla kontenera usługi Azure Cosmos
description: Dowiedz się, jak zdefiniować unikatowe klucze dla kontenera usługi Azure Cosmos
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: fb9872d2fd41066899ff9198915d573bfb4a0b84
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240976"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Zdefiniuj unikatowe klucze dla kontenera usługi Azure Cosmos

W tym artykule przedstawiono różne sposoby, aby zdefiniować [unikatowe klucze](unique-keys.md) podczas tworzenia kontenera usługi Azure Cosmos. Jest to obecnie możliwe do wykonania tej operacji za pomocą witryny Azure portal lub za pomocą jednego z zestawów SDK.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account) lub wybierz istniejącą grupę.

1. Otwórz **Eksplorator danych** okienka i wybierz kontener, w którym chcesz pracować.

1. Kliknij pozycję **nowy kontener**.

1. W **Dodaj kontener** okno dialogowe, kliknij pozycję **+ Dodaj Unikatowy klucz** można dodać wpisu klucza unikatowy.

1. Wprowadź ścieżki unikatowe ograniczenie klucza

1. W razie potrzeby dodaj więcej wpisów kluczy unikatowych, klikając **+ Dodaj Unikatowy klucz.**

![Zrzut ekranu przedstawiający wpis unikatowego ograniczenia klucza w witrynie Azure portal](./media/how-to-define-unique-keys/unique-keys-portal.png)

## <a name="use-the-net-sdk-v2"></a>Użyj zestawu SDK platformy .NET w wersji 2

Podczas tworzenia nowego kontenera przy użyciu [zestawu SDK platformy .NET w wersji 2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/), `UniqueKeyPolicy` obiekt może służyć do definiowania ograniczeń unique klucza.

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

## <a name="use-the-java-sdk"></a>Korzystanie z języka Java SDK

Podczas tworzenia nowego kontenera przy użyciu [zestawu Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb), `UniqueKeyPolicy` obiekt może służyć do definiowania ograniczeń unique klucza.

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

## <a name="use-the-nodejs-sdk"></a>Korzystanie z zestawu SDK środowiska Node.js

Podczas tworzenia nowego kontenera przy użyciu [zestawu SDK środowiska Node.js](https://www.npmjs.com/package/@azure/cosmos), `UniqueKeyPolicy` obiekt może służyć do definiowania ograniczeń unique klucza.

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

## <a name="use-the-python-sdk"></a>Korzystanie z języka Python SDK

Podczas tworzenia nowego kontenera przy użyciu [zestawu SDK języka Python](https://pypi.org/project/azure-cosmos/), unikatowych ograniczeń klucza można określić jako część słownika przekazany jako parametr.

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            { 'paths': ['/firstName', '/lastName', '/emailAddress'] },
            { 'paths': ['/address/zipCode'] }
        ]
    }
})
```

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [partycjonowania](partition-data.md)
- Zapoznaj się z [jak działa indeksowanie](index-overview.md)