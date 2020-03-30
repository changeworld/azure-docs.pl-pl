---
title: Konfigurowanie usługi Time to Live w usłudze Azure Cosmos DB i zarządzanie nim
description: Dowiedz się, jak skonfigurować czas życia w kontenerze i elemencie w usłudze Azure Cosmos DB i zarządzać nim
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: anfeldma
ms.openlocfilehash: 72653a3b28181316a2bf7dd7e73f2685c3afcf73
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384266"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>Konfigurowanie czasu wygaśnięcia w usłudze Azure Cosmos DB

W usłudze Azure Cosmos DB możesz skonfigurować czas wygaśnięcia (Time to Live, TTL) na poziomie kontenera lub zastąpić go na poziomie elementu po ustawieniu TTL dla kontenera. TTL można skonfigurować dla kontenera przy użyciu witryny Azure Portal lub zestawu SDK właściwego dla używanego języka. Zastępowanie TTL na poziome elementu można skonfigurować za pomocą zestawów SDK.

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Włączanie czasu wygaśnięcia dla kontenera w witrynie Azure Portal

Wykonaj następujące czynności, aby włączyć czas wygaśnięcia dla kontenera bez terminu wygaśnięcia. Włącz tę funkcję, aby umożliwić zastępowanie TTL na poziomie elementu. Możesz także ustawić TTL, wprowadzając niezerową liczbę sekund.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

2. Utwórz nowe konto usługi Azure Cosmos lub wybierz istniejące.

3. Otwórz okienko **Eksplorator danych**.

4. Wybierz istniejący kontener, rozwiń go, a następnie zmodyfikuj następujące wartości:

   * Otwórz okno **Skalowanie i ustawienia**.
   * W obszarze **Ustawienia** znajdź pozycję **Czas wygaśnięcia**.
   * Wybierz pozycję **Włączony (brak wartości domyślnej)** lub wybierz pozycję **Włączony** i ustaw wartość TTL.
   * Kliknij przycisk **Zapisz**, aby zapisać zmiany.

   ![Konfigurowanie czasu wygaśnięcia w witrynie Azure Portal](./media/how-to-time-to-live/how-to-time-to-live-portal.png)

* Gdy DefaultTimeToLive jest null następnie czas na żywo jest wyłączony
* Gdy DefaultTimeToLive wynosi -1, to ustawienie Czas na żywo jest włączone (nie ma wartości domyślnej)
* Gdy DefaultTimeToLive ma inną wartość Int (z wyjątkiem 0), ustawienie Czas do żywo jest włączone

## <a name="enable-time-to-live-on-a-container-using-azure-cli-or-powershell"></a>Włącz czas, aby żyć w kontenerze przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell

Aby utworzyć lub włączyć czas wygaśnięcia w kontenerze zobacz,

* [Tworzenie kontenera z ttl przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#create-a-container-with-ttl)
* [Tworzenie kontenera z ttl przy użyciu programu PowerShell](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Włączanie czasu wygaśnięcia dla kontenera za pomocą zestawu SDK

### <a name="net-sdk-v2-microsoftazuredocumentdb"></a><a id="dotnet-enable-noexpiry"></a>Plik .NET SDK v2 (microsoft.Azure.DocumentDB)

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

### <a name="net-sdk-v3-microsoftazurecosmos"></a><a id="dotnet-enable-noexpiry"></a>.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// Create a new container with TTL enabled and without any expiration value
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = -1 //(never expire by default)
});
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-noexpiry"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(-1);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-noexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(-1);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Ustawianie czasu wygaśnięcia dla kontenera za pomocą zestawu SDK

Aby ustawić czas wygaśnięcia dla kontenera, należy podać niezerową liczbę dodatnią, określającą czas w sekundach. Na podstawie skonfigurowanej wartości TTL wszystkie elementy w kontenerze zostaną usunięte po upływie wskazanego czasu od wartości znacznika czasu ostatniej modyfikacji `_ts`.

### <a name="net-sdk-v2-microsoftazuredocumentdb"></a><a id="dotnet-enable-withexpiry"></a>Plik .NET SDK v2 (microsoft.Azure.DocumentDB)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24 // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition;
```

### <a name="net-sdk-v3-microsoftazurecosmos"></a><a id="dotnet-enable-withexpiry"></a>.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
});
```

### <a name="nodejs-sdk"></a><a id="nodejs-enable-withexpiry"></a>NodeJS SDK

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

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-defaultexpiry"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-defaultexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

## <a name="set-time-to-live-on-an-item"></a>Ustawianie czasu wygaśnięcia elementu

Oprócz skonfigurowania domyślnego czasu wygaśnięcia dla kontenera możesz ustawić czas wygaśnięcia konkretnego elementu. Ustawienie czasu wygaśnięcia na poziomie elementu spowoduje zastąpienie domyślnego czasu wygaśnięcia elementu w kontenerze.

* Aby ustawić czas wygaśnięcia elementu, podaj niezerową liczbę dodatnią oznaczającą czas w sekundach — element zostanie usunięty po upływie tego czasu od wartości znacznika czasu ostatniej modyfikacji `_ts`.

* Jeśli element nie ma pola TTL, będzie obowiązywał domyślny czas wygaśnięcia ustawiony dla kontenera.

* Jeśli czas wygaśnięcia zostanie wyłączony na poziomie kontenera, pole TTL elementu będzie ignorowane do momentu ponownego włączenia czasu wygaśnięcia dla kontenera.

### <a name="azure-portal"></a><a id="portal-set-ttl-item"></a>Portal Azure

Aby włączyć czas życia na elemencie, należy wykonać następujące czynności:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

2. Utwórz nowe konto usługi Azure Cosmos lub wybierz istniejące.

3. Otwórz okienko **Eksplorator danych**.

4. Wybierz istniejący kontener, rozwiń go, a następnie zmodyfikuj następujące wartości:

   * Otwórz okno **Skalowanie i ustawienia**.
   * W obszarze **Ustawienia** znajdź pozycję **Czas wygaśnięcia**.
   * Wybierz **on (nie domyślnie)** lub wybierz **włącz** i ustaw wartość TTL. 
   * Kliknij przycisk **Zapisz**, aby zapisać zmiany.

5. Następnie przejdź do elementu, dla którego chcesz ustawić `ttl` godzinę życia, dodaj właściwość i wybierz pozycję **Aktualizuj**. 

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

### <a name="net-sdk-any"></a><a id="dotnet-set-ttl-item"></a>Plik SDK .NET (dowolny)

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

### <a name="nodejs-sdk"></a><a id="nodejs-set-ttl-item"></a>NodeJS SDK

```javascript
const itemDefinition = {
          id: "doc",
          name: "sample Item",
          key: "value",
          ttl: 2
        };
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-itemexpiry"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String getId() {return this.id;}
    public void setId(String new_id) {this.id = new_id;}
    public String getCustomerId() {return this.customerId;}
    public void setCustomerId(String new_cid) {this.customerId = new_cid;}
    public Integer getTtl() {return this.ttl;}
    public void setTtl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);

```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-itemexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public void id(String new_id) {this.id = new_id;}
    public String customerId() {return this.customerId;}
    public void customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public void ttl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);

```

## <a name="reset-time-to-live"></a>Resetowanie czasu wygaśnięcia

Możesz zresetować czas wygaśnięcia elementu, wykonując operację zapisu lub aktualizacji na elemencie. Operacja zapisu lub aktualizacji spowoduje ustawienie znacznika czasu `_ts` na bieżący czas, od którego ponownie rozpocznie się odliczanie czasu wygaśnięcia elementu. Jeśli chcesz zmienić czas wygaśnięcia elementu, możesz zaktualizować pole TTL tak samo jak każde inne pole.

### <a name="net-sdk-v2-microsoftazuredocumentdb"></a><a id="dotnet-extend-ttl-item"></a>Plik .NET SDK v2 (microsoft.Azure.DocumentDB)

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

### <a name="net-sdk-v3-microsoftazurecosmos"></a><a id="dotnet-extend-ttl-item"></a>.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = 60 * 30 * 30; // update time to live
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-modifyitemexpiry"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
CosmosAsyncItemResponse<SalesOrder> itemResponse = container.readItem("SO05", new PartitionKey("CO18009186470"), SalesOrder.class)
        .flatMap(readResponse -> {
            SalesOrder salesOrder = readResponse.getItem();
            salesOrder.setTtl(60 * 30 * 30);
            return container.createItem(salesOrder);
}).block();
```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-modifyitemexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
container.getItem("SO05", new PartitionKey("CO18009186470")).read()
        .flatMap(readResponse -> {
            SalesOrder salesOrder = null;
            try {
                salesOrder = readResponse.properties().getObject(SalesOrder.class);
            } catch (Exception err) {

            }
            salesOrder.ttl(60 * 30 * 30);
            return container.createItem(salesOrder);
}).block();
```

## <a name="turn-off-time-to-live"></a>Wyłączanie wygasania

Jeśli ustawiono czas wygaśnięcia elementu, ale nie chcesz, aby ten element wygasał, możesz pobrać go, usunąć pole TTL i ponownie umieścić element na serwerze. Po usunięciu pola TTL z elementu stosowana jest do niego domyślna wartość czasu wygaśnięcia przypisana do kontenera. Ustaw wartość TTL na –1, aby element nie dziedziczył wartości czasu wygaśnięcia z kontenera i nie wygasał.

### <a name="net-sdk-v2-microsoftazuredocumentdb"></a><a id="dotnet-turn-off-ttl-item"></a>Plik .NET SDK v2 (microsoft.Azure.DocumentDB)

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

### <a name="net-sdk-v3-microsoftazurecosmos"></a><a id="dotnet-turn-off-ttl-item"></a>.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = null; // inherit the default TTL of the container
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-itemdefaultexpiry"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
CosmosAsyncItemResponse<SalesOrder> itemResponse = container.readItem("SO05", new PartitionKey("CO18009186470"), SalesOrder.class)
        .flatMap(readResponse -> {
            SalesOrder salesOrder = readResponse.getItem();
            salesOrder.setTtl(null);
            return container.createItem(salesOrder);
}).block();
```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-itemdefaultexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
container.getItem("SO05", new PartitionKey("CO18009186470")).read()
        .flatMap(readResponse -> {
            SalesOrder salesOrder = null;
            try {
                salesOrder = readResponse.properties().getObject(SalesOrder.class);
            } catch (Exception err) {

            }
            salesOrder.ttl(null);
            return container.createItem(salesOrder);
}).block();
```

## <a name="disable-time-to-live"></a>Wyłączanie czasu wygaśnięcia

Aby wyłączyć czas wygaśnięcia dla kontenera i zatrzymać proces w tle sprawdzający, czy elementy wygasły, należy usunąć właściwość `DefaultTimeToLive` z kontenera. Usunięcie tej właściwości ma inne skutki niż ustawienie jej na –1. Jeśli wartość właściwości zostanie ustawiona na –1, nowe elementy dodawane do kontenera nie będą wygasały, ale można będzie zastąpić tę wartość w przypadku wybranych elementów w kontenerze. Po usunięciu właściwości TTL z kontenera elementy nigdy nie wygasną, nawet jeśli są one jawnie zastąpione poprzednią domyślną wartość TTL.

### <a name="net-sdk-v2-microsoftazuredocumentdb"></a><a id="dotnet-disable-ttl"></a>Plik .NET SDK v2 (microsoft.Azure.DocumentDB)

```csharp
// Get the container, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

### <a name="net-sdk-v3-microsoftazurecosmos"></a><a id="dotnet-disable-ttl"></a>.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// Get the container, update DefaultTimeToLive to null
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Disable TTL
containerResponse.Resource.DefaultTimeToLive = null;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-disableexpiry"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
// Disable TTL
containerProperties.setDefaultTimeToLiveInSeconds(null);
// Update container settings
container.replace(containerProperties).block();
```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-disableexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
// Disable TTL
containerProperties.defaultTimeToLive(null);
// Update container settings
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat czasu wygaśnięcia, zobacz artykuł:

* [Time to live (Czas wygaśnięcia)](time-to-live.md)
