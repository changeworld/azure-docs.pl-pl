---
title: Powiązanie danych wyjściowych usługi Azure Cosmos DB dla funkcji 2.x
description: Dowiedz się, jak używać powiązania danych wyjściowych usługi Azure Cosmos DB w usłudze Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: 636903c20e07f11a2fd919654cfaa62037171f20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277767"
---
# <a name="azure-cosmos-db-output-binding-for-azure-functions-2x"></a>Powiązanie danych wyjściowych usługi Azure Cosmos DB dla usługi Azure Functions 2.x

Powiązanie danych wyjściowych usługi Azure Cosmos DB umożliwia pisanie nowego dokumentu w bazie danych usługi Azure Cosmos DB przy użyciu interfejsu API SQL.

Aby uzyskać informacje na temat szczegółów konfiguracji i konfiguracji, zobacz [omówienie](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz kolejki, napisz jeden doc](#queue-trigger-write-one-doc-c)
* [Wyzwalacz kolejki, zapis dokumentów przy użyciu IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c)

Przykłady odnoszą się `ToDoItem` do prostego typu:

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

<a id="queue-trigger-write-one-doc-c"></a>

### <a name="queue-trigger-write-one-doc"></a>Wyzwalacz kolejki, napisz jeden doc

W poniższym przykładzie przedstawiono [funkcję Języka C#,](functions-dotnet-class-library.md) która dodaje dokument do bazy danych przy użyciu danych dostarczonych w wiadomości z magazynu kolejki.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

<a id="queue-trigger-write-docs-using-iasynccollector-c"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wyzwalacz kolejki, zapis dokumentów przy użyciu IAsyncCollector

W poniższym przykładzie przedstawiono [funkcję Języka C#,](functions-dotnet-class-library.md) która dodaje kolekcję dokumentów do bazy danych przy użyciu danych podanych w wiadomości kolejki JSON.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz kolejki, napisz jeden doc](#queue-trigger-write-one-doc-c-script)
* [Wyzwalacz kolejki, zapis dokumentów przy użyciu IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>Wyzwalacz kolejki, napisz jeden doc

W poniższym przykładzie pokazano powiązanie danych wyjściowych usługi Azure Cosmos DB w pliku *function.json* i [funkcję skryptu Języka C#,](functions-reference-csharp.md) która używa powiązania. Funkcja używa powiązania wejściowego kolejki dla kolejki, która odbiera JSON w następującym formacie:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funkcja tworzy dokumenty usługi Azure Cosmos DB w następującym formacie dla każdego rekordu:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Oto dane powiązania w pliku *function.json:*

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

W sekcji [konfiguracji](#configuration) opisano te właściwości.

Oto kod skryptu języka C#:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

<a id="queue-trigger-write-docs-using-iasynccollector-c-script"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wyzwalacz kolejki, zapis dokumentów przy użyciu IAsyncCollector

Aby utworzyć wiele dokumentów, `ICollector<T>` można `IAsyncCollector<T>` `T` powiązać z lub gdzie jest jednym z obsługiwanych typów.

W tym przykładzie `ToDoItem` odnosi się do prostego typu:

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

Oto plik function.json:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Oto kod skryptu języka C#:

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie danych wyjściowych usługi Azure Cosmos DB w pliku *function.json* i [funkcję JavaScript,](functions-reference-node.md) która używa powiązania. Funkcja używa powiązania wejściowego kolejki dla kolejki, która odbiera JSON w następującym formacie:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funkcja tworzy dokumenty usługi Azure Cosmos DB w następującym formacie dla każdego rekordu:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Oto dane powiązania w pliku *function.json:*

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

W sekcji [konfiguracji](#configuration) opisano te właściwości.

Oto kod JavaScript:

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano, jak napisać dokument do bazy danych usługi Azure CosmosDB jako dane wyjściowe funkcji.

Definicja powiązania jest zdefiniowana w *pliku function.json,* gdzie *typ* jest ustawiony na `cosmosDB`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "cosmosDB",
      "direction": "out",
      "name": "doc",
      "databaseName": "demodb",
      "collectionName": "data",
      "createIfNotExists": "true",
      "connectionStringSetting": "AzureCosmosDBConnectionString"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Aby zapisać w bazie danych, przekaż `set` obiekt dokumentu do metody parametru bazy danych.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

* [Wyzwalacz kolejki, zapisz wiadomość w bazie danych za pomocą wartości zwracanej](#queue-trigger-save-message-to-database-via-return-value-java)
* [Wyzwalacz HTTP, zapisz jeden dokument w bazie danych za pomocą wartości zwracanej](#http-trigger-save-one-document-to-database-via-return-value-java)
* [Wyzwalacz HTTP, zapisz jeden dokument w bazie danych za pośrednictwem outputbinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [Wyzwalacz HTTP, zapisz wiele dokumentów w bazie danych za pośrednictwem outputbinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>Wyzwalacz kolejki, zapisz wiadomość w bazie danych za pomocą wartości zwracanej

W poniższym przykładzie przedstawiono funkcję Java, która dodaje dokument do bazy danych z danymi z wiadomości w magazynie kolejki.

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database",
  databaseName = "ToDoList",
  collectionName = "Items",
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg",
      queueName = "myqueue-items",
      connection = "AzureWebJobsStorage")
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```
<a id="http-trigger-save-one-document-to-database-via-return-value-java"></a>

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>Wyzwalacz HTTP, zapisz jeden dokument w bazie danych za pomocą wartości zwracanej

W poniższym przykładzie pokazano funkcję Języka ```@CosmosDBOutput``` Java, której podpis ```String```jest adnotowany i ma wartość zwracaną typu . Dokument JSON zwrócony przez funkcję zostanie automatycznie zapisany do odpowiedniej kolekcji usługi CosmosDB.

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database",
      databaseName = "ToDoList",
      collectionName = "Items",
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

<a id="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>Wyzwalacz HTTP, zapisz jeden dokument w bazie danych za pośrednictwem outputbinding

W poniższym przykładzie pokazano funkcję Java, która zapisuje dokument do usługi CosmosDB za pomocą parametru wyjściowego. ```OutputBinding<T>``` W tym przykładzie ```outputItem``` parametr musi być ```@CosmosDBOutput```opisywany za pomocą , a nie podpisu funkcji. Za ```OutputBinding<T>``` pomocą umożliwia funkcji skorzystać z powiązania do zapisu dokumentu do usługi CosmosDB, a jednocześnie umożliwia zwracanie innej wartości do obiektu wywołującego funkcji, takich jak dokument JSON lub XML.

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<String> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

<a id="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>Wyzwalacz HTTP, zapisz wiele dokumentów w bazie danych za pośrednictwem outputbinding

W poniższym przykładzie pokazano funkcję Java, która zapisuje wiele dokumentów do usługi CosmosDB za pomocą parametru wyjściowego. ```OutputBinding<T>``` W tym przykładzie ```outputItem``` parametr jest ```@CosmosDBOutput```opisywany za pomocą , a nie podpisu funkcji. Parametr wyjściowy, ```outputItem``` ma ```ToDoItem``` listę obiektów jako typ parametru szablonu. Za ```OutputBinding<T>``` pomocą umożliwia funkcji skorzystać z powiązania do zapisu dokumentów do usługi CosmosDB, a jednocześnie umożliwia zwracanie innej wartości do obiektu wywołującego funkcji, takich jak dokument JSON lub XML.

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);

          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

W [bibliotece środowiska wykonawczego](/java/api/overview/azure/functions/runtime)funkcji `@CosmosDBOutput` Java użyj adnotacji na temat parametrów, które zostaną zapisane w usłudze Cosmos DB.  Typ parametru adnotacji ```OutputBinding<T>```powinien być , gdzie T jest natywnym typem Java lub POJO.

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C #](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [CosmosDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs)

Konstruktor atrybutu przyjmuje nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i innych właściwościach, które można skonfigurować, zobacz [Dane wyjściowe — konfiguracja](#configuration). Oto przykład `CosmosDB` atrybutu w podpisie metody:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez Pythona.

# <a name="java"></a>[Java](#tab/java)

Adnotacja `CosmosDBOutput` jest dostępna do zapisu danych w usłudze Cosmos DB. Adnotację można zastosować do funkcji lub do indywidualnego parametru funkcji. Gdy jest używana w metodzie funkcji, zwracana wartość funkcji jest to, co jest zapisywane w usłudze Cosmos DB. Jeśli używasz adnotacji z parametrem, typ parametru musi `OutputBinding<T>` `T` być zadeklarowany jako typ natywnego java lub POJO.

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `CosmosDB` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typu**     | Nie dotyczy | Musi być `cosmosDB`ustawiona na .        |
|**Kierunku**     | Nie dotyczy | Musi być `out`ustawiona na .         |
|**Nazwa**     | Nie dotyczy | Nazwa parametru wiązania reprezentującego dokument w funkcji.  |
|**Databasename** | **DatabaseName**|Baza danych zawierająca kolekcję, w której tworzony jest dokument.     |
|**Collectionname** |**CollectionName**  | Nazwa kolekcji, w której tworzony jest dokument. |
|**tworzenieIfNotExists**  |**CreateIfNotExists**    | Wartość logiczna wskazująca, czy kolekcja jest tworzona, gdy nie istnieje. Wartość domyślna jest *false,* ponieważ nowe kolekcje są tworzone z przepływnością zarezerwowaną, co ma wpływ na koszty. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey (klucz)**|**PartitionKey** |Gdy `CreateIfNotExists` true, definiuje ścieżkę klucza partycji dla utworzonej kolekcji.|
|**kolekcjaDuchnia**|**KolekcjaWyjęcie**| Gdy `CreateIfNotExists` true, definiuje [przepływność](../cosmos-db/set-throughput.md) utworzonej kolekcji.|
|**connectionStringSetting**    |**ConnectionStringSetting (Ustawienia połączeń)** |Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi Azure Cosmos DB.        |
|**preferowaneLokacje**| **PreferowaneLocations**| (Opcjonalnie) Definiuje preferowane lokalizacje (regiony) dla kont bazy danych replikowanych geograficznie w usłudze Usługi Azure Cosmos DB. Wartości powinny być oddzielone przecinkami. Na przykład "Wschodnie stany USA, południowo-środkowe stany USA,Europa Północna". |
|**useMultipleWriteLocations**| **UżyjMultipleWriteLocations**| (Opcjonalnie) Po ustawieniu `true` `PreferredLocations`wraz z programem , można wykorzystać [zapisy w wielu regionach](../cosmos-db/how-to-manage-database-account.md#configure-multiple-write-regions) w usłudze Azure Cosmos DB. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Sposób użycia

Domyślnie podczas zapisu do parametru wyjściowego w funkcji, dokument jest tworzony w bazie danych. Ten dokument ma automatycznie generowany identyfikator GUID jako identyfikator dokumentu. Można określić identyfikator dokumentu wyjściowego, określając `id` właściwość w obiekcie JSON przekazanym parametrowi wyjściowemu.

> [!Note]
> Po określeniu identyfikatora istniejącego dokumentu zostanie on zastąpiony przez nowy dokument wyjściowy.

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody zwrotne

| Wiązanie | Tematy pomocy |
|---|---|
| CosmosDB | [Kody błędów usługi CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>ustawienia host.json

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersji 2.x. Aby uzyskać więcej informacji na temat globalnych ustawień konfiguracji w wersji 2.x, zobacz [odwołanie host.json dla usługi Azure Functions w wersji 2.x](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------|
|Tryb bramy|Brama|Tryb połączenia używany przez funkcję podczas łączenia się z usługą Azure Cosmos DB. Dostępne `Direct` są i są dostępne pod`Gateway`|
|Protocol (Protokół)|Https (https)|Protokół połączenia używany przez tę funkcję podczas połączenia z usługą Azure Cosmos DB.  Przeczytaj [tutaj, aby uzyskać wyjaśnienie obu trybów](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|Nie dotyczy|Prefiks dzierżawy do użycia we wszystkich funkcjach w aplikacji.|

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie funkcji podczas tworzenia lub modyfikowanie dokumentu usługi Azure Cosmos DB (wyzwalacz)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Odczyt dokumentu usługi Azure Cosmos DB (powiązanie wejściowe)](./functions-bindings-cosmosdb-v2-input.md)