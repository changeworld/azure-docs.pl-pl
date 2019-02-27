---
title: Jak skonfigurować wielowzorcowość w usłudze Azure Cosmos DB
description: Dowiedz się, jak skonfigurować wielowzorcowość w aplikacjach w usłudze Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 2/12/2019
ms.author: mjbrown
ms.openlocfilehash: 84c8e2921602bb653c0b1ef0adffd3d89e91bd78
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312144"
---
# <a name="how-to-configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Jak skonfigurować wielowzorcowość w aplikacjach korzystających z usługi Azure Cosmos DB

Aby używać funkcji wielowzorcowości w aplikacjach, należy włączyć zapisy w wielu regionach i skonfigurować możliwość wieloadresowości. Wieloadresowość jest konfigurowana przez ustawienie bieżącego regionu wdrożenia aplikacji.

## <a id="netv2">Zestaw .NET SDK w wersji 2</a>

Aby włączyć wielowzorcowość w aplikacjach, ustaw parametr `UseMultipleWriteLocations` na wartość true i skonfiguruj parametr `SetCurrentLocation` jako region, w którym aplikacja jest wdrażana, a usługa Azure Cosmos DB replikowana.

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a id="netv3"></a>Zestaw .NET SDK w wersji 3 (wersja zapoznawcza)

Aby włączyć wielowzorcowość w aplikacjach, skonfiguruj parametr `UseCurrentRegion` na region, w którym aplikacja jest wdrażana, a usługa Cosmos DB replikowana.

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>Java Async SDK

Aby włączyć wielowzorcowość w aplikacjach, ustaw parametr `policy.setUsingMultipleWriteLocations(true)` na wartość true i skonfiguruj parametr `policy.setPreferredLocations` na region, w którym aplikacja jest wdrażana, a usługa Cosmos DB replikowana.

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a id="javascript"></a>Node.js, JavaScript, TypeScript SDK

Aby włączyć wielowzorcowość w aplikacjach, ustaw parametr `connectionPolicy.UseMultipleWriteLocations` na wartość true i skonfiguruj parametr `connectionPolicy.PreferredLocations` na region, w którym aplikacja jest wdrażana, a usługa Cosmos DB replikowana.

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a id="python"></a>Python SDK

Aby włączyć wielowzorcowość w aplikacjach, ustaw parametr `connection_policy.UseMultipleWriteLocations` na wartość true i skonfiguruj parametr `connection_policy.PreferredLocations` na region, w którym aplikacja jest wdrażana, a usługa Cosmos DB replikowana.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat wielowzorcowości, dystrybucji globalnej i spójności w usłudze Azure Cosmos DB. Zobacz następujące artykuły:

* [Utilize session tokens for managing consistency in Azure Cosmos DB (Korzystanie z tokenów sesji do zarządzania spójnością w usłudze Azure Cosmos DB)](how-to-manage-consistency.md#utilize-session-tokens)

* [Conflict types and resolution policies in Azure Cosmos DB (Typy konfliktów i zasady ich rozwiązywania w usłudze Azure Cosmos DB)](conflict-resolution-policies.md)

* [High availability in Azure Cosmos DB (Wysoka dostępność w usłudze Azure Cosmos DB)](high-availability.md)

* [Choosing the right consistency level in Azure Cosmos DB (Wybieranie odpowiedniego poziomu spójności w usłudze Azure Cosmos DB)](consistency-levels-choosing.md)

* [Consistency, availability and performance tradeoffs in Azure Cosmos DB (Kompromisy w zakresie spójności, dostępności i wydajności w usłudze Azure Cosmos DB)](consistency-levels-tradeoffs.md)
