---
title: Powiązania magazynu tabel platformy Azure dla funkcji platformy Azure
description: Dowiedz się, jak używać powiązań usługi Azure Table Storage w usłudze Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: 1aa3537679ee37cbc6085344d2f31ae4043d32bb
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520669"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Powiązania magazynu tabel platformy Azure dla funkcji platformy Azure

W tym artykule wyjaśniono, jak pracować z powiązaniami magazynu tabel platformy Azure w usłudze Azure Functions. Usługa Azure Functions obsługuje powiązania danych wejściowych i wyjściowych dla usługi Azure Table Storage.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety - Funkcje 1.x

Powiązania magazynu tabel znajdują się w pakiecie [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet w wersji 2.x. Kod źródłowy pakietu znajduje się w repozytorium [GitHub azure-webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table)

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakiety — funkcje 2.x lub nowsze

Powiązania magazynu tabel znajdują się w pakiecie [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet w wersji 3.x. Kod źródłowy pakietu znajduje się w repozytorium [GitHub azure-webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables)

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Dane wejściowe

Użyj powiązania wejściowego magazynu tabel platformy Azure, aby odczytać tabelę na koncie usługi Azure Storage.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>Jeden podmiot

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która odczytuje pojedynczy wiersz tabeli. Dla każdego rekordu wstawionego w tabeli funkcja zostanie wyzwolona.

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

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która odczytuje wiele wierszy tabeli, `MyPoco` z `TableEntity`których pochodzi klasa .

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

### <a name="cloudtable"></a>CloudTable (Tabela chmur)

`IQueryable`nie jest obsługiwany w [czasie wykonywania Functions v2](functions-versions.md). Alternatywą jest użycie `CloudTable` parametru metody do odczytu tabeli przy użyciu zestawu SDK usługi Azure Storage. Oto przykład funkcji, która wysyła zapytanie do tabeli dziennika usług Azure Functions:

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

Aby uzyskać więcej informacji na temat korzystania z cloudtable, zobacz [Wprowadzenie do usługi Azure Table Storage](../cosmos-db/table-storage-how-to-use-dotnet.md).

Jeśli spróbujesz `CloudTable` powiązać i pojawi się komunikat o błędzie, upewnij się, że masz odwołanie do [poprawnej wersji SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

### <a name="one-entity"></a>Jeden podmiot

W poniższym przykładzie przedstawiono powiązanie wejściowe tabeli w pliku *function.json* i kod [skryptu C#,](functions-reference-csharp.md) który używa powiązania. Funkcja używa wyzwalacza kolejki do odczytu pojedynczego wiersza tabeli. 

Plik *function.json* określa `partitionKey` a `rowKey`i . Wartość `rowKey` "{queueTrigger}" wskazuje, że klucz wiersza pochodzi z ciągu komunikatu kolejki.

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

W sekcji [konfiguracji](#input---configuration) opisano te właściwości.

Oto kod skryptu języka C#:

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

W poniższym przykładzie przedstawiono powiązanie wejściowe tabeli w pliku *function.json* i kod [skryptu C#,](functions-reference-csharp.md) który używa powiązania. Funkcja odczytuje jednostki dla klucza partycji, który jest określony w komunikacie kolejki.

Oto plik *function.json:*

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

W sekcji [konfiguracji](#input---configuration) opisano te właściwości.

Kod skryptu języka C# dodaje odwołanie do sdk usługi Azure `TableEntity`Storage, dzięki czemu typ jednostki może pochodzić z:

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

### <a name="cloudtable"></a>CloudTable (Tabela chmur)

`IQueryable`nie jest obsługiwany w czasie wykonywania funkcji dla [wersji 2.x i nowszej)](functions-versions.md). Alternatywą jest użycie `CloudTable` parametru metody do odczytu tabeli przy użyciu zestawu SDK usługi Azure Storage. Oto przykład funkcji, która wysyła zapytanie do tabeli dziennika usług Azure Functions:

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

Aby uzyskać więcej informacji na temat korzystania z cloudtable, zobacz [Wprowadzenie do usługi Azure Table Storage](../cosmos-db/table-storage-how-to-use-dotnet.md).

Jeśli spróbujesz `CloudTable` powiązać i pojawi się komunikat o błędzie, upewnij się, że masz odwołanie do [poprawnej wersji SDK magazynu](#azure-storage-sdk-version-in-functions-1x).


# <a name="javascript"></a>[JavaScript](#tab/javascript)

W poniższym przykładzie przedstawiono powiązanie wejściowe tabeli w pliku *function.json* i [kod JavaScript,](functions-reference-node.md) który używa powiązania. Funkcja używa wyzwalacza kolejki do odczytu pojedynczego wiersza tabeli. 

Plik *function.json* określa `partitionKey` a `rowKey`i . Wartość `rowKey` "{queueTrigger}" wskazuje, że klucz wiersza pochodzi z ciągu komunikatu kolejki.

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

W sekcji [konfiguracji](#input---configuration) opisano te właściwości.

Oto kod JavaScript:

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

Poniższy przykład przedstawia funkcję wyzwalaną HTTP, która zwraca listę obiektów osób, które znajdują się w określonej partycji w magazynie tabel. W tym przykładzie klucz partycji jest wyodrębniany z trasy http, a tableName i połączenie pochodzą z ustawień funkcji. 

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

TableInput adnotacji można również wyodrębnić powiązania z treści json żądania, jak pokazano w poniższym przykładzie.

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

Poniższe przykłady używa filtru do kwerendy dla osób o określonej nazwie w tabeli platformy Azure i ogranicza liczbę możliwych dopasowań do 10 wyników.

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

## <a name="input---attributes-and-annotations"></a>Dane wejściowe - atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

 W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj następujących atrybutów, aby skonfigurować powiązanie wejściowe tabeli:

* [Tableattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Konstruktor atrybutu przyjmuje nazwę tabeli, klucz partycji i klucz wiersza. Atrybut może być używany `out` na parametr lub na wartość zwracaną funkcji, jak pokazano w poniższym przykładzie:

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

  Można ustawić `Connection` właściwość, aby określić konto magazynu do użycia, jak pokazano w poniższym przykładzie:

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

  Aby uzyskać pełny przykład, zobacz dane wejściowe — przykład C#.

* [Atrybut StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Umożliwia inny sposób określania konta magazynu, którego chcesz użyć. Konstruktor przyjmuje nazwę ustawienia aplikacji, który zawiera ciąg połączenia magazynu. Atrybut można zastosować na poziomie parametru, metody lub klasy. Poniższy przykład przedstawia poziom klasy i poziom metody:

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

* Właściwość `Table` atrybutu. `Connection`
* Atrybut `StorageAccount` zastosowany do tego samego `Table` parametru co atrybut.
* Atrybut `StorageAccount` zastosowany do funkcji.
* Atrybut `StorageAccount` stosowany do klasy.
* Domyślne konto magazynu dla aplikacji funkcji (ustawienie aplikacji AzureWebJobsStorage").

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez Pythona.

# <a name="java"></a>[Java](#tab/java)

W [bibliotece środowiska wykonawczego](/java/api/overview/azure/functions/runtime)funkcji `@TableInput` Java użyj adnotacji na temat parametrów, których wartość pochodziłaby z magazynu tabel.  Ta adnotacja może być używana z natywnymi typami `Optional<T>`Java, POJO lub wartościami nullable przy użyciu .

---

## <a name="input---configuration"></a>Wejście - konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `Table` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typu** | Nie dotyczy | Musi być `table`ustawiona na . Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w witrynie Azure portal.|
|**Kierunku** | Nie dotyczy | Musi być `in`ustawiona na . Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w witrynie Azure portal. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej reprezentującej tabelę lub jednostkę w kodzie funkcji. | 
|**tableName** | **Tablename** | Nazwa tabeli.| 
|**partitionKey (klucz)** | **PartitionKey** |Element opcjonalny. Klucz partycji jednostki tabeli do odczytu. Zobacz sekcję [użycia,](#input---usage) aby uzyskać wskazówki dotyczące używania tej właściwości.| 
|**klawisz rowKey** |**RowKey** | Element opcjonalny. Klucz wiersza jednostki tabeli do odczytu. Zobacz sekcję [użycia,](#input---usage) aby uzyskać wskazówki dotyczące używania tej właściwości.| 
|**wziąć** |**Weź** | Element opcjonalny. Maksymalna liczba jednostek do odczytu w języku JavaScript. Zobacz sekcję [użycia,](#input---usage) aby uzyskać wskazówki dotyczące używania tej właściwości.| 
|**Filtr** |**Filtr** | Element opcjonalny. Wyrażenie filtru OData dla wprowadzania danych w tabeli w języku JavaScript. Zobacz sekcję [użycia,](#input---usage) aby uzyskać wskazówki dotyczące używania tej właściwości.| 
|**Połączenia** |**Połączenia** | Nazwa ustawienia aplikacji, która zawiera ciąg połączenia magazynu do użycia dla tego powiązania. Ustawienie może być nazwą "AzureWebJobs" ustawienie aplikacji lub nazwę ciągu połączenia. Na przykład jeśli nazwa ustawienia jest "AzureWebJobsMyStorage", można określić "MyStorage" tutaj. Środowisko wykonawcze functions automatycznie wyszukuje ustawienie aplikacji o nazwie "AzureWebJobsMyStorage". Jeśli pozostawisz `connection` puste, środowisko wykonawcze Functions używa domyślnego ciągu połączenia `AzureWebJobsStorage`Magazyn w ustawieniu aplikacji o nazwie .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Wejście - użycie

# <a name="c"></a>[C#](#tab/csharp)

* **Odczyt jednego wiersza w**

  Zestaw `partitionKey` `rowKey`i . Dostęp do danych tabeli `T <paramName>`przy użyciu parametru metody . W skrypcie C# `paramName` jest `name` wartością określoną we właściwości *function.json*. `T`jest typem, który `ITableEntity` implementuje lub `TableEntity`wywodzi się z . Właściwości `filter` `take` i nie są używane w tym scenariuszu.

* **Odczyt jednego lub więcej wierszy**

  Dostęp do danych tabeli `IQueryable<T> <paramName>`przy użyciu parametru metody . W skrypcie C# `paramName` jest `name` wartością określoną we właściwości *function.json*. `T`musi być typem, `ITableEntity` który implementuje lub wywodzi się z `TableEntity`. Można użyć `IQueryable` metod, aby wykonać wszelkie filtrowania wymagane. W `partitionKey` `rowKey`tym `filter`scenariuszu nie są używane właściwości i `take` właściwości.  

  > [!NOTE]
  > `IQueryable`nie jest obsługiwany w [czasie wykonywania Functions v2](functions-versions.md). Alternatywą jest [użycie parametru metody paramName cloudtable](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) do odczytu tabeli przy użyciu sdk usługi Azure Storage. Jeśli spróbujesz `CloudTable` powiązać i pojawi się komunikat o błędzie, upewnij się, że masz odwołanie do [poprawnej wersji SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

* **Odczyt jednego wiersza w**

  Zestaw `partitionKey` `rowKey`i . Dostęp do danych tabeli `T <paramName>`przy użyciu parametru metody . W skrypcie C# `paramName` jest `name` wartością określoną we właściwości *function.json*. `T`jest typem, który `ITableEntity` implementuje lub `TableEntity`wywodzi się z . Właściwości `filter` `take` i nie są używane w tym scenariuszu.

* **Odczyt jednego lub więcej wierszy**

  Dostęp do danych tabeli `IQueryable<T> <paramName>`przy użyciu parametru metody . W skrypcie C# `paramName` jest `name` wartością określoną we właściwości *function.json*. `T`musi być typem, `ITableEntity` który implementuje lub wywodzi się z `TableEntity`. Można użyć `IQueryable` metod, aby wykonać wszelkie filtrowania wymagane. W `partitionKey` `rowKey`tym `filter`scenariuszu nie są używane właściwości i `take` właściwości.  

  > [!NOTE]
  > `IQueryable`nie jest obsługiwany w [czasie wykonywania Functions v2](functions-versions.md). Alternatywą jest [użycie parametru metody paramName cloudtable](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) do odczytu tabeli przy użyciu sdk usługi Azure Storage. Jeśli spróbujesz `CloudTable` powiązać i pojawi się komunikat o błędzie, upewnij się, że masz odwołanie do [poprawnej wersji SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Ustaw `filter` właściwości `take` i. Nie ustawiaj `partitionKey` `rowKey`ani nie ustawiaj . Dostęp do jednostki tabeli wejściowej (lub encji) za pomocą programu `context.bindings.<BINDING_NAME>`. Deserializowane obiekty `RowKey` `PartitionKey` mają i właściwości.

# <a name="python"></a>[Python](#tab/python)

Dane tabeli są przekazywane do funkcji jako ciąg JSON. De-serialize wiadomości przez `json.loads` wywołanie, jak pokazano w [przykładzie](#input)wprowadzania .

# <a name="java"></a>[Java](#tab/java)

[TableInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableinput) Atrybut daje dostęp do wiersza tabeli, który wyzwolił funkcję.

---

## <a name="output"></a>Dane wyjściowe

Użyj powiązania danych wyjściowych magazynu tabel platformy Azure, aby zapisać jednostki do tabeli na koncie usługi Azure Storage.

> [!NOTE]
> To powiązanie danych wyjściowych nie obsługuje aktualizowania istniejących jednostek. Użyj `TableOperation.Replace` operacji [z pakietu Azure Storage SDK,](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) aby zaktualizować istniejącą jednostkę.

# <a name="c"></a>[C#](#tab/csharp)

W poniższym przykładzie przedstawiono [funkcję Języka C#,](functions-dotnet-class-library.md) która używa wyzwalacza HTTP do pisania pojedynczego wiersza tabeli. 

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

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

W poniższym przykładzie przedstawiono powiązanie danych wyjściowych tabeli w pliku *function.json* i kod [skryptu C#,](functions-reference-csharp.md) który używa powiązania. Funkcja zapisuje wiele elementów tabeli.

Oto plik *function.json:*

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

W sekcji [konfiguracji](#output---configuration) opisano te właściwości.

Oto kod skryptu języka C#:

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

W poniższym przykładzie przedstawiono powiązanie danych wyjściowych tabeli w pliku *function.json* i [funkcję JavaScript,](functions-reference-node.md) która używa powiązania. Funkcja zapisuje wiele elementów tabeli.

Oto plik *function.json:*

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

W sekcji [konfiguracji](#output---configuration) opisano te właściwości.

Oto kod JavaScript:

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

W poniższym przykładzie pokazano, jak używać powiązania danych wyjściowych magazynu tabeli. Powiązanie `table` jest konfigurowane w *pliku function.json* przez przypisanie wartości do `name`, `tableName`, `partitionKey`i `connection`:

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

Poniższa funkcja generuje unikatowy interfejs `rowKey` użytkownika dla wartości i utrzymuje komunikat do magazynu tabel.

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

W poniższym przykładzie przedstawiono funkcję Java, która używa wyzwalacza HTTP do pisania pojedynczego wiersza tabeli.

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

W poniższym przykładzie przedstawiono funkcję Java, która używa wyzwalacza HTTP do pisania wielu wierszy tabeli.

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

## <a name="output---attributes-and-annotations"></a>Dane wyjściowe - atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj [atrybutu TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Konstruktor atrybutu przyjmuje nazwę tabeli. Atrybut może być używany `out` na parametr lub na wartość zwracaną funkcji, jak pokazano w poniższym przykładzie:

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

Można ustawić `Connection` właściwość, aby określić konto magazynu do użycia, jak pokazano w poniższym przykładzie:

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

Aby uzyskać pełny przykład, zobacz [Output — Przykład C#.](#output)

Za pomocą `StorageAccount` atrybutu można określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji, zobacz [Input - attributes](#input---attributes-and-annotations).

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez Pythona.

# <a name="java"></a>[Java](#tab/java)

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)użyj adnotacji [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) na parametrach, aby zapisać wartości w magazynie tabel.

Zobacz [przykład,](#output)aby uzyskać więcej szczegółów .

---

## <a name="output---configuration"></a>Wyjście - konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `Table` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typu** | Nie dotyczy | Musi być `table`ustawiona na . Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w witrynie Azure portal.|
|**Kierunku** | Nie dotyczy | Musi być `out`ustawiona na . Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w witrynie Azure portal. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej używana w kodzie funkcji reprezentująca tabelę lub encję. Ustaw, `$return` aby odwoływać się do wartości zwracanej funkcji.| 
|**tableName** |**Tablename** | Nazwa tabeli.| 
|**partitionKey (klucz)** |**PartitionKey** | Klucz partycji jednostki tabeli do zapisu. Zobacz [sekcję użycia,](#output---usage) aby uzyskać wskazówki dotyczące używania tej właściwości.| 
|**klawisz rowKey** |**RowKey** | Klucz wiersza jednostki tabeli do zapisania. Zobacz [sekcję użycia,](#output---usage) aby uzyskać wskazówki dotyczące używania tej właściwości.| 
|**Połączenia** |**Połączenia** | Nazwa ustawienia aplikacji, która zawiera ciąg połączenia magazynu do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", można określić tylko pozostałą część nazwy tutaj. Na przykład jeśli `connection` ustawisz "MyStorage", środowisko wykonawcze Functions szuka ustawienia aplikacji o nazwie "MyStorage". Jeśli pozostawisz `connection` puste, środowisko wykonawcze Functions używa domyślnego ciągu połączenia `AzureWebJobsStorage`Magazyn w ustawieniu aplikacji o nazwie .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Wyjście - użycie

# <a name="c"></a>[C#](#tab/csharp)

Dostęp do jednostki tabeli `ICollector<T> paramName` wyjściowej przy użyciu parametru `IAsyncCollector<T> paramName` metody lub `T` gdzie zawiera `PartitionKey` i `RowKey` właściwości. Właściwościom tym często towarzyszy wdrożenie `ITableEntity` lub `TableEntity`dziedziczenie .

Alternatywnie można użyć `CloudTable` parametru metody do zapisu w tabeli przy użyciu zestawu SDK usługi Azure Storage. Jeśli spróbujesz `CloudTable` powiązać i pojawi się komunikat o błędzie, upewnij się, że masz odwołanie do [poprawnej wersji SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Dostęp do jednostki tabeli `ICollector<T> paramName` wyjściowej przy użyciu parametru `IAsyncCollector<T> paramName` metody lub `T` gdzie zawiera `PartitionKey` i `RowKey` właściwości. Właściwościom tym często towarzyszy wdrożenie `ITableEntity` lub `TableEntity`dziedziczenie . Wartość `paramName` jest określona `name` we właściwości *function.json*.

Alternatywnie można użyć `CloudTable` parametru metody do zapisu w tabeli przy użyciu zestawu SDK usługi Azure Storage. Jeśli spróbujesz `CloudTable` powiązać i pojawi się komunikat o błędzie, upewnij się, że masz odwołanie do [poprawnej wersji SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Dostęp do zdarzenia `context.bindings.<name>` wyjściowego przy `<name>` użyciu, `name` gdzie jest wartość określona we właściwości *function.json*.

# <a name="python"></a>[Python](#tab/python)

Istnieją dwie opcje wyprowadzania komunikatu wiersza magazynu tabeli z funkcji:

- **Zwracana wartość:** Ustaw `name` właściwość w `$return` *function.json* na . W tej konfiguracji wartość zwracana funkcji jest zachowywana jako wiersz magazynu tabeli.

- **Imperatyw:** Przekaż wartość do [metody zestawu](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) parametru zadeklarowanego jako typ [Out.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) Wartość przekazana `set` do jest zachowywana jako komunikat Centrum zdarzeń.

# <a name="java"></a>[Java](#tab/java)

Istnieją dwie opcje wyprowadzania wiersza magazynu tabeli z funkcji przy użyciu adnotacji [TableStorageOutput:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet)

- **Zwracana wartość:** Stosując adnotację do samej funkcji, zwracana wartość funkcji jest zachowywana jako wiersz magazynu tabeli.

- **Imperatyw:** Aby jawnie ustawić wartość wiadomości, zastosuj adnotację `T` do `PartitionKey` określonego parametru typu, `RowKey` [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)gdzie zawiera i właściwości. Właściwościom tym często towarzyszy wdrożenie `ITableEntity` lub `TableEntity`dziedziczenie .

---

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody zwrotne

| Wiązanie | Tematy pomocy |
|---|---|
| Tabela | [Kody błędów tabeli](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Obiekt Blob, Tabela, Kolejka | [Kody błędów magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Obiekt Blob, Tabela, Kolejka | [Rozwiązywanie problemów](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach funkcji platformy Azure](functions-triggers-bindings.md)
