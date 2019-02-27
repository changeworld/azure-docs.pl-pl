---
title: Jak zaimplementować synchronizację niestandardową w celu optymalizacji pod kątem wyższej dostępności i wydajności w usłudze Azure Cosmos DB
description: Dowiedz się, jak zaimplementować synchronizację niestandardową w celu optymalizacji pod kątem wyższej dostępności i wydajności w usłudze Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 2/12/2019
ms.author: mjbrown
ms.openlocfilehash: 43cb73784806358bccb9758be2923d3df5e9badd
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414885"
---
# <a name="how-to-implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Jak zaimplementować synchronizację niestandardową w celu optymalizacji pod kątem wyższej dostępności i wydajności

Usługa Azure Cosmos DB oferuje pięć dobrze zdefiniowanych poziomów spójności do wyboru w celu osiągnięcia kompromisu między spójnością, wydajnością i dostępnością. Wysoki poziom spójności zapewnia synchroniczne replikowanie danych i trwałość w każdym regionie, w którym jest dostępne konto usługi Azure Cosmos. Jednak taka konfiguracja zapewnia najwyższy poziom trwałości kosztem wydajności i dostępności. Aby w aplikacji kontrolować poziom trwałości danych lub obniżyć go w zależności od potrzeb aplikacji bez ograniczania dostępności, możliwe jest zastosowanie synchronizacji niestandardowej w warstwie aplikacji w celu osiągnięcia żądanego poziomu trwałości.

Na poniższym diagramie w sposób wizualny przedstawiono model synchronizacji niestandardowej.

![Synchronizacja niestandardowa](./media/how-to-custom-synchronization/custom-synchronization.png)

W tym scenariuszu kontener usługi Azure Cosmos jest replikowany globalnie w kilku regionach na wielu kontynentach. Użycie wysokiego poziomu spójności dla wszystkich regionów w tym scenariuszu będzie miało wpływ na wydajność. Aby zapewnić wyższy poziom trwałości danych bez negatywnego wpływu na opóźnienie zapisu, aplikacja może używać dwóch klientów, które współdzielą ten sam token sesji.

Pierwszy klient może zapisywać dane w regionie lokalnym (np. Zachodnie stany USA). Drugi klient (np. w regionie Wschodnie stany USA) to klient odczytu używany do zapewnienia synchronizacji. Dzięki przesłaniu tokenu sesji z odpowiedzi operacji zapisu do następnej operacji odczytu, operacja odczytu zapewni synchronizację operacji zapisu w regionie Wschodnie stany USA. Usługa Azure Cosmos DB zapewni widoczność operacji zapisu w co najmniej jednym regionie i gwarancję ich odporności na awarię regionalną, jeśli oryginalny region zapisu przestanie działać. W tym scenariuszu każda operacja zapisu jest synchronizowana z regionem Wschodnie stany USA, co zmniejsza opóźnienie związane z zastosowaniem wysokiego poziomu spójności we wszystkich regionach. W przypadku scenariusza związanego z wielowzorcowością, w ramach którego operacje zapisu mają miejsce w każdym regionie, ten model można rozszerzyć w celu równoległej synchronizacji w wielu regionach.

## <a name="configure-the-clients"></a>Konfigurowanie klientów

Poniższy przykład przedstawia warstwę dostępu do danych, w której w tym celu utworzono wystąpienia dwóch klientów.

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

## <a name="implement-custom-synchronization"></a>Implementowanie synchronizacji niestandardowej

Po zainicjowaniu klientów aplikacja może wykonywać operacje zapisu w regionie lokalnym (Zachodnie stany USA) i wymuszać synchronizację operacji zapisu w regionie Wschodnie stany USA w następujący sposób.

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

Ten model można rozszerzyć w celu równoległej synchronizacji w wielu regionach.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat dystrybucji globalnej i spójności w usłudze Azure Cosmos DB, przeczytaj następujące artykuły:

* [Choosing the right consistency level in Azure Cosmos DB (Wybieranie odpowiedniego poziomu spójności w usłudze Azure Cosmos DB)](consistency-levels-choosing.md)

* [Kompromisy w zakresie spójności, dostępności i wydajności w usłudze Azure Cosmos DB](consistency-levels-tradeoffs.md)

* [How to manage consistency in Azure Cosmos DB (Jak zarządzać spójnością w usłudze Azure Cosmos DB)](how-to-manage-consistency.md)

* [Partitioning and data distribution in Azure Cosmos DB (Partycjonowanie i dystrybucja danych w usłudze Azure Cosmos DB)](partition-data.md)
