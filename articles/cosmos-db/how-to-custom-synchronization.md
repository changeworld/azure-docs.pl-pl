---
title: Jak zaimplementować synchronizację niestandardową w celu optymalizacji pod kątem wyższej dostępności i wydajności w usłudze Azure Cosmos DB
description: Dowiedz się, jak zaimplementować synchronizację niestandardową w celu optymalizacji pod kątem wyższej dostępności i wydajności w Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: 0f630c2139d1d7d391d6c5578e5e7f378e56dcb4
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2019
ms.locfileid: "69013791"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Implementowanie niestandardowej synchronizacji w celu optymalizacji pod kątem wyższej dostępności i wydajności

Azure Cosmos DB oferuje [pięć dobrze zdefiniowanych poziomów spójności](consistency-levels.md) , które można wybrać, aby zrównoważyć kompromis między spójnością, wydajnością i dostępnością. Silna spójność pomaga zapewnić, że dane są synchronicznie replikowane i trwale utrwalone w każdym regionie, w którym jest dostępne konto platformy Azure Cosmos. Ta konfiguracja zapewnia najwyższy poziom trwałości, ale jest kosztem wydajności i dostępności. Jeśli chcesz, aby aplikacja mogła kontrolować lub zmniejszać trwałość danych do potrzeb aplikacji bez kompromisu dostępności, możesz użyć *niestandardowej synchronizacji* w warstwie aplikacji w celu osiągnięcia żądanego poziomu trwałości.

Na poniższym obrazie przedstawiono wizualnie model synchronizacji niestandardowej:

![Synchronizacja niestandardowa](./media/how-to-custom-synchronization/custom-synchronization.png)

W tym scenariuszu kontener usługi Azure Cosmos jest replikowany globalnie w kilku regionach na wielu kontynentach. Używanie silnej spójności dla wszystkich regionów w tym scenariuszu ma wpływ na wydajność. Aby zapewnić wyższy poziom trwałości danych bez naruszania opóźnień zapisu, aplikacja może używać dwóch klientów, którzy korzystają z tego samego [tokenu sesji](how-to-manage-consistency.md#utilize-session-tokens).

Pierwszy klient może zapisywać dane w regionie lokalnym (np. Zachodnie stany USA). Drugi klient (na przykład w regionie Wschodnie stany USA) jest klientem odczytu, który jest używany do zapewnienia synchronizacji. Przepływając token sesji z odpowiedzi zapisu do poniższego odczytu, odczyt gwarantuje synchronizację zapisów w regionie Wschodnie stany USA. Azure Cosmos DB zapewnia, że zapisy są widoczne w co najmniej jednym regionie. W przypadku awarii oryginalnego regionu zapisu zagwarantowane jest przekroczenie regionalnej przerwy w działaniu. W tym scenariuszu każdy zapis jest synchronizowany do regionu Wschodnie stany USA, skracając czas oczekiwania na zastosowanie silnej spójności we wszystkich regionach. W scenariuszu z wieloma wzorcami, w których zapisy są wykonywane w każdym regionie, można rozwinąć ten model w celu zsynchronizowania ich z wieloma regionami równolegle.

## <a name="configure-the-clients"></a>Konfigurowanie klientów

Poniższy przykład pokazuje warstwę dostępu do danych, która tworzy wystąpienia dwóch klientów na potrzeby niestandardowej synchronizacji:

### <a name="net-v2-sdk"></a>Zestaw .NET V2 SDK
```csharp
class MyDataAccessLayer
{
    private DocumentClient writeClient;
    private DocumentClient readClient;

    public async Task InitializeAsync(Uri accountEndpoint, string key)
    {
        ConnectionPolicy writeConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp,
            UseMultipleWriteLocations = true
        };
        writeConnectionPolicy.SetCurrentLocation(LocationNames.WestUS);

        ConnectionPolicy readConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        };
        readConnectionPolicy.SetCurrentLocation(LocationNames.EastUS);

        writeClient = new DocumentClient(accountEndpoint, key, writeConnectionPolicy);
        readClient = new DocumentClient(accountEndpoint, key, readConnectionPolicy, ConsistencyLevel.Session);

        await Task.WhenAll(new Task[]
        {
            writeClient.OpenAsync(),
            readClient.OpenAsync()
        });
    }
}
```

### <a name="net-v3-sdk"></a>Zestaw SDK dla platformy .NET v3
```csharp
class MyDataAccessLayer
{
    private CosmosClient writeClient;
    private CosmosClient readClient;

    public void InitializeAsync(string accountEndpoint, string key)
    {
        CosmosClientOptions writeConnectionOptions = new CosmosClientOptions();
        writeConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        writeConnectionOptions.ApplicationRegion = "West US";

        CosmosClientOptions readConnectionOptions = new CosmosClientOptions();
        readConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        readConnectionOptions.ApplicationRegion = "East US";


        writeClient = new CosmosClient(accountEndpoint, key, writeConnectionOptions);
        readClient = new CosmosClient(accountEndpoint, key, readConnectionOptions);
    }
}
```

## <a name="implement-custom-synchronization"></a>Implementowanie synchronizacji niestandardowej

Po zainicjowaniu klientów aplikacja może wykonywać operacje zapisu w regionie lokalnym (Zachodnie stany USA) i wymusić synchronizację zapisów w regionach Wschodnie stany USA w następujący sposób.

### <a name="net-v2-sdk"></a>Zestaw .NET V2 SDK
```csharp
class MyDataAccessLayer
{
    public async Task CreateItem(string containerLink, Document document)
    {
        ResourceResponse<Document> response = await writeClient.CreateDocumentAsync(
            containerLink, document);

        await readClient.ReadDocumentAsync(response.Resource.SelfLink,
            new RequestOptions { SessionToken = response.SessionToken });
    }
}
```

### <a name="net-v3-sdk"></a>Zestaw SDK dla platformy .NET v3
```csharp
class MyDataAccessLayer
{
     public async Task CreateItem(string databaseId, string containerId, dynamic item)
     {
        ItemResponse<dynamic> response = await writeClient.GetContainer("containerId", "databaseId")
            .CreateItemAsync<dynamic>(
                item,
                new PartitionKey("test"));

        await readClient.GetContainer("containerId", "databaseId").ReadItemAsync<dynamic>(
            response.Resource.id,
            new PartitionKey("test"),
            new ItemRequestOptions { SessionToken = response.Headers.Session, ConsistencyLevel = ConsistencyLevel.Session });
    }
}
```

Można zwiększyć model, aby zsynchronizować go z wieloma regionami równolegle.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat globalnej dystrybucji i spójności w Azure Cosmos DB, przeczytaj następujące artykuły:

* [Wybierz odpowiedni poziom spójności w Azure Cosmos DB](consistency-levels-choosing.md)
* [Kompromisy w zakresie spójności, dostępności i wydajności w usłudze Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Zarządzanie spójnością w Azure Cosmos DB](how-to-manage-consistency.md)
* [Partitioning and data distribution in Azure Cosmos DB (Partycjonowanie i dystrybucja danych w usłudze Azure Cosmos DB)](partition-data.md)
