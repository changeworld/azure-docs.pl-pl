---
title: Powiązanie wejściowe usługi Azure Cosmos DB dla funkcji 2.x
description: Dowiedz się, jak używać powiązania wejściowego usługi Azure Cosmos DB w usłudze Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: eabcf40e28927919215979ccc46fa029d19adbfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943419"
---
# <a name="azure-cosmos-db-input-binding-for-azure-functions-2x"></a>Powiązanie danych wejściowych usługi Azure Cosmos DB dla usługi Azure Functions 2.x

Powiązanie wejściowe usługi Azure Cosmos DB używa interfejsu API SQL, aby pobrać co najmniej jeden dokument usługi Azure Cosmos DB, a następnie przekazuje go do parametru wejściowego funkcji. Identyfikator dokumentu lub parametry zapytania można określić na podstawie wyzwalacza wywołującego funkcję.

Aby uzyskać informacje na temat szczegółów konfiguracji i konfiguracji, zobacz [omówienie](./functions-bindings-cosmosdb-v2.md).

> [!NOTE]
> Jeśli kolekcja jest [podzielona na partycje,](../cosmos-db/partition-data.md#logical-partitions)operacje wyszukiwania należy również określić wartość klucza partycji.
>

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz kolejki, wyszukuj identyfikator z JSON](#queue-trigger-look-up-id-from-json-c)
* [Wyzwalacz HTTP, wyszukuj identyfikator z ciągu zapytania](#http-trigger-look-up-id-from-query-string-c)
* [Wyzwalacz HTTP, wyszukuj identyfikator z danych trasy](#http-trigger-look-up-id-from-route-data-c)
* [Wyzwalacz HTTP, wyszukuj identyfikator z danych trasy przy użyciu programu SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [Wyzwalacz HTTP, pobierz wiele dokumentów, używając SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [Wyzwalacz HTTP, pobierz wiele dokumentów, używając dokumentu DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Przykłady odnoszą się `ToDoItem` do prostego typu:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string PartitionKey { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Wyzwalacz kolejki, wyszukuj identyfikator z JSON 

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez komunikat kolejki, który zawiera obiekt JSON. Wyzwalacz kolejki analizuje JSON do obiektu `ToDoItemLookup`typu , który zawiera wartość klucza identyfikatora i partycji do wyszukania. Ten identyfikator i wartość klucza partycji `ToDoItem` są używane do pobierania dokumentu z określonej bazy danych i kolekcji.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }

        public string ToDoItemPartitionKeyValue { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}",
                PartitionKey = "{ToDoItemPartitionKeyValue}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId} Key={toDoItemLookup?.ToDoItemPartitionKeyValue}");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Wyzwalacz HTTP, wyszukuj identyfikator z ciągu zapytania

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania do określenia wartości klucza identyfikatora i partycji do wyszukania. Ten identyfikator i wartość klucza partycji `ToDoItem` są używane do pobierania dokumentu z określonej bazy danych i kolekcji.

>[!NOTE]
>W parametrze ciągu zapytania HTTP rozróżniana jest wielkość liter.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}",
                PartitionKey = "{Query.partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Wyzwalacz HTTP, wyszukuj identyfikator z danych trasy

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa danych trasy do określenia wartości klucza identyfikatora i partycji do wyszukania. Ten identyfikator i wartość klucza partycji `ToDoItem` są używane do pobierania dokumentu z określonej bazy danych i kolekcji.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{partitionKey}/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}",
                PartitionKey = "{partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Wyzwalacz HTTP, wyszukuj identyfikator z danych trasy przy użyciu programu SqlQuery

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa danych trasy do określenia identyfikatora do wyszukania. Ten identyfikator jest używany do `ToDoItem` pobierania dokumentu z określonej bazy danych i kolekcji.

W przykładzie pokazano, jak używać `SqlQuery` wyrażenia wiązania w parametrze. Dane trasy można przekazać `SqlQuery` do parametru, jak pokazano, ale obecnie [nie można przekazać wartości ciągu zapytania](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).

> [!NOTE]
> Jeśli chcesz wykonać kwerendę tylko przez identyfikator, zaleca się użycie wyszukiwania w górę, podobnie jak [w poprzednich przykładach,](#http-trigger-look-up-id-from-query-string-c)ponieważ zużywa mniej [jednostek żądań.](../cosmos-db/request-units.md) Operacje odczytu punktu (GET) są [bardziej wydajne](../cosmos-db/optimize-cost-queries.md) niż zapytania według identyfikatora.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequest req,
            [CosmosDB("ToDoItems", "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Wyzwalacz HTTP, pobierz wiele dokumentów, używając SqlQuery

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Kwerenda jest określona we właściwości atrybutu. `SqlQuery`

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>Wyzwalacz HTTP, pobierz wiele dokumentów, używając dokumentu DocumentClient

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Kod używa wystąpienia `DocumentClient` dostarczonego przez powiązanie usługi Azure Cosmos DB do odczytu listy dokumentów. Wystąpienie `DocumentClient` może być również używane do operacji zapisu.

> [!NOTE]
> Można również użyć interfejsu [IDocumentClient,](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.idocumentclient?view=azure-dotnet) aby ułatwić testowanie.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.LogInformation($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.LogInformation(result.Description);
                }
            }
            return new OkResult();
        }
    }
}
```

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz kolejki, wyszukuj identyfikator z ciągu](#queue-trigger-look-up-id-from-string-c-script)
* [Kolejka wyzwalacza, pobierz wiele dokumentów, używając SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Wyzwalacz HTTP, wyszukuj identyfikator z ciągu zapytania](#http-trigger-look-up-id-from-query-string-c-script)
* [Wyzwalacz HTTP, wyszukuj identyfikator z danych trasy](#http-trigger-look-up-id-from-route-data-c-script)
* [Wyzwalacz HTTP, pobierz wiele dokumentów, używając SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Wyzwalacz HTTP, pobierz wiele dokumentów, używając dokumentu DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Przykłady wyzwalaczy HTTP odnoszą `ToDoItem` się do prostego typu:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>Wyzwalacz kolejki, wyszukuj identyfikator z ciągu

W poniższym przykładzie pokazano powiązanie wejściowe usługi Cosmos DB w pliku *function.json* i [funkcję skryptu Języka C#,](functions-reference-csharp.md) która używa powiązania. Funkcja odczytuje pojedynczy dokument i aktualizuje wartość tekstową dokumentu.

Oto dane powiązania w pliku *function.json:*

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```
W sekcji [konfiguracji](#configuration) opisano te właściwości.

Oto kod skryptu języka C#:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Kolejka wyzwalacza, pobierz wiele dokumentów, używając SqlQuery

W poniższym przykładzie pokazano powiązanie wejściowe usługi Azure Cosmos DB w pliku *function.json* i [funkcję skryptu Języka C#,](functions-reference-csharp.md) która używa powiązania. Funkcja pobiera wiele dokumentów określonych przez kwerendę SQL, używając wyzwalacza kolejki w celu dostosowania parametrów kwerendy.

Wyzwalacz kolejki zawiera `departmentId`parametr . Komunikat kolejki `{ "departmentId" : "Finance" }` zwróci wszystkie rekordy dla działu finansowego.

Oto dane powiązania w pliku *function.json:*

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

W sekcji [konfiguracji](#configuration) opisano te właściwości.

Oto kod skryptu języka C#:

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Wyzwalacz HTTP, wyszukuj identyfikator z ciągu zapytania

W poniższym przykładzie przedstawiono [funkcję skryptu Języka C#,](functions-reference-csharp.md) która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania do określenia wartości klucza identyfikatora i partycji do wyszukania. Ten identyfikator i wartość klucza partycji `ToDoItem` są używane do pobierania dokumentu z określonej bazy danych i kolekcji.

Oto plik *function.json:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey" : "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Oto kod skryptu języka C#:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Wyzwalacz HTTP, wyszukuj identyfikator z danych trasy

W poniższym przykładzie przedstawiono [funkcję skryptu Języka C#,](functions-reference-csharp.md) która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa danych trasy do określenia wartości klucza identyfikatora i partycji do wyszukania. Ten identyfikator i wartość klucza partycji `ToDoItem` są używane do pobierania dokumentu z określonej bazy danych i kolekcji.

Oto plik *function.json:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Oto kod skryptu języka C#:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Wyzwalacz HTTP, pobierz wiele dokumentów, używając SqlQuery

W poniższym przykładzie przedstawiono [funkcję skryptu Języka C#,](functions-reference-csharp.md) która pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Kwerenda jest określona we właściwości atrybutu. `SqlQuery`

Oto plik *function.json:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

Oto kod skryptu języka C#:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.LogInformation(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>Wyzwalacz HTTP, pobierz wiele dokumentów, używając dokumentu DocumentClient

W poniższym przykładzie przedstawiono [funkcję skryptu Języka C#,](functions-reference-csharp.md) która pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Kod używa wystąpienia `DocumentClient` dostarczonego przez powiązanie usługi Azure Cosmos DB do odczytu listy dokumentów. Wystąpienie `DocumentClient` może być również używane do operacji zapisu.

Oto plik *function.json:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

Oto kod skryptu języka C#:

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.LogInformation($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.LogInformation(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Ta sekcja zawiera następujące przykłady, które odczytują pojedynczy dokument, określając wartość identyfikatora z różnych źródeł:

* [Wyzwalacz kolejki, wyszukuj identyfikator z JSON](#queue-trigger-look-up-id-from-json-javascript)
* [Wyzwalacz HTTP, wyszukuj identyfikator z ciągu zapytania](#http-trigger-look-up-id-from-query-string-javascript)
* [Wyzwalacz HTTP, wyszukuj identyfikator z danych trasy](#http-trigger-look-up-id-from-route-data-javascript)
* [Kolejka wyzwalacza, pobierz wiele dokumentów, używając SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Wyzwalacz kolejki, wyszukuj identyfikator z JSON

W poniższym przykładzie pokazano powiązanie wejściowe usługi Cosmos DB w pliku *function.json* i [funkcję JavaScript,](functions-reference-node.md) która używa powiązania. Funkcja odczytuje pojedynczy dokument i aktualizuje wartość tekstową dokumentu.

Oto dane powiązania w pliku *function.json:*

```json
{
    "name": "inputDocumentIn",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

W sekcji [konfiguracji](#configuration) opisano te właściwości.

Oto kod JavaScript:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Wyzwalacz HTTP, wyszukuj identyfikator z ciągu zapytania

W poniższym przykładzie przedstawiono [funkcję JavaScript,](functions-reference-node.md) która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania do określenia wartości klucza identyfikatora i partycji do wyszukania. Ten identyfikator i wartość klucza partycji `ToDoItem` są używane do pobierania dokumentu z określonej bazy danych i kolekcji.

Oto plik *function.json:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Oto kod JavaScript:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Wyzwalacz HTTP, wyszukuj identyfikator z danych trasy

W poniższym przykładzie przedstawiono [funkcję JavaScript,](functions-reference-node.md) która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa danych trasy do określenia wartości klucza identyfikatora i partycji do wyszukania. Ten identyfikator i wartość klucza partycji `ToDoItem` są używane do pobierania dokumentu z określonej bazy danych i kolekcji.

Oto plik *function.json:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Oto kod JavaScript:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Kolejka wyzwalacza, pobierz wiele dokumentów, używając SqlQuery

W poniższym przykładzie pokazano powiązanie wejściowe usługi Azure Cosmos DB w pliku *function.json* i [funkcję JavaScript,](functions-reference-node.md) która używa powiązania. Funkcja pobiera wiele dokumentów określonych przez kwerendę SQL, używając wyzwalacza kolejki w celu dostosowania parametrów kwerendy.

Wyzwalacz kolejki zawiera `departmentId`parametr . Komunikat kolejki `{ "departmentId" : "Finance" }` zwróci wszystkie rekordy dla działu finansowego.

Oto dane powiązania w pliku *function.json:*

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

W sekcji [konfiguracji](#configuration) opisano te właściwości.

Oto kod JavaScript:

```javascript
    module.exports = function (context, input) {
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }
        context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

Ta sekcja zawiera następujące przykłady, które odczytują pojedynczy dokument, określając wartość identyfikatora z różnych źródeł:

* [Wyzwalacz kolejki, wyszukuj identyfikator z JSON](#queue-trigger-look-up-id-from-json-python)
* [Wyzwalacz HTTP, wyszukuj identyfikator z ciągu zapytania](#http-trigger-look-up-id-from-query-string-python)
* [Wyzwalacz HTTP, wyszukuj identyfikator z danych trasy](#http-trigger-look-up-id-from-route-data-python)
* [Kolejka wyzwalacza, pobierz wiele dokumentów, używając SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Wyzwalacz kolejki, wyszukuj identyfikator z JSON

W poniższym przykładzie pokazano powiązanie wejściowe usługi Cosmos DB w pliku *function.json* i [funkcję Języka Python,](functions-reference-python.md) która używa powiązania. Funkcja odczytuje pojedynczy dokument i aktualizuje wartość tekstową dokumentu.

Oto dane powiązania w pliku *function.json:*

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "$return",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

W sekcji [konfiguracji](#configuration) opisano te właściwości.

Oto kod Pythona:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Wyzwalacz HTTP, wyszukuj identyfikator z ciągu zapytania

W poniższym przykładzie przedstawiono [funkcję języka Python,](functions-reference-python.md) która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania do określenia wartości klucza identyfikatora i partycji do wyszukania. Ten identyfikator i wartość klucza partycji `ToDoItem` są używane do pobierania dokumentu z określonej bazy danych i kolekcji.

Oto plik *function.json:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": true,
  "scriptFile": "__init__.py"
}
```

Oto kod Pythona:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])

    return 'OK'
```

<a id="http-trigger-look-up-id-from-route-data-python"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Wyzwalacz HTTP, wyszukuj identyfikator z danych trasy

W poniższym przykładzie przedstawiono [funkcję języka Python,](functions-reference-python.md) która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa danych trasy do określenia wartości klucza identyfikatora i partycji do wyszukania. Ten identyfikator i wartość klucza partycji `ToDoItem` są używane do pobierania dokumentu z określonej bazy danych i kolekcji.

Oto plik *function.json:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Oto kod Pythona:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])
    return 'OK'
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Kolejka wyzwalacza, pobierz wiele dokumentów, używając SqlQuery

W poniższym przykładzie pokazano powiązanie wejściowe usługi Azure Cosmos DB w pliku *function.json* i [funkcję Języka Python,](functions-reference-python.md) która używa powiązania. Funkcja pobiera wiele dokumentów określonych przez kwerendę SQL, używając wyzwalacza kolejki w celu dostosowania parametrów kwerendy.

Wyzwalacz kolejki zawiera `departmentId`parametr . Komunikat kolejki `{ "departmentId" : "Finance" }` zwróci wszystkie rekordy dla działu finansowego.

Oto dane powiązania w pliku *function.json:*

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

W sekcji [konfiguracji](#configuration) opisano te właściwości.

Oto kod Pythona:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

# <a name="java"></a>[Java](#tab/java)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz HTTP, wyszukuj identyfikator z ciągu zapytania — parametr String](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [Wyzwalacz HTTP, wyszukuj identyfikator z ciągu zapytania - parametr POJO](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [Wyzwalacz HTTP, wyszukuj identyfikator z danych trasy](#http-trigger-look-up-id-from-route-data-java)
* [Wyzwalacz HTTP, wyszukuj identyfikator z danych trasy przy użyciu programu SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [Wyzwalacz HTTP, pobierz wiele dokumentów z danych trasy, używając SqlQuery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

Przykłady odnoszą się `ToDoItem` do prostego typu:

```java
public class ToDoItem {

  private String id;
  private String description;

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }

  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}
```

<a id="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>Wyzwalacz HTTP, wyszukuj identyfikator z ciągu zapytania — parametr String

W poniższym przykładzie przedstawiono funkcję Języka Java, która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania do określenia wartości klucza identyfikatora i partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania dokumentu z określonej bazy danych i kolekcji w formie string.

```java
public class DocByIdFromQueryString {

    @FunctionName("DocByIdFromQueryString")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

W [bibliotece środowiska wykonawczego](/java/api/overview/azure/functions/runtime)funkcji `@CosmosDBInput` Java użyj adnotacji na temat parametrów funkcji, których wartość pochodziłaby z usługi Cosmos DB.  Ta adnotacja może być używana z natywnymi typami `Optional<T>`Java, POJO lub wartościami nullable przy użyciu .

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>Wyzwalacz HTTP, wyszukuj identyfikator z ciągu zapytania - parametr POJO

W poniższym przykładzie przedstawiono funkcję Języka Java, która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania do określenia wartości klucza identyfikatora i partycji do wyszukania. Ten identyfikator i wartość klucza partycji używane do pobierania dokumentu z określonej bazy danych i kolekcji. Dokument jest następnie konwertowany ```ToDoItem``` na wystąpienie wcześniej utworzonego POJO i przekazywany jako argument do funkcji.

```java
public class DocByIdFromQueryStringPojo {

    @FunctionName("DocByIdFromQueryStringPojo")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Item from the database is " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

<a id="http-trigger-look-up-id-from-route-data-java"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Wyzwalacz HTTP, wyszukuj identyfikator z danych trasy

W poniższym przykładzie przedstawiono funkcję Języka Java, która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa parametru trasy do określenia wartości klucza identyfikatora i partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania dokumentu z ```Optional<String>```określonej bazy danych i kolekcji, zwracając go jako .

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{partitionKeyValue}/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Wyzwalacz HTTP, wyszukuj identyfikator z danych trasy przy użyciu programu SqlQuery

W poniższym przykładzie przedstawiono funkcję Języka Java, która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa parametru trasy do określenia identyfikatora do wyszukania. Ten identyfikator jest używany do pobierania dokumentu z określonej bazy danych i ```ToDoItem[]```kolekcji, konwertując wynik ustawiony na , ponieważ wiele dokumentów może zostać zwróconych, w zależności od kryteriów kwerendy.

> [!NOTE]
> Jeśli chcesz wykonać kwerendę tylko przez identyfikator, zaleca się użycie wyszukiwania w górę, podobnie jak [w poprzednich przykładach,](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)ponieważ zużywa mniej [jednostek żądań.](../cosmos-db/request-units.md) Operacje odczytu punktu (GET) są [bardziej wydajne](../cosmos-db/optimize-cost-queries.md) niż zapytania według identyfikatora.
>

```java
public class DocByIdFromRouteSqlQuery {

    @FunctionName("DocByIdFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems2/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where r.id = {id}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Items from the database are " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>Wyzwalacz HTTP, pobierz wiele dokumentów z danych trasy, używając SqlQuery

W poniższym przykładzie przedstawiono funkcję Języka Java, która pobiera wiele dokumentów. Funkcja jest wyzwalana przez żądanie HTTP, które ```desc``` używa parametru trasy do ```description``` określenia ciągu do wyszukiwania w polu. Wyszukiwany termin jest używany do pobierania kolekcji dokumentów z określonej bazy danych ```ToDoItem[]``` i kolekcji, konwertując wynik ustawiony na a i przekazując go jako argument do funkcji.

```java
public class DocsFromRouteSqlQuery {

    @FunctionName("DocsFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems3/{desc}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where contains(r.description, {desc})",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] items,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Number of items from the database is " + (items == null ? 0 : items.length));

        // Convert and display
        if (items == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("No documents found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(items)
                          .build();
        }
    }
}
 ```

 ---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C #](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [CosmosDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs)

Konstruktor atrybutu przyjmuje nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i innych właściwościach, które można skonfigurować, zobacz [następującą sekcję konfiguracji](#configuration).

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez Pythona.

# <a name="java"></a>[Java](#tab/java)

Z [biblioteki środowiska wykonawczego](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)funkcji `@CosmosDBOutput` Java użyj adnotacji na temat parametrów zapisywanych w usłudze Cosmos DB. Typ parametru adnotacji `OutputBinding<T>`powinien `T` być , gdzie jest natywny typ Java lub POJO.

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `CosmosDB` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typu**     | Nie dotyczy | Musi być `cosmosDB`ustawiona na .        |
|**Kierunku**     | Nie dotyczy | Musi być `in`ustawiona na .         |
|**Nazwa**     | Nie dotyczy | Nazwa parametru wiązania reprezentującego dokument w funkcji.  |
|**Databasename** |**DatabaseName** |Baza danych zawierająca dokument.        |
|**Collectionname** |**CollectionName** | Nazwa kolekcji, która zawiera dokument. |
|**Identyfikator**    | **Identyfikator** | Identyfikator dokumentu do pobrania. Ta właściwość obsługuje [wyrażenia wiązania](./functions-bindings-expressions-patterns.md). Nie ustawiaj właściwości `id` **sqlQuery i sqlQuery.** Jeśli nie ustawisz jednego, cała kolekcja zostanie pobrana. |
|**sqlQuery ( sqlQuery )**  |**Zapytania SqlQuery**  | Kwerenda SQL usługi Azure Cosmos DB używana do pobierania wielu dokumentów. Właściwość obsługuje powiązania środowiska wykonawczego, jak `SELECT * FROM c where c.departmentId = {departmentId}`w tym przykładzie: . Nie ustawiaj zarówno `id` `sqlQuery` właściwości, jak i właściwości. Jeśli nie ustawisz jednego, cała kolekcja zostanie pobrana.|
|**connectionStringSetting**     |**ConnectionStringSetting (Ustawienia połączeń)**|Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi Azure Cosmos DB. |
|**partitionKey (klucz)**|**PartitionKey**|Określa wartość klucza partycji dla wyszukiwania. Może zawierać parametry wiązania. Jest to wymagane dla wyszukiwania w kolekcjach [podzielonych na partycje.](../cosmos-db/partition-data.md#logical-partitions)|
|**preferowaneLokacje**| **PreferowaneLocations**| (Opcjonalnie) Definiuje preferowane lokalizacje (regiony) dla kont bazy danych replikowanych geograficznie w usłudze Usługi Azure Cosmos DB. Wartości powinny być oddzielone przecinkami. Na przykład "Wschodnie stany USA, południowo-środkowe stany USA,Europa Północna". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Sposób użycia

# <a name="c"></a>[C #](#tab/csharp)

Gdy funkcja kończy się pomyślnie, wszelkie zmiany wprowadzone do dokumentu wejściowego za pośrednictwem nazwanych parametrów wejściowych są automatycznie utrwalone.

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Gdy funkcja kończy się pomyślnie, wszelkie zmiany wprowadzone do dokumentu wejściowego za pośrednictwem nazwanych parametrów wejściowych są automatycznie utrwalone.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Aktualizacje nie są dokonywane automatycznie po zamknięciu funkcji. Zamiast tego `context.bindings.<documentName>In` użyj `context.bindings.<documentName>Out` i do aktualizacji. Zobacz przykład JavaScript.

# <a name="python"></a>[Python](#tab/python)

Dane są udostępniane funkcji za `DocumentList` pomocą parametru. Zmiany wprowadzone w dokumencie nie są automatycznie utrwalane.

# <a name="java"></a>[Java](#tab/java)

Z [biblioteki środowiska wykonawczego](https://docs.microsoft.com/java/api/overview/azure/functions/runtime) [@CosmosDBInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) funkcji Java adnotacja udostępnia dane usługi Cosmos DB do funkcji. Ta adnotacja może być używana z natywnymi typami `Optional<T>`Java, POJO lub wartościami nullable przy użyciu .

---

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie funkcji podczas tworzenia lub modyfikowanie dokumentu usługi Azure Cosmos DB (wyzwalacz)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Zapisywanie zmian w dokumencie usługi Azure Cosmos DB (powiązanie danych wyjściowych)](./functions-bindings-cosmosdb-v2-output.md)