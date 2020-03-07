---
title: Zarządzanie spójnością w Azure Cosmos DB
description: Informacje o konfigurowaniu poziomów spójności i zarządzaniu nimi w Azure Cosmos DB przy użyciu Azure Portal, zestawu .NET SDK, zestawu Java SDK i różnych zestawów SDK
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 68be15e1ffd9093ab67fc046edaad991d633ca7e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385712"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Zarządzanie poziomami spójności w usłudze Azure Cosmos DB

W tym artykule wyjaśniono, jak zarządzać poziomami spójności w usłudze Azure Cosmos DB. Dowiesz się, jak skonfigurować domyślny poziom spójności, zastąpić spójność domyślną, ręcznie zarządzać tokenami sesji oraz jak rozumieć metrykę PBS (Probabilistically Bounded Staleness).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Konfigurowanie domyślnego poziomu spójności

[Domyślny poziom spójności](consistency-levels.md) jest poziomem spójności używanym domyślnie przez klientów. Klienci zawsze mogą ją przesłonić.

### <a name="cli"></a>Interfejs wiersza polecenia

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Eventual
```

### <a name="powershell"></a>Program PowerShell

Ten przykład tworzy nowe konto usługi Azure Cosmos z włączoną obsługą wielu regionów zapisu w regionach Wschodnie stany USA i zachodnie stany USA. Domyślny poziom spójności jest ustawiony na spójność *sesji* .

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="Session"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="azure-portal"></a>Portalu Azure

Aby wyświetlić lub zmodyfikować domyślny poziom spójności, zaloguj się do witryny Azure Portal. Znajdź konto usługi Azure Cosmos i Otwórz domyślne okienko **spójności** . Wybierz odpowiedni poziom spójności jako nowe ustawienie domyślne, a następnie wybierz pozycję **Zapisz**. Azure Portal udostępnia również wizualizację różnych poziomów spójności przy użyciu notatek muzycznych. 

![Menu spójności w witrynie Azure Portal](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Zastępowanie domyślnego poziomu spójności

Klienci mogą zastąpić domyślny poziom spójności, który jest ustawiony przez usługę. Poziom spójności można ustawić dla każdego żądania, który zastępuje domyślny poziom spójności ustawiony na poziomie konta.

### <a id="override-default-consistency-dotnet"></a>ZESTAW .NET SDK V2

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

### <a id="override-default-consistency-dotnet-v3"></a>ZESTAW .NET SDK V3

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item, 
        new PartitionKey(itemPartitionKey), 
        requestOptions);
```

### <a id="override-default-consistency-java-async"></a>Java Async SDK

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

### <a id="override-default-consistency-java-sync"></a>Java Sync SDK

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```

### <a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>Korzystanie z tokenów sesji

Jeden z poziomów spójności w Azure Cosmos DB jest spójność *sesji* . Jest to domyślny poziom stosowany domyślnie do kont Cosmos. Podczas pracy ze spójnością *sesji* klient będzie używać tokenu sesji wewnętrznie w przypadku każdego żądania odczytu/zapytania, aby zapewnić zachowanie ustawienia poziomu spójności.

Aby ręcznie zarządzać tokenami sesji, pobieraj token sesji z odpowiedzi i ustawiaj go dla poszczególnych żądań. Jeśli nie chcesz ręcznie zarządzać tokenami sesji, nie musisz korzystać z tych przykładów. Zestaw SDK automatycznie śledzi tokeny sesji. Jeśli nie ustawisz tokenu sesji ręcznie, zestaw SDK domyślnie użyje najnowszego tokenu sesji.

### <a id="utilize-session-tokens-dotnet"></a>ZESTAW .NET SDK V2

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-dotnet-v3"></a>ZESTAW .NET SDK V3

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
```

### <a id="utilize-session-tokens-java-async"></a>Java Async SDK

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

### <a id="utilize-session-tokens-java-sync"></a>Java Sync SDK

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```

### <a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a id="utilize-session-tokens-python"></a>Python SDK

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>Monitorowanie metryki PBS (Probabilistically Bounded Staleness)

Jak ostateczna jest spójność ostateczna? W przypadku średniego przypadku można zaoferować nieaktualność w odniesieniu do historii wersji i czasu. Metryka [**probabilistically ograniczona (PBS)** ](https://pbs.cs.berkeley.edu/) próbuje określić prawdopodobieństwo nieodświeżoności i pokazuje ją jako metrykę. Aby wyświetlić metrykę usługi PBS, przejdź do swojego konta usługi Azure Cosmos w Azure Portal. Otwórz okienko **metryki** i wybierz kartę **spójność** . Spójrz na wykres o nazwie **prawdopodobieństwo silnie spójnych odczytów w oparciu o obciążenie (zobacz PBS)** .

![Wykres PBS w witrynie Azure Portal](./media/how-to-manage-consistency/pbs-metric.png)


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zarządzaniu konfliktami danych lub przejdź do następnej kluczowej koncepcji w usłudze Azure Cosmos DB. Zobacz następujące artykuły:

* [Poziomy spójności w Azure Cosmos DB](consistency-levels.md)
* [Zarządzanie konfliktami między regionami](how-to-manage-conflicts.md)
* [Partycjonowanie i dystrybucja danych](partition-data.md)
* [Założenia dotyczące spójności w nowoczesnych systemach rozproszonej bazy danych](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [Wysoka dostępność](high-availability.md)
* [Azure Cosmos DB umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
