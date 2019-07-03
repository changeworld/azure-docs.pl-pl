---
title: Jak skonfigurować wielowzorcowość w usłudze Azure Cosmos DB
description: Dowiedz się, jak skonfigurować Multi-Master w aplikacji w usłudze Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/02/2019
ms.author: mjbrown
ms.openlocfilehash: 73b4ada713e264aaa2504fe4d4f504e07ae45181
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538080"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Konfigurowanie Multi-Master w aplikacjach korzystających z usługi Azure Cosmos DB

Po utworzeniu konta usługi z wieloma regionami zapisu włączone należy dwie zmiany w aplikacji, aby ConnectionPolicy dla obiektu DocumentClient włączyć funkcje wielu wzorców i z obsługą wielu regionów, w usłudze Azure Cosmos DB. W ramach ConnectionPolicy Ustaw UseMultipleWriteLocations to true i przekazać nazwę regionu, w którym aplikacja jest wdrażana SetCurrentLocation. Spowoduje to wypełnienie właściwość PreferredLocations na podstawie zbliżenia geograficznej lokalizacji przekazanej. Jeśli do konta później zostanie dodany nowy region, aplikacja ma zostać zaktualizowane lub ponownego wdrażania, automatycznie wykryje bliżej region i zostanie automatycznie domu się ona wystąpi zdarzenie regionalne.

> [!TIP]
> Konta usługi cosmos nie utworzyć wstępnie skonfigurowane dla Multi-Master można migrować bez przestoju. Aby dowiedzieć się więcej, zobacz [skonfigurować zapisu wielu regionów](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a id="netv2">Zestaw .NET SDK w wersji 2</a>

Aby włączyć Multi-Master w aplikacji, ustaw `UseMultipleWriteLocations` do `true`. Ponadto należy ustawić `SetCurrentLocation` w regionie, w których aplikacja jest wdrażana i których usługi Azure Cosmos DB są replikowane:

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

Aby włączyć Multi-Master w aplikacji, ustaw `UseCurrentRegion` w regionie, w których aplikacja jest wdrażana i których usługi Cosmos DB są replikowane:

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>Java Async SDK

Aby włączyć Multi-Master w aplikacji, ustaw `policy.setUsingMultipleWriteLocations(true)` i ustaw `policy.setPreferredLocations` w regionie, w których aplikacja jest wdrażana i których usługi Cosmos DB są replikowane:

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

## <a id="javascript"></a>Środowisko node.js, JavaScript i TypeScript SDK

Aby włączyć Multi-Master w aplikacji, ustaw `connectionPolicy.UseMultipleWriteLocations` do `true`. Ponadto należy ustawić `connectionPolicy.PreferredLocations` w regionie, w których aplikacja jest wdrażana i których usługi Cosmos DB są replikowane:

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

Aby włączyć Multi-Master w aplikacji, ustaw `connection_policy.UseMultipleWriteLocations` do `true`. Ponadto należy ustawić `connection_policy.PreferredLocations` w regionie, w których aplikacja jest wdrażana i których usługi Cosmos DB są replikowane.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj następujące artykuły:

* [Używanie tokenów sesji do zarządzania spójności w usłudze Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Conflict types and resolution policies in Azure Cosmos DB (Typy konfliktów i zasady ich rozwiązywania w usłudze Azure Cosmos DB)](conflict-resolution-policies.md)
* [High availability in Azure Cosmos DB (Wysoka dostępność w usłudze Azure Cosmos DB)](high-availability.md)
* [Poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md)
* [Wybierz poziom spójności w prawo w usłudze Azure Cosmos DB](consistency-levels-choosing.md)
* [Kompromisy w zakresie spójności, dostępności i wydajności w usłudze Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Dostępność i wydajność kompromisy dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Globalnie skalowanie aprowizowana przepływność](scaling-throughput.md)
* [Dystrybucja globalna: Under the hood (Za kulisami usługi Azure RMS — działanie)](global-dist-under-the-hood.md)
