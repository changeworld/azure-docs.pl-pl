---
title: Dowiedz się, jak skonfigurować czas wygaśnięcia w usłudze Azure Cosmos DB i jak nim zarządzać
description: Dowiedz się, jak skonfigurować czas wygaśnięcia w usłudze Azure Cosmos DB i jak nim zarządzać
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: b5eb57562dac498c0e5bf2960007f52ad1117ac2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244765"
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

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Włączanie czasu wygaśnięcia dla kontenera za pomocą zestawu SDK

### <a id="dotnet-enable-noexpiry"></a>.NET SDK

```csharp
// Create a new collection with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition,
    new RequestOptions { OfferThroughput = 20000 });
```

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Ustawianie czasu wygaśnięcia dla kontenera za pomocą zestawu SDK

### <a id="dotnet-enable-withexpiry"></a>.NET SDK

Aby ustawić czas wygaśnięcia dla kontenera, należy podać niezerową liczbę dodatnią, określającą czas w sekundach. Na podstawie skonfigurowanej wartości TTL wszystkie elementy w kontenerze zostaną usunięte po upływie wskazanego czasu od wartości znacznika czasu ostatniej modyfikacji `_ts`.

```csharp
// Create a new collection with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition,
    new RequestOptions { OfferThroughput = 20000 });
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

Aby włączyć czas wygaśnięcia elementu, użyj następujących kroków:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Utwórz nowe konto usługi Azure Cosmos lub wybierz istniejące.

3. Otwórz okienko **Eksplorator danych**.

4. Wybierz istniejący kontener, rozwiń go, a następnie zmodyfikuj następujące wartości:

   * Otwórz okno **Skalowanie i ustawienia**.
   * W obszarze **Ustawienia** znajdź pozycję **Czas wygaśnięcia**.
   * Wybierz **na (Brak wartości domyślnej)** lub wybierz **na** i ustaw wartość czasu wygaśnięcia. 
   * Kliknij przycisk **Zapisz**, aby zapisać zmiany.

5. Następnie przejdź do elementu, dla którego chcesz ustawić czas na żywo, dodawanie `ttl` właściwości i wybierz pozycję **aktualizacji**. 

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

### <a id="dotnet-set-ttl-item"></a>.NET SDK

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

### <a id="dotnet-extend-ttl-item"></a>.NET SDK

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

## <a name="turn-off-time-to-live"></a>Wyłączanie wygasania

Jeśli ustawiono czas wygaśnięcia elementu, ale nie chcesz, aby ten element wygasał, możesz pobrać go, usunąć pole TTL i ponownie umieścić element na serwerze. Po usunięciu pola TTL z elementu stosowana jest do niego domyślna wartość czasu wygaśnięcia przypisana do kontenera. Ustaw wartość TTL na –1, aby element nie dziedziczył wartości czasu wygaśnięcia z kontenera i nie wygasał.

### <a id="dotnet-turn-off-ttl-item"></a>.NET SDK

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = null; // inherit the default TTL of the collection

response = await client.ReplaceDocumentAsync(readDocument);
```

## <a name="disable-time-to-live"></a>Wyłączanie czasu wygaśnięcia

Aby wyłączyć czas wygaśnięcia dla kontenera i zatrzymać proces w tle sprawdzający, czy elementy wygasły, należy usunąć właściwość `DefaultTimeToLive` z kontenera. Usunięcie tej właściwości ma inne skutki niż ustawienie jej na –1. Jeśli wartość właściwości zostanie ustawiona na –1, nowe elementy dodawane do kontenera nie będą wygasały, ale można będzie zastąpić tę wartość w przypadku wybranych elementów w kontenerze. Po usunięciu właściwości TTL z kontenera elementy nigdy nie wygasa, nawet jeśli są jawnie przesłaniane poprzedniego wartość domyślna czasu wygaśnięcia.

### <a id="dotnet-disable-ttl"></a>.NET SDK

```csharp
// Get the collection, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat czasu wygaśnięcia, zobacz artykuł:

* [Time to live (Czas wygaśnięcia)](time-to-live.md)
