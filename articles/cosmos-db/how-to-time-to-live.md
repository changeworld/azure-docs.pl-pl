---
title: Dowiedz się, jak skonfigurować czas wygaśnięcia i zarządzać nim w Azure Cosmos DB
description: Dowiedz się, jak skonfigurować czas wygaśnięcia i zarządzać nim w Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: e36b894ec9c7433bc114145e0de5f519f73486f1
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815911"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>Skonfiguruj czas wygaśnięcia w Azure Cosmos DB

W Azure Cosmos DB możesz wybrać opcję Skonfiguruj czas wygaśnięcia (TTL) na poziomie kontenera lub przesłonić ją na poziomie elementu po ustawieniu dla kontenera. Wartość TTL dla kontenera można skonfigurować przy użyciu Azure Portal lub zestawów SDK specyficznych dla języka. Przesłonięcia TTL poziomu elementu można skonfigurować przy użyciu zestawów SDK.

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Włącz na żywo czas wygaśnięcia kontenera przy użyciu Azure Portal

Wykonaj następujące kroki, aby włączyć czas wygaśnięcia w kontenerze bez wygaśnięcia. Włącz tę opcję, aby zezwolić na zastąpienie czasu wygaśnięcia na poziomie elementu. Możesz również ustawić czas wygaśnięcia, wprowadzając wartość różną od zera dla sekund.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

2. Utwórz nowe konto usługi Azure Cosmos lub Wybierz istniejące konto.

3. Otwórz okienko **Eksplorator danych** .

4. Wybierz istniejący kontener, rozwiń go i zmodyfikuj następujące wartości:

   * Otwórz okno **skalowanie & ustawienia** .
   * W obszarze **ustawienie** Znajdź, **czas wygaśnięcia**.
   * Wybierz opcję **włączone (brak wartości domyślnej)** lub wybierz opcję **włączone** i ustaw wartość czasu wygaśnięcia
   * Kliknij przycisk **Zapisz** , aby zapisać zmiany.

   ![Skonfiguruj czas wygaśnięcia w Azure Portal](./media/how-to-time-to-live/how-to-time-to-live-portal.png)

* Gdy DefaultTimeToLive ma wartość null, czas wygaśnięcia jest wyłączony
* Gdy DefaultTimeToLive ma wartość-1, ustawienie czasu wygaśnięcia jest włączone (bez domyślnego)
* Gdy DefaultTimeToLive ma inną wartość int (z wyjątkiem 0), ustawienie czasu wygaśnięcia jest włączone

## <a name="enable-time-to-live-on-a-container-using-azure-cli-or-powershell"></a>Włączanie czasu wygaśnięcia w kontenerze przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell

Aby utworzyć lub włączyć czas wygaśnięcia dla kontenera, zobacz.

* [Tworzenie kontenera z czasem wygaśnięcia przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#create-a-container-with-ttl)
* [Tworzenie kontenera z czasem wygaśnięcia przy użyciu programu PowerShell](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Włączanie czasu wygaśnięcia kontenera przy użyciu zestawu SDK

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

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Ustaw czas wygaśnięcia kontenera przy użyciu zestawu SDK

Aby ustawić czas wygaśnięcia w kontenerze, należy podać niezerową liczbę dodatnią wskazującą czas w sekundach. Na podstawie skonfigurowanej wartości TTL wszystkie elementy w kontenerze po ostatniej modyfikacji sygnatury czasowej elementu `_ts` są usuwane.

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

## <a name="set-time-to-live-on-an-item"></a>Ustaw czas wygaśnięcia dla elementu

Oprócz ustawienia domyślnego czasu, który ma być aktywny w kontenerze, można ustawić czas wygaśnięcia dla elementu. Ustawienie czasu wygaśnięcia na poziomie elementu spowoduje zastąpienie domyślnego czasu wygaśnięcia elementu w tym kontenerze.

* Aby ustawić czas wygaśnięcia dla elementu, należy podać niezerową liczbę dodatnią, która wskazuje okres, w sekundach, wygaśnięcia elementu po ostatniej modyfikacji znacznika czasu elementu `_ts`.

* Jeśli element nie ma pola czasu wygaśnięcia, domyślnie wartość TTL ustawiona dla kontenera będzie stosowana do elementu.

* Jeśli czas wygaśnięcia jest wyłączony na poziomie kontenera, pole TTL w elemencie zostanie zignorowane do momentu ponownego włączenia czasu wygaśnięcia w kontenerze.

### <a id="portal-set-ttl-item"></a>Azure Portal

Wykonaj następujące kroki, aby włączyć czas wygaśnięcia dla elementu:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

2. Utwórz nowe konto usługi Azure Cosmos lub Wybierz istniejące konto.

3. Otwórz okienko **Eksplorator danych** .

4. Wybierz istniejący kontener, rozwiń go i zmodyfikuj następujące wartości:

   * Otwórz okno **skalowanie & ustawienia** .
   * W obszarze **ustawienie** Znajdź, **czas wygaśnięcia**.
   * Wybierz opcję **włączone (brak wartości domyślnej)** lub wybierz opcję **włączone** i ustaw wartość TTL. 
   * Kliknij przycisk **Zapisz** , aby zapisać zmiany.

5. Następnie przejdź do elementu, dla którego chcesz ustawić czas wygaśnięcia, Dodaj właściwość `ttl` i wybierz pozycję **Aktualizuj**. 

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

Możesz zresetować czas wygaśnięcia elementu, wykonując operację zapisu lub aktualizacji dla elementu. Operacja zapisu lub aktualizacji ustawi wartość `_ts` na bieżącą godzinę, a czas wygaśnięcia dla tego elementu zostanie rozpoczęty ponownie. Jeśli chcesz zmienić czas wygaśnięcia elementu, możesz zaktualizować pole tak samo jak w przypadku aktualizowania dowolnego innego pola.

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

## <a name="turn-off-time-to-live"></a>Wyłącz czas wygaśnięcia

Jeśli ustawiono czas wygaśnięcia dla elementu i nie chcesz, aby ten element już się nie wygaśnie, możesz uzyskać ten element, usunąć pole czasu wygaśnięcia i zastąpić element na serwerze. Gdy pole czasu wygaśnięcia zostanie usunięte z elementu, do elementu zostanie zastosowana domyślna wartość czasu wygaśnięcia przypisana do kontenera. Ustaw wartość parametru TTL na-1, aby zapobiec wygaśnięciu elementu i nie odziedziczyć wartości TTL z kontenera.

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

## <a name="disable-time-to-live"></a>Wyłącz czas wygaśnięcia

Aby wyłączyć czas wygaśnięcia w kontenerze i zatrzymać proces w tle sprawdzania elementów wygasłych, należy usunąć Właściwość `DefaultTimeToLive` w kontenerze. Usunięcie tej właściwości różni się od ustawienia jej do-1. Gdy ustawisz ją na wartość-1, nowe elementy dodawane do kontenera będą na bieżąco na żywo, jednak można zastąpić tę wartość dla określonych elementów w kontenerze. Po usunięciu właściwości TTL z kontenera elementy będą nigdy nie wygasną, nawet jeśli istnieją jawnie zastąpione poprzednią domyślną wartością czasu wygaśnięcia.

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

Dowiedz się więcej na temat czasu wygaśnięcia w następującym artykule:

* [Czas wygaśnięcia](time-to-live.md)
