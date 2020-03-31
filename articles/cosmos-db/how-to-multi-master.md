---
title: Jak skonfigurować wielowzorcowość w usłudze Azure Cosmos DB
description: Dowiedz się, jak skonfigurować wiele wzorca dla aplikacji przy użyciu różnych zestawów SDK w usłudze Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 654baed649093add2aa62f4ba81bf6ce7c3e0df5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873645"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Konfigurowanie wielu wzorców w aplikacjach korzystających z usługi Azure Cosmos DB

Po utworzeniu konta z włączonymi wieloma regionami zapisu należy wprowadzić dwie zmiany w aplikacji do ConnectionPolicy dla DocumentClient, aby włączyć funkcje multi-master i multi-homowy w usłudze Azure Cosmos DB. W ramach ConnectionPolicy, ustaw UseMultipleWriteLocations true i przekazać nazwę regionu, w którym aplikacja jest wdrażana do SetCurrentLocation. Spowoduje to wypełnić PreferredLocations właściwości na podstawie bliskości geograficznej z lokalizacji przekazanych w. Jeśli nowy region zostanie później dodany do konta, aplikacja nie musi być aktualizowana ani ponownie rozmieszczana, automatycznie wykryje region bliższy i automatycznie będzie do niego mieszczona w przypadku wystąpienia zdarzenia regionalnego.

> [!Note]
> Konta usługi Cosmos początkowo skonfigurowane z pojedynczym regionem zapisu można skonfigurować do wielu regionów zapisu (tj. multi-master) z zerowym czasem przestoju. Aby dowiedzieć się więcej zobacz, [Konfigurowanie regionów wielokrotnego zapisu](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a name="net-sdk-v2"></a><a id="netv2"></a>Zestaw .NET SDK w wersji 2

Aby włączyć multi-master w `UseMultipleWriteLocations` aplikacji, ustaw na `true`. Ponadto zestaw `SetCurrentLocation` do regionu, w którym aplikacja jest wdrażana i gdzie usługa Azure Cosmos DB jest replikowana:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>Plik SDK .NET w wersji 3

Aby włączyć wiele wzorca w `ApplicationRegion` aplikacji, należy ustawić region, w którym aplikacja jest wdrażana i gdzie usługa Cosmos DB jest replikowana:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

Opcjonalnie można użyć `CosmosClientBuilder` i `WithApplicationRegion` osiągnąć ten sam wynik:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-async-sdk"></a><a id="java"></a>Java Async SDK

Aby włączyć wiele wzorca w `policy.setUsingMultipleWriteLocations(true)` aplikacji, ustaw i ustaw `policy.setPreferredLocations` region, w którym aplikacja jest wdrażana i gdzie usługa Cosmos DB jest replikowana:

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

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>SDK node.js, JavaScript i TypeScript

Aby włączyć multi-master w `connectionPolicy.UseMultipleWriteLocations` aplikacji, ustaw na `true`. Ponadto ustaw `connectionPolicy.PreferredLocations` region, w którym aplikacja jest wdrażana i gdzie usługa Cosmos DB jest replikowana:

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

## <a name="python-sdk"></a><a id="python"></a>Python SDK

Aby włączyć multi-master w `connection_policy.UseMultipleWriteLocations` aplikacji, ustaw na `true`. Ponadto ustaw `connection_policy.PreferredLocations` region, w którym aplikacja jest wdrażana i gdzie usługa Cosmos DB jest replikowana.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Następne kroki

Przeczytaj następujące artykuły:

* [Zarządzanie spójnością usługi Azure Cosmos DB za pomocą tokenów sesji](how-to-manage-consistency.md#utilize-session-tokens)
* [Conflict types and resolution policies in Azure Cosmos DB (Typy konfliktów i zasady ich rozwiązywania w usłudze Azure Cosmos DB)](conflict-resolution-policies.md)
* [High availability in Azure Cosmos DB (Wysoka dostępność w usłudze Azure Cosmos DB)](high-availability.md)
* [Poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md)
* [Wybierz odpowiedni poziom spójności w usłudze Azure Cosmos DB](consistency-levels-choosing.md)
* [Kompromisy spójności, dostępności i wydajności w usłudze Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Kompromisy w zakresie dostępności i wydajności dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Globalne skalowanie aprowizowanej przepływności](scaling-throughput.md)
* [Globalna dystrybucja: Pod maską](global-dist-under-the-hood.md)
