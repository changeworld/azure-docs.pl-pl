---
title: Jak zaimplementować synchronizację niestandardową w celu optymalizacji pod kątem wyższej dostępności i wydajności w usłudze Azure Cosmos DB
description: Dowiedz się, jak zaimplementować niestandardowy synchronizacji zoptymalizowane pod kątem większej dostępności i wydajności w usłudze Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: f10e260432a93a0413d65d6f5814d00a50e9465a
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560267"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Implementowanie niestandardowego synchronizacji zoptymalizowane pod kątem większej dostępności i wydajności

Usługa Azure Cosmos DB oferuje [pięć dobrze zdefiniowanych poziomów spójności](consistency-levels.md) można wybrać z równoważyć zależnościami między spójnością, wydajności i dostępności. Wysoki poziom spójności pomaga zapewnić danych replikowanych synchronicznie i trwale utrwalone w każdym regionie, w których konto usługi Cosmos Azure jest dostępna. Ta konfiguracja zapewnia najwyższy poziom niezawodności, ale kosztem wydajności i dostępności. Jeśli chcesz, aby aplikację, aby kontrolować lub zwalnia trwałości danych w aplikacji stosownie do potrzeb bez naruszania dostępności, możesz użyć *synchronizacji niestandardowych* w warstwie aplikacji w celu osiągnięcia poziomu niezawodności możesz chcesz.

Poniższa ilustracja przedstawia wizualnie modelu niestandardowego synchronizacji:

![Niestandardowe synchronizacji](./media/how-to-custom-synchronization/custom-synchronization.png)

W tym scenariuszu kontenera usługi Azure Cosmos jest replikowany globalnie w kilku regionach na wielu kontynentach. Za pomocą wysoki poziom spójności w przypadku wszystkich regionów, w tym scenariuszu wpływa na wydajność. Aby zapewnić wyższy poziom trwałości danych bez naruszania opóźnienie zapisu, aplikacja może użyć dwóch klientów, które mają takie same [tokenu sesji](how-to-manage-consistency.md#utilize-session-tokens).

Pierwszy klient może zapisywać dane w regionie lokalnym (np. Zachodnie stany USA). Drugi klienta (na przykład we wschodnich Stanach USA) jest odczytu klienta, który służy do zapewnienia synchronizacji. Przepływających tokenu sesji z odpowiedzi zapisu do następujących odczytu, odczytu zapewnia synchronizację zapisów wschodnie stany USA. Usługa Azure Cosmos DB gwarantuje, że zapisy są widoczne dla co najmniej jednego regionu. Mają gwarancję, że przetrwać regionalnej awarii, jeśli oryginalny region zapisu ulegnie awarii. W tym scenariuszu każdego zapisu jest synchronizowana z usługą wschodnie stany USA, zmniejszając czas oczekiwania na wprowadzenie wysoki poziom spójności we wszystkich regionach. W przypadku wielu wzorców, której wystąpienia zapisy w każdym regionie, można rozszerzyć ten model, aby zsynchronizować do wielu regionów, w sposób równoległy.

## <a name="configure-the-clients"></a>Konfigurowanie klientów

Poniższy przykład pokazuje warstwy dostępu do danych, tworzącym dwóch klientów niestandardowych synchronizacji:

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

Po klientów są inicjowane, aplikację można wykonywać operacje zapisu region lokalny (zachodnie stany USA) i wymuszanie synchronizacji zapisy do wschodnie stany USA w następujący sposób.

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

Możesz rozszerzyć modelu do synchronizacji w wielu regionach w sposób równoległy.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat dystrybucji globalnej i spójność w usłudze Azure Cosmos DB, przeczytaj następujące artykuły:

* [Wybierz poziom spójności w prawo w usłudze Azure Cosmos DB](consistency-levels-choosing.md)
* [Kompromisy w zakresie spójności, dostępności i wydajności w usłudze Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Zarządzanie spójności w usłudze Azure Cosmos DB](how-to-manage-consistency.md)
* [Partitioning and data distribution in Azure Cosmos DB (Partycjonowanie i dystrybucja danych w usłudze Azure Cosmos DB)](partition-data.md)
