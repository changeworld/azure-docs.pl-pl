---
title: Definiowanie unikatowych kluczy dla kontenera usługi Azure Cosmos
description: Dowiedz się, jak definiować unikatowe klucze dla kontenera usługi Azure Cosmos
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 313dd07c2b8eeb5684310b57d74053d3cbc1b5e1
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356385"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Definiowanie unikatowych kluczy dla kontenera usługi Azure Cosmos

W tym artykule przedstawiono różne sposoby definiowania [unikatowych kluczy](unique-keys.md) podczas tworzenia kontenera usługi Azure Cosmos. Obecnie jest możliwe wykonanie tej operacji przy użyciu Azure Portal lub jednego z zestawów SDK.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account) lub Wybierz istniejące.

1. Otwórz okienko **Eksplorator danych** i wybierz kontener, w którym chcesz korzystać.

1. Kliknij pozycję **nowy kontener**.

1. W oknie dialogowym **Dodawanie kontenera** kliknij pozycję **+ Dodaj unikatowy klucz** , aby dodać unikatowy wpis klucza.

1. Wprowadź ścieżki unikatowego klucza ograniczenia

1. W razie konieczności Dodaj więcej unikatowych wpisów kluczy, klikając pozycję **+ Dodaj klucz unikatowy** .

![Zrzut ekranu wpisu ograniczenia klucza unikatowego na Azure Portal](./media/how-to-define-unique-keys/unique-keys-portal.png)

## <a name="use-the-net-sdk-v2"></a>Korzystanie z zestawu .NET SDK V2

Podczas tworzenia nowego kontenera przy użyciu [zestawu .NET SDK V2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) `UniqueKeyPolicy` obiekt może służyć do definiowania unikatowych ograniczeń klucza.

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

## <a name="use-the-java-sdk"></a>Korzystanie z zestawu SDK języka Java

Podczas tworzenia nowego kontenera przy użyciu `UniqueKeyPolicy` [zestawu SDK języka Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)obiekt może służyć do definiowania unikatowych ograniczeń klucza.

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

## <a name="use-the-nodejs-sdk"></a>Korzystanie z zestawu SDK środowiska Node. js

Podczas tworzenia nowego kontenera przy użyciu `UniqueKeyPolicy` [zestawu SDK środowiska Node. js](https://www.npmjs.com/package/@azure/cosmos)obiekt może służyć do definiowania unikatowych ograniczeń klucza.

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

## <a name="use-the-python-sdk"></a>Korzystanie z zestawu SDK języka Python

Podczas tworzenia nowego kontenera przy użyciu [zestawu SDK języka Python](https://pypi.org/project/azure-cosmos/)można określić unikalne ograniczenia klucza jako część słownika przekazaną jako parametr.

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

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się [](partition-data.md) więcej na temat partycjonowania
- Zapoznaj się z [działaniem indeksowania](index-overview.md)