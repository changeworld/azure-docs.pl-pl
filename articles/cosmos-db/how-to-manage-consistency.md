---
title: Dowiedz się, jak zarządzać spójnością w usłudze Azure Cosmos DB
description: Dowiedz się, jak zarządzać spójnością w usłudze Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/08/2019
ms.author: mjbrown
ms.openlocfilehash: 511a12cd7f1e88a95342cf5129142791c6d50b31
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68000884"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Zarządzanie poziomami spójności w usłudze Azure Cosmos DB

W tym artykule wyjaśniono, jak zarządzać poziomami spójności w usłudze Azure Cosmos DB. Dowiesz się, jak skonfigurować domyślny poziom spójności, zastąpić spójność domyślną, ręcznie zarządzać tokenami sesji oraz jak rozumieć metrykę PBS (Probabilistically Bounded Staleness).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Konfigurowanie domyślnego poziomu spójności

[Domyślny poziom spójności](consistency-levels.md) jest poziomu spójności używanego przez klientów domyślnie. Klienci zawsze można zmienić go.

### <a name="cli"></a>Interfejs wiersza polecenia

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Eventual
```

### <a name="powershell"></a>PowerShell

W tym przykładzie tworzy nowe konto usługi Azure Cosmos z wieloma regionami zapisu włączone w regionach wschodnie stany USA i zachodnie stany USA. Ustawiono domyślny poziom spójności *sesji* spójności.

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

### <a name="azure-portal"></a>Azure Portal

Aby wyświetlić lub zmodyfikować domyślny poziom spójności, zaloguj się do witryny Azure Portal. Znajdź swoje konto usługi Azure Cosmos, a następnie otwórz **domyślna spójność** okienka. Wybierz odpowiedni poziom spójności jako nowe ustawienie domyślne, a następnie wybierz pozycję **Zapisz**. Azure portal udostępnia również wizualizację poziomów spójności różnych oraz utworów muzycznych oznaczenia. 

![Menu spójności w witrynie Azure Portal](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Zastępowanie domyślnego poziomu spójności

Klienci mogą zastąpić domyślny poziom spójności, który jest ustawiony przez usługę. Poziom spójności można ustawić na żądanie, który zastępuje domyślny poziom spójności na poziomie konta.

### <a id="override-default-consistency-dotnet"></a>ZESTAW SDK PLATFORMY .NET W WERSJI 2

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

### <a id="override-default-consistency-dotnet-v3"></a>ZESTAW SDK PLATFORMY .NET W WERSJI 3

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
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>Korzystanie z tokenów sesji

Jeden z poziomów spójności w usłudze Azure Cosmos DB jest *sesji* spójności. Jest to domyślny poziom domyślnie stosowane do konta usługi Cosmos. Podczas pracy z *sesji* spójności, klient będzie używać tokenu sesji wewnętrznie z każdym żądaniem odczytu/zapytań do zapewnienia utrzymania Ustawianie poziomu spójności.

Aby ręcznie zarządzać tokenami sesji, pobieraj token sesji z odpowiedzi i ustawiaj go dla poszczególnych żądań. Jeśli nie chcesz ręcznie zarządzać tokenami sesji, nie musisz korzystać z tych przykładów. Zestaw SDK automatycznie śledzi tokeny sesji. Jeśli nie ustawisz tokenu sesji ręcznie, zestaw SDK domyślnie użyje najnowszego tokenu sesji.

### <a id="utilize-session-tokens-dotnet"></a>ZESTAW SDK PLATFORMY .NET W WERSJI 2

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-dotnet-v3"></a>ZESTAW SDK PLATFORMY .NET W WERSJI 3

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

Jak ostatecznej jest spójność ostateczna? W przypadku średniej może oferujemy granice nieaktualność względem historii wersji i godziny. [ **Probabilistically powiązana nieaktualność książek Telefonicznych** ](https://pbs.cs.berkeley.edu/) metryki podejmuje próbę określają ilościowo prawdopodobieństwo nieaktualność i pojawia się jako metrykę. Aby wyświetlić metryki PBS, przejdź do swojego konta usługi Azure Cosmos w witrynie Azure portal. Otwórz okienko **Metryki** i wybierz kartę **Spójność**. Spójrz na wykres o nazwie **Probability of strongly consistent reads based on your workload (see PBS)** (Prawdopodobieństwo silnie spójnych odczytów na podstawie obciążenia (zobacz PBS)).

![Wykres PBS w witrynie Azure Portal](./media/how-to-manage-consistency/pbs-metric.png)


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zarządzaniu konfliktami danych lub przejdź do następnej kluczowej koncepcji w usłudze Azure Cosmos DB. Zobacz następujące artykuły:

* [Poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md)
* [Zarządzanie konfliktami między regionami](how-to-manage-conflicts.md)
* [Partycjonowanie i dystrybucja danych](partition-data.md)
* [Wady i zalety spójności w nowoczesnych rozproszoną bazę danych, projektowanie systemów](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [Wysoka dostępność](high-availability.md)
* [Azure Cosmos DB w umowie SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
