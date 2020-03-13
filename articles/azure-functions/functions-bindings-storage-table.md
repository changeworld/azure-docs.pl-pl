---
title: Powiązania usługi Azure Table Storage dla Azure Functions
description: Dowiedz się, jak używać powiązań usługi Azure Table Storage w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: edeafb5730f06dac22fd9919ca42ea388d5fd0f6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277182"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Powiązania usługi Azure Table Storage dla Azure Functions

W tym artykule opisano sposób pracy z powiązaniami w usłudze Azure Table Storage w programie Azure Functions. Azure Functions obsługuje powiązania danych wejściowych i wyjściowych dla usługi Azure Table Storage.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania magazynu tabel są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) w wersji 2. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakiety — funkcje 2. x i nowsze

Powiązania magazynu tabel są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) w wersji 3. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Dane wejściowe

Użyj powiązania danych wejściowych usługi Azure Table Storage, aby odczytać tabelę na koncie usługi Azure Storage.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>Jedna jednostka

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która odczytuje pojedynczy wiersz tabeli. Dla każdego rekordu wstawionego w tabeli funkcja zostanie wyzwolona.

Wartość klucza wiersza "{queueTrigger}" wskazuje, że klucz wiersza pochodzi z ciągu komunikatu kolejki.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="iqueryable"></a>IQueryable

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która odczytuje wiele wierszy tabeli, w których Klasa `MyPoco` dziedziczy z `TableEntity`.

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable` nie jest obsługiwana w [środowisku uruchomieniowym funkcji v2](functions-versions.md). Alternatywą jest użycie `CloudTable` parametru metody w celu odczytania tabeli przy użyciu zestawu SDK usługi Azure Storage. Oto przykład funkcji, która bada Azure Functions tabeli dziennika:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

Aby uzyskać więcej informacji na temat korzystania z chmury, zobacz [Rozpoczynanie pracy z usługą Azure Table Storage](../cosmos-db/table-storage-how-to-use-dotnet.md).

Jeśli próbujesz powiązać z `CloudTable` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C#Napisy](#tab/csharp-script)

### <a name="one-entity"></a>Jedna jednostka

Poniższy przykład przedstawia powiązanie danych wejściowych tabeli w pliku *Function. JSON* i [ C# kodzie skryptu](functions-reference-csharp.md) , który używa powiązania. Funkcja używa wyzwalacza kolejki do odczytywania pojedynczego wiersza tabeli. 

Plik *Function. JSON* określa `partitionKey` i `rowKey`. Wartość `rowKey` "{queueTrigger}" wskazuje, że klucz wiersza pochodzi z ciągu komunikatu kolejki.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

W sekcji [Konfiguracja](#input---configuration) objaśniono te właściwości.

Poniżej przedstawiono kod skryptu języka C#:

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="iqueryable"></a>IQueryable

Poniższy przykład przedstawia powiązanie danych wejściowych tabeli w pliku *Function. JSON* i [ C# kodzie skryptu](functions-reference-csharp.md) , który używa powiązania. Funkcja odczytuje jednostki dla klucza partycji, który jest określony w komunikacie kolejki.

Oto plik *Function. JSON* :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

W sekcji [Konfiguracja](#input---configuration) objaśniono te właściwości.

Kod C# skryptu dodaje odwołanie do zestawu SDK usługi Azure Storage, dzięki czemu typ jednostki może pochodzić od `TableEntity`:

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable` nie jest obsługiwana w środowisku uruchomieniowym funkcji dla [wersji 2. x i nowszych)](functions-versions.md). Alternatywą jest użycie `CloudTable` parametru metody w celu odczytania tabeli przy użyciu zestawu SDK usługi Azure Storage. Oto przykład funkcji, która bada Azure Functions tabeli dziennika:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

Aby uzyskać więcej informacji na temat korzystania z chmury, zobacz [Rozpoczynanie pracy z usługą Azure Table Storage](../cosmos-db/table-storage-how-to-use-dotnet.md).

Jeśli próbujesz powiązać z `CloudTable` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](#azure-storage-sdk-version-in-functions-1x).


# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład przedstawia powiązanie danych wejściowych tabeli w pliku *Function. JSON* i [kodzie JavaScript](functions-reference-node.md) , który używa powiązania. Funkcja używa wyzwalacza kolejki do odczytywania pojedynczego wiersza tabeli. 

Plik *Function. JSON* określa `partitionKey` i `rowKey`. Wartość `rowKey` "{queueTrigger}" wskazuje, że klucz wiersza pochodzi z ciągu komunikatu kolejki.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

W sekcji [Konfiguracja](#input---configuration) objaśniono te właściwości.

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Wiersz pojedynczej tabeli 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

# <a name="java"></a>[Java](#tab/java)

Poniższy przykład pokazuje funkcję wyzwalaną przez protokół HTTP, która zwraca listę obiektów osób znajdujących się w określonej partycji w magazynie tabel. W tym przykładzie klucz partycji jest wyodrębniany z trasy http, a TableName i Connection pochodzą z ustawień funkcji. 

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() { return this.PartitionKey; }
    public void setPartitionKey(String key) { this.PartitionKey = key; }
    public String getRowKey() { return this.RowKey; }
    public void setRowKey(String key) { this.RowKey = key; }
    public String getName() { return this.Name; }
    public void setName(String name) { this.Name = name; }
}

@FunctionName("getPersonsByPartitionKey")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}") HttpRequestMessage<Optional<String>> request,
        @BindingName("partitionKey") String partitionKey,
        @TableInput(name="persons", partitionKey="{partitionKey}", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with partition key: " + partitionKey);

    return persons;
}
```

Adnotacja TableInput może również wyodrębnić powiązania z treści JSON żądania, jak pokazano w poniższym przykładzie.

```java
@FunctionName("GetPersonsByKeysFromRequest")
public HttpResponseMessage get(
        @HttpTrigger(name = "getPerson", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="query") HttpRequestMessage<Optional<String>> request,
        @TableInput(name="persons", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") Person person,
        final ExecutionContext context) {

    if (person == null) {
        return request.createResponseBuilder(HttpStatus.NOT_FOUND)
                    .body("Person not found.")
                    .build();
    }

    return request.createResponseBuilder(HttpStatus.OK)
                    .header("Content-Type", "application/json")
                    .body(person)
                    .build();
}
```

Poniższe przykłady używają filtru do wykonywania zapytań dotyczących osób mających określoną nazwę w tabeli platformy Azure i ograniczają liczbę możliwych dopasowań do 10 wyników.

```java
@FunctionName("getPersonsByName")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="filter/{name}") HttpRequestMessage<Optional<String>> request,
        @BindingName("name") String name,
        @TableInput(name="persons", filter="Name eq '{name}'", take = "10", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with name: " + name);

    return persons;
}
```

---

## <a name="input---attributes-and-annotations"></a>Input — atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

 W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj następujących atrybutów, aby skonfigurować powiązanie danych wejściowych tabeli:

* [Tabelaattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Konstruktor atrybutu przyjmuje nazwę tabeli, klucz partycji i klucz wiersza. Ten atrybut może być używany dla parametru `out` lub wartości zwracanej funkcji, jak pokazano w następującym przykładzie:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Możesz ustawić właściwość `Connection`, aby określić konto magazynu, które ma być używane, jak pokazano w następującym przykładzie:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Pełny przykład można znaleźć w temacie Input- C# example.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Zapewnia inny sposób określania konta magazynu, które ma być używane. Konstruktor przyjmuje nazwę ustawienia aplikacji, które zawiera parametry połączenia magazynu. Ten atrybut można zastosować na parametrów, metody lub poziomie klasy. Poniższy przykład pokazuje poziom klasy i metody:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Konto magazynu do użycia jest określane w następującej kolejności:

* Właściwość `Connection` atrybutu `Table`.
* Atrybut `StorageAccount` stosowany do tego samego parametru, który jest atrybutem `Table`.
* Atrybut `StorageAccount` stosowany do funkcji.
* Atrybut `StorageAccount` stosowany do klasy.
* Domyślne konto magazynu dla aplikacji funkcji (ustawienie aplikacji "AzureWebJobsStorage").

# <a name="c-script"></a>[C#Napisy](#tab/csharp-script)

Atrybuty nie są obsługiwane przez C# skrypt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="java"></a>[Java](#tab/java)

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj adnotacji `@TableInput` w przypadku parametrów, których wartość pochodzi z magazynu tabel.  Tej adnotacji można używać w przypadku natywnych typów Java, Pojo lub wartości null przy użyciu `Optional<T>`.

---

## <a name="input---configuration"></a>Dane wejściowe — Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* i `Table` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | Musi być ustawiony na `table`. Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w Azure Portal.|
|**direction** | Nie dotyczy | Musi być ustawiony na `in`. Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w Azure Portal. |
|**Nazwij** | Nie dotyczy | Nazwa zmiennej, która reprezentuje tabelę lub jednostkę w kodzie funkcji. | 
|**tableName** | **TableName** | Nazwa tabeli.| 
|**partitionKey** | **PartitionKey** |Opcjonalny. Klucz partycji jednostki tabeli do odczytania. Zapoznaj się z sekcją [użycie](#input---usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**rowKey** |**RowKey** | Opcjonalny. Klucz wiersza jednostki tabeli, który ma zostać odczytany. Zapoznaj się z sekcją [użycie](#input---usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**czasochłonn** |**Czasochłonn** | Opcjonalny. Maksymalna liczba jednostek do odczytania w języku JavaScript. Zapoznaj się z sekcją [użycie](#input---usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**filtru** |**Filtru** | Opcjonalny. Wyrażenie filtru OData dla danych wejściowych tabeli w języku JavaScript. Zapoznaj się z sekcją [użycie](#input---usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**połączenia** |**Połączenie** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia magazynu, które będą używane dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", w tym miejscu możesz określić tylko resztę nazwy. Na przykład jeśli ustawisz `connection` na "WebStorage", środowisko uruchomieniowe funkcji szuka ustawienia aplikacji o nazwie "Moje magazyn". W przypadku pozostawienia `connection` pustego środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Dane wejściowe — użycie

# <a name="c"></a>[C#](#tab/csharp)

* **Odczytaj jeden wiersz w**

  Ustaw `partitionKey` i `rowKey`. Dostęp do danych tabeli przy użyciu `T <paramName>`parametru metody. W C# skrypcie `paramName` jest wartością określoną we właściwości `name` *funkcji Function. JSON*. `T` jest zazwyczaj typem implementującym `ITableEntity` lub pochodzącym z `TableEntity`. W tym scenariuszu nie są używane właściwości `filter` i `take`.

* **Odczytaj jeden lub więcej wierszy**

  Dostęp do danych tabeli przy użyciu `IQueryable<T> <paramName>`parametru metody. W C# skrypcie `paramName` jest wartością określoną we właściwości `name` *funkcji Function. JSON*. `T` musi być typem implementującym `ITableEntity` lub pochodną z `TableEntity`. Możesz użyć metod `IQueryable`, aby wykonać dowolne filtrowanie. Właściwości `partitionKey`, `rowKey`, `filter`i `take` nie są używane w tym scenariuszu.  

  > [!NOTE]
  > `IQueryable` nie jest obsługiwana w [środowisku uruchomieniowym funkcji v2](functions-versions.md). Alternatywą jest [użycie parametru metody w chmurze paramName](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) , aby odczytać tabelę przy użyciu zestawu SDK usługi Azure Storage. Jeśli próbujesz powiązać z `CloudTable` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C#Napisy](#tab/csharp-script)

* **Odczytaj jeden wiersz w**

  Ustaw `partitionKey` i `rowKey`. Dostęp do danych tabeli przy użyciu `T <paramName>`parametru metody. W C# skrypcie `paramName` jest wartością określoną we właściwości `name` *funkcji Function. JSON*. `T` jest zazwyczaj typem implementującym `ITableEntity` lub pochodzącym z `TableEntity`. W tym scenariuszu nie są używane właściwości `filter` i `take`.

* **Odczytaj jeden lub więcej wierszy**

  Dostęp do danych tabeli przy użyciu `IQueryable<T> <paramName>`parametru metody. W C# skrypcie `paramName` jest wartością określoną we właściwości `name` *funkcji Function. JSON*. `T` musi być typem implementującym `ITableEntity` lub pochodną z `TableEntity`. Możesz użyć metod `IQueryable`, aby wykonać dowolne filtrowanie. Właściwości `partitionKey`, `rowKey`, `filter`i `take` nie są używane w tym scenariuszu.  

  > [!NOTE]
  > `IQueryable` nie jest obsługiwana w [środowisku uruchomieniowym funkcji v2](functions-versions.md). Alternatywą jest [użycie parametru metody w chmurze paramName](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) , aby odczytać tabelę przy użyciu zestawu SDK usługi Azure Storage. Jeśli próbujesz powiązać z `CloudTable` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Ustaw właściwości `filter` i `take`. Nie ustawiaj `partitionKey` ani `rowKey`. Uzyskaj dostęp do jednostki tabeli wejściowej (lub jednostek) za pomocą `context.bindings.<BINDING_NAME>`. Obiekty deserializowane mają właściwości `RowKey` i `PartitionKey`.

# <a name="python"></a>[Python](#tab/python)

Dane tabeli są przesyłane do funkcji jako ciąg JSON. Deserializowanie komunikatu przez wywołanie `json.loads`, jak pokazano w [przykładzie](#input)wejściowym.

# <a name="java"></a>[Java](#tab/java)

Atrybut [TableInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableinput) umożliwia dostęp do wiersza tabeli, który wyzwolił funkcję.

---

## <a name="output"></a>Dane wyjściowe

Za pomocą powiązania danych wyjściowych usługi Azure Table Storage można pisać jednostki w tabeli na koncie usługi Azure Storage.

> [!NOTE]
> To powiązanie danych wyjściowych nie obsługuje aktualizowania istniejących jednostek. Użyj operacji `TableOperation.Replace` [z zestawu SDK usługi Azure Storage,](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) aby zaktualizować istniejącą jednostkę.

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która używa wyzwalacza http do pisania pojedynczego wiersza tabeli. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-script"></a>[C#Napisy](#tab/csharp-script)

Poniższy przykład przedstawia powiązanie danych wyjściowych tabeli w pliku *Function. JSON* i [ C# kodzie skryptu](functions-reference-csharp.md) , który używa powiązania. Funkcja zapisuje wiele jednostek tabeli.

Oto plik *Function. JSON* :

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

W sekcji [Konfiguracja](#output---configuration) objaśniono te właściwości.

Poniżej przedstawiono kod skryptu języka C#:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład przedstawia powiązanie danych wyjściowych tabeli w pliku *Function. JSON* i [funkcji języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja zapisuje wiele jednostek tabeli.

Oto plik *Function. JSON* :

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

W sekcji [Konfiguracja](#output---configuration) objaśniono te właściwości.

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano, jak używać powiązania danych wyjściowych magazynu tabel. Powiązanie `table` jest konfigurowane w pliku *Function. JSON* przez przypisanie wartości do `name`, `tableName`, `partitionKey`i `connection`:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
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
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Następująca funkcja generuje unikatowy UUI wartości `rowKey` i utrzymuje komunikat w magazynie tabel.

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

# <a name="java"></a>[Java](#tab/java)

Poniższy przykład pokazuje funkcję języka Java, która używa wyzwalacza HTTP do pisania pojedynczego wiersza tabeli.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

Poniższy przykład pokazuje funkcję języka Java, która używa wyzwalacza HTTP do pisania wielu wierszy tabeli.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

---

## <a name="output---attributes-and-annotations"></a>Dane wyjściowe — atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj klasy [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Konstruktor atrybutu przyjmuje nazwę tabeli. Ten atrybut może być używany dla parametru `out` lub wartości zwracanej funkcji, jak pokazano w następującym przykładzie:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Możesz ustawić właściwość `Connection`, aby określić konto magazynu, które ma być używane, jak pokazano w następującym przykładzie:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Aby uzyskać pełny przykład, zobacz [Output- C# example](#output).

Można użyć atrybutu `StorageAccount`, aby określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji, zobacz [atrybuty danych wejściowych](#input---attributes-and-annotations).

# <a name="c-script"></a>[C#Napisy](#tab/csharp-script)

Atrybuty nie są obsługiwane przez C# skrypt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="java"></a>[Java](#tab/java)

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj adnotacji [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) w parametrach, aby zapisać wartości w magazynie tabel.

Zobacz [przykład, aby uzyskać więcej szczegółów](#output).

---

## <a name="output---configuration"></a>Dane wyjściowe — Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* i `Table` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | Musi być ustawiony na `table`. Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w Azure Portal.|
|**direction** | Nie dotyczy | Musi być ustawiony na `out`. Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w Azure Portal. |
|**Nazwij** | Nie dotyczy | Nazwa zmiennej używana w kodzie funkcji, która reprezentuje tabelę lub jednostkę. Ustaw `$return`, aby odwoływać się do zwracanej wartości funkcji.| 
|**tableName** |**TableName** | Nazwa tabeli.| 
|**partitionKey** |**PartitionKey** | Klucz partycji jednostki tabeli do zapisania. Zapoznaj się z [sekcją użycie](#output---usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**rowKey** |**RowKey** | Klucz wiersza jednostki tabeli do zapisania. Zapoznaj się z [sekcją użycie](#output---usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**połączenia** |**Połączenie** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia magazynu, które będą używane dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", w tym miejscu możesz określić tylko resztę nazwy. Na przykład jeśli ustawisz `connection` na "WebStorage", środowisko uruchomieniowe funkcji szuka ustawienia aplikacji o nazwie "Moje magazyn". W przypadku pozostawienia `connection` pustego środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

# <a name="c"></a>[C#](#tab/csharp)

Uzyskaj dostęp do jednostki tabeli wyjściowej przy użyciu parametru metody `ICollector<T> paramName` lub `IAsyncCollector<T> paramName` gdzie `T` zawiera właściwości `PartitionKey` i `RowKey`. Te właściwości są często towarzyszące implementowaniem `ITableEntity` lub dziedziczenia `TableEntity`.

Alternatywnie można użyć `CloudTable` parametru metody do zapisu w tabeli przy użyciu zestawu Azure Storage SDK. Jeśli próbujesz powiązać z `CloudTable` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C#Napisy](#tab/csharp-script)

Uzyskaj dostęp do jednostki tabeli wyjściowej przy użyciu parametru metody `ICollector<T> paramName` lub `IAsyncCollector<T> paramName` gdzie `T` zawiera właściwości `PartitionKey` i `RowKey`. Te właściwości są często towarzyszące implementowaniem `ITableEntity` lub dziedziczenia `TableEntity`. Wartość `paramName` jest określona we właściwości `name` *funkcji Function. JSON*.

Alternatywnie można użyć `CloudTable` parametru metody do zapisu w tabeli przy użyciu zestawu Azure Storage SDK. Jeśli próbujesz powiązać z `CloudTable` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Dostęp do zdarzenia wyjściowego przy użyciu `context.bindings.<name>`, gdzie `<name>` jest wartością określoną we właściwości `name` *funkcji Function. JSON*.

# <a name="python"></a>[Python](#tab/python)

Istnieją dwie opcje wyprowadzania komunikatu wiersza magazynu tabel z funkcji:

- **Wartość zwracana**: ustaw właściwość `name` w *funkcji Function. JSON* na `$return`. W przypadku tej konfiguracji wartość zwracana przez funkcję jest utrwalana jako wiersz magazynu tabeli.

- Bezwzględnie **: Przekaż**wartość do metody [Set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) parametru zadeklarowanego jako typ [out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) . Wartość przeniesiona do `set` jest utrwalana jako komunikat centrum zdarzeń.

# <a name="java"></a>[Java](#tab/java)

Istnieją dwie opcje wyprowadzania wiersza magazynu tabel z funkcji przy użyciu adnotacji [TableStorageOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet) :

- **Wartość zwracana**: przez zastosowanie adnotacji do samej funkcji, wartość zwracana funkcji jest utrwalana jako wiersz magazynu tabeli.

- Bezwzględnie **: aby**jawnie ustawić wartość komunikatu, Zastosuj adnotację do określonego parametru typu [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding), gdzie `T` zawiera właściwości `PartitionKey` i `RowKey`. Te właściwości są często towarzyszące implementowaniem `ITableEntity` lub dziedziczenia `TableEntity`.

---

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie | Dokumentacja |
|---|---|
| Tabela | [Kody błędów tabeli](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Obiekt BLOB, tabela, kolejka | [Kody błędów magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Obiekt BLOB, tabela, kolejka | [Rozwiązywanie problemów](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach usługi Azure Functions](functions-triggers-bindings.md)
