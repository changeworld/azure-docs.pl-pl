---
title: Jak zaimplementować synchronizację niestandardową w celu optymalizacji pod kątem wyższej dostępności i wydajności w usłudze Azure Cosmos DB
description: Dowiedz się, jak zaimplementować synchronizację niestandardową w celu optymalizacji pod kątem wyższej dostępności i wydajności w usłudze Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: d948798f161eb36578cb679b6d96409917424fd4
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678466"
---
# <a name="how-to-implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Jak zaimplementować synchronizację niestandardową w celu optymalizacji pod kątem wyższej dostępności i wydajności

Usługa Azure Cosmos DB oferuje [pięć dobrze zdefiniowanych poziomów spójności](consistency-levels.md) można wybrać z równoważyć zależnościami między spójnością, wydajności i dostępności. Zapewnia wysoki poziom spójności, dane są replikowane synchronicznie i trwale utrwalone w każdym regionie, gdzie znajduje się konto usługi Azure Cosmos. Jednak taka konfiguracja zapewnia najwyższy poziom trwałości kosztem wydajności i dostępności. Jeśli aplikacja chce kontroli/zwalnia trwałość danych do własnych aplikacji wymaga bez naruszania dostępności, można stosować *synchronizacji niestandardowych* w warstwie aplikacji, aby osiągnąć żądany poziom trwałość.

Na poniższym diagramie w sposób wizualny przedstawiono model synchronizacji niestandardowej.

![Synchronizacja niestandardowa](./media/how-to-custom-synchronization/custom-synchronization.png)

W tym scenariuszu kontener usługi Azure Cosmos jest replikowany globalnie w kilku regionach na wielu kontynentach. Za pomocą wysoki poziom spójności w przypadku wszystkich regionów, w tym scenariuszu mogło mieć wpływ na wydajność. Aby zapewnić wyższy poziom trwałości danych bez naruszania opóźnienia zapisu, aplikacja może użyć dwóch klientów, które mają takie same [tokenu sesji](how-to-manage-consistency.md#utilize-session-tokens).

Pierwszy klient może zapisywać dane w regionie lokalnym (np. Zachodnie stany USA). Drugi klient (np. w regionie Wschodnie stany USA) to klient odczytu używany do zapewnienia synchronizacji. Przez przepływających tokenu sesji z odpowiedzi zapisu do następujących odczytu, odczytu zapewni synchronizacji zapisów wschodnie stany USA. Usługa Azure Cosmos DB zapewni widoczność operacji zapisu w co najmniej jednym regionie i gwarancję ich odporności na awarię regionalną, jeśli oryginalny region zapisu przestanie działać. W tym scenariuszu każda operacja zapisu jest synchronizowana z regionem Wschodnie stany USA, co zmniejsza opóźnienie związane z zastosowaniem wysokiego poziomu spójności we wszystkich regionach. W przypadku scenariusza związanego z wielowzorcowością, w ramach którego operacje zapisu mają miejsce w każdym regionie, ten model można rozszerzyć w celu równoległej synchronizacji w wielu regionach.

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

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat dystrybucji globalnej i spójności w usłudze Azure Cosmos DB, przeczytaj następujące artykuły:

* [Choosing the right consistency level in Azure Cosmos DB (Wybieranie odpowiedniego poziomu spójności w usłudze Azure Cosmos DB)](consistency-levels-choosing.md)
* [Kompromisy w zakresie spójności, dostępności i wydajności w usłudze Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [How to manage consistency in Azure Cosmos DB (Jak zarządzać spójnością w usłudze Azure Cosmos DB)](how-to-manage-consistency.md)
* [Partitioning and data distribution in Azure Cosmos DB (Partycjonowanie i dystrybucja danych w usłudze Azure Cosmos DB)](partition-data.md)
