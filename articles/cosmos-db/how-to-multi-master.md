---
title: Jak skonfigurować wielowzorcowość w usłudze Azure Cosmos DB
description: Dowiedz się, jak skonfigurować wielowzorcowość w aplikacjach w usłudze Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: b862c59002369662d37b6d6a9de28370b0000497
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682274"
---
# <a name="how-to-configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Jak skonfigurować wielowzorcowość w aplikacjach korzystających z usługi Azure Cosmos DB

Aby użyć funkcji wielu wzorców w aplikacji, musisz włączyć zapisów w wielu regionach i skonfigurować międzyregionalnych w usłudze Azure Cosmos DB. Obsługa wielu regionów jest skonfigurowany, ustawiając region, w którym aplikacja jest wdrażana.

## <a id="netv2">Zestaw .NET SDK w wersji 2</a>

Aby włączyć Multi-Master w zestawie aplikacji `UseMultipleWriteLocations` wartość PRAWDA, a następnie skonfiguruj `SetCurrentLocation` do regionu, w której aplikacja jest wdrażana i replikacji usługi Azure Cosmos DB.

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

Aby włączyć Multi-Master w zestawie aplikacji `policy.setUsingMultipleWriteLocations(true)` i skonfigurować `policy.setPreferredLocations` do regionu, w której aplikacja jest wdrażana i Cosmos DB są replikowane.

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

## <a name="next-steps"></a>Kolejne kroki

Następnie można przeczytać następujące artykuły:

* [Utilize session tokens for managing consistency in Azure Cosmos DB (Korzystanie z tokenów sesji do zarządzania spójnością w usłudze Azure Cosmos DB)](how-to-manage-consistency.md#utilize-session-tokens)
* [Conflict types and resolution policies in Azure Cosmos DB (Typy konfliktów i zasady ich rozwiązywania w usłudze Azure Cosmos DB)](conflict-resolution-policies.md)
* [High availability in Azure Cosmos DB (Wysoka dostępność w usłudze Azure Cosmos DB)](high-availability.md)
* [Poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md)
* [Choosing the right consistency level in Azure Cosmos DB (Wybieranie odpowiedniego poziomu spójności w usłudze Azure Cosmos DB)](consistency-levels-choosing.md)
* [Consistency, availability and performance tradeoffs in Azure Cosmos DB (Kompromisy w zakresie spójności, dostępności i wydajności w usłudze Azure Cosmos DB)](consistency-levels-tradeoffs.md)
* [Dostępność i wydajność kompromisy dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Globalnie skalowanie aprowizowana przepływność](scaling-throughput.md)
* [Dystrybucja globalna - kulisy](global-dist-under-the-hood.md)
