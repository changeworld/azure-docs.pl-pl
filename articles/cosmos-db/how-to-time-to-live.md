---
title: Dowiedz się, jak skonfigurować czas wygaśnięcia w usłudze Azure Cosmos DB i jak nim zarządzać
description: Dowiedz się, jak skonfigurować czas wygaśnięcia w usłudze Azure Cosmos DB i jak nim zarządzać
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: mjbrown
ms.openlocfilehash: ddda7b96147892efb38cb0405120db3613e98cf8
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104866"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>Konfigurowanie czasu wygaśnięcia w usłudze Azure Cosmos DB

W usłudze Azure Cosmos DB możesz skonfigurować czas wygaśnięcia (Time to Live, TTL) na poziomie kontenera lub zastąpić go na poziomie elementu po ustawieniu TTL dla kontenera. TTL można skonfigurować dla kontenera przy użyciu witryny Azure Portal lub zestawu SDK właściwego dla używanego języka. Zastępowanie TTL na poziome elementu można skonfigurować za pomocą zestawów SDK.

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Włączanie czasu wygaśnięcia dla kontenera w witrynie Azure Portal

Wykonaj następujące czynności, aby włączyć czas wygaśnięcia dla kontenera bez terminu wygaśnięcia. Włącz tę funkcję, aby umożliwić zastępowanie TTL na poziomie elementu. Możesz także ustawić TTL, wprowadzając niezerową liczbę sekund.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Utwórz nowe konto usługi Azure Cosmos lub wybierz istniejące.

3. Otwórz okienko **Eksplorator danych**.

4. Wybierz istniejący kontener, rozwiń go, a następnie zmodyfikuj następujące wartości:

   * Otwórz okno **Skalowanie i ustawienia**.
   * W obszarze **Ustawienia** znajdź pozycję **Czas wygaśnięcia**.
   * Wybierz pozycję **Włączony (brak wartości domyślnej)** lub wybierz pozycję **Włączony** i ustaw wartość TTL.
   * Kliknij przycisk **Zapisz**, aby zapisać zmiany.

   ![Konfigurowanie czasu wygaśnięcia w witrynie Azure Portal](./media/how-to-time-to-live/how-to-time-to-live-portal.png)


- Gdy DefaultTimeToLive ma wartość null, czas wygaśnięcia jest wyłączony
- Gdy DefaultTimeToLive ma wartość-1, ustawienie czasu wygaśnięcia jest włączone (bez domyślnego)
- Gdy DefaultTimeToLive ma inną wartość int (z wyjątkiem 0), ustawienie czasu wygaśnięcia jest włączone

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Włączanie czasu wygaśnięcia dla kontenera za pomocą zestawu SDK

### <a id="dotnet-enable-noexpiry"></a>.NET SDK V2 (Microsoft. Azure. DocumentDB)

```csharp
// Create a new container with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition);
```

### <a id="dotnet-enable-noexpiry"></a>Zestaw .NET SDK v3 (Microsoft. Azure. Cosmos)

```csharp
// Create a new container with TTL enabled and without any expiration value
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = -1 //(never expire by default)
});
```

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Ustawianie czasu wygaśnięcia dla kontenera za pomocą zestawu SDK

Aby ustawić czas wygaśnięcia dla kontenera, należy podać niezerową liczbę dodatnią, określającą czas w sekundach. Na podstawie skonfigurowanej wartości TTL wszystkie elementy w kontenerze zostaną usunięte po upływie wskazanego czasu od wartości znacznika czasu ostatniej modyfikacji `_ts`.

### <a id="dotnet-enable-withexpiry"></a>.NET SDK V2 (Microsoft. Azure. DocumentDB)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition;
```

### <a id="dotnet-enable-withexpiry"></a>Zestaw .NET SDK v3 (Microsoft. Azure. Cosmos)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
});
```

### <a id="nodejs-enable-withexpiry"></a>NodeJS SDK

```javascript
const containerDefinition = {
          id: "sample container1",
        };

async function createcontainerWithTTL(db: Database, containerDefinition: ContainerDefinition, collId: any, defaultTtl: number) {
      containerDefinition.id = collId;
      containerDefinition.defaultTtl = defaultTtl;
      await db.containers.create(containerDefinition);
}
```

## <a name="set-time-to-live-on-an-item"></a>Ustawianie czasu wygaśnięcia elementu

Oprócz skonfigurowania domyślnego czasu wygaśnięcia dla kontenera możesz ustawić czas wygaśnięcia konkretnego elementu. Ustawienie czasu wygaśnięcia na poziomie elementu spowoduje zastąpienie domyślnego czasu wygaśnięcia elementu w kontenerze.

* Aby ustawić czas wygaśnięcia elementu, podaj niezerową liczbę dodatnią oznaczającą czas w sekundach — element zostanie usunięty po upływie tego czasu od wartości znacznika czasu ostatniej modyfikacji `_ts`.

* Jeśli element nie ma pola TTL, będzie obowiązywał domyślny czas wygaśnięcia ustawiony dla kontenera.

* Jeśli czas wygaśnięcia zostanie wyłączony na poziomie kontenera, pole TTL elementu będzie ignorowane do momentu ponownego włączenia czasu wygaśnięcia dla kontenera.

### <a id="portal-set-ttl-item"></a>Azure Portal

Wykonaj następujące kroki, aby włączyć czas wygaśnięcia dla elementu:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Utwórz nowe konto usługi Azure Cosmos lub wybierz istniejące.

3. Otwórz okienko **Eksplorator danych**.

4. Wybierz istniejący kontener, rozwiń go, a następnie zmodyfikuj następujące wartości:

   * Otwórz okno **Skalowanie i ustawienia**.
   * W obszarze **Ustawienia** znajdź pozycję **Czas wygaśnięcia**.
   * Wybierz opcję **włączone (brak wartości domyślnej)** lub wybierz opcję **włączone** i ustaw wartość TTL. 
   * Kliknij przycisk **Zapisz**, aby zapisać zmiany.

5. Następnie przejdź do elementu, dla którego chcesz ustawić czas wygaśnięcia, Dodaj `ttl` Właściwość i wybierz pozycję **Aktualizuj**. 

   ```json
   {
    "id": "1",
    "_rid": "Jic9ANWdO-EFAAAAAAAAAA==",
    "_self": "dbs/Jic9AA==/colls/Jic9ANWdO-E=/docs/Jic9ANWdO-EFAAAAAAAAAA==/",
    "_etag": "\"0d00b23f-0000-0000-0000-5c7712e80000\"",
    "_attachments": "attachments/",
    "ttl": 10,
    "_ts": 1551307496
   }
   ```

### <a id="dotnet-set-ttl-item"></a>Zestaw SDK platformy .NET (any)

```csharp
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    [JsonProperty(PropertyName="cid")]
    public string CustomerId { get; set; }
    // used to set expiration policy
    [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
    public int? ttl { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    ttl = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

### <a id="nodejs-set-ttl-item"></a>NodeJS SDK

```javascript
const itemDefinition = {
          id: "doc",
          name: "sample Item",
          key: "value", 
          ttl: 2
        };
```


## <a name="reset-time-to-live"></a>Resetowanie czasu wygaśnięcia

Możesz zresetować czas wygaśnięcia elementu, wykonując operację zapisu lub aktualizacji na elemencie. Operacja zapisu lub aktualizacji spowoduje ustawienie znacznika czasu `_ts` na bieżący czas, od którego ponownie rozpocznie się odliczanie czasu wygaśnięcia elementu. Jeśli chcesz zmienić czas wygaśnięcia elementu, możesz zaktualizować pole TTL tak samo jak każde inne pole.

### <a id="dotnet-extend-ttl-item"></a>.NET SDK V2 (Microsoft. Azure. DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

### <a id="dotnet-extend-ttl-item"></a>Zestaw .NET SDK v3 (Microsoft. Azure. Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = 60 * 30 * 30; // update time to live
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

## <a name="turn-off-time-to-live"></a>Wyłączanie wygasania

Jeśli ustawiono czas wygaśnięcia elementu, ale nie chcesz, aby ten element wygasał, możesz pobrać go, usunąć pole TTL i ponownie umieścić element na serwerze. Po usunięciu pola TTL z elementu stosowana jest do niego domyślna wartość czasu wygaśnięcia przypisana do kontenera. Ustaw wartość TTL na –1, aby element nie dziedziczył wartości czasu wygaśnięcia z kontenera i nie wygasał.

### <a id="dotnet-turn-off-ttl-item"></a>.NET SDK V2 (Microsoft. Azure. DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = null; // inherit the default TTL of the container

response = await client.ReplaceDocumentAsync(readDocument);
```

### <a id="dotnet-turn-off-ttl-item"></a>Zestaw .NET SDK v3 (Microsoft. Azure. Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = null; // inherit the default TTL of the container
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

## <a name="disable-time-to-live"></a>Wyłączanie czasu wygaśnięcia

Aby wyłączyć czas wygaśnięcia dla kontenera i zatrzymać proces w tle sprawdzający, czy elementy wygasły, należy usunąć właściwość `DefaultTimeToLive` z kontenera. Usunięcie tej właściwości ma inne skutki niż ustawienie jej na –1. Jeśli wartość właściwości zostanie ustawiona na –1, nowe elementy dodawane do kontenera nie będą wygasały, ale można będzie zastąpić tę wartość w przypadku wybranych elementów w kontenerze. Po usunięciu właściwości TTL z kontenera elementy będą nigdy nie wygasną, nawet jeśli istnieją jawnie zastąpione poprzednią domyślną wartością czasu wygaśnięcia.

### <a id="dotnet-disable-ttl"></a>.NET SDK V2 (Microsoft. Azure. DocumentDB)

```csharp
// Get the container, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

### <a id="dotnet-disable-ttl"></a>Zestaw .NET SDK v3 (Microsoft. Azure. Cosmos)

```csharp
// Get the container, update DefaultTimeToLive to null
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Disable TTL
containerResponse.Resource.DefaultTimeToLive = null;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat czasu wygaśnięcia, zobacz artykuł:

* [Time to live (Czas wygaśnięcia)](time-to-live.md)
