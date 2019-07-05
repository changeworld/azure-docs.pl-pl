---
title: Powiązania magazynu dla usługi Azure Functions dla tabeli platformy Azure
description: Dowiedz się, jak używać usługi Azure Table storage powiązań w usłudze Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: usługi Azure functions, funkcje, przetwarzanie zdarzeń, obliczanie dynamiczne, architektura bez serwera
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: 002ea00364056f0780146b79936a6bc04e289973
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480077"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Powiązania magazynu dla usługi Azure Functions dla tabeli platformy Azure

W tym artykule wyjaśniono, jak pracować z usługą Azure Table storage powiązań w usłudze Azure Functions. Usługi Azure Functions obsługuje danych wejściowych i wyjściowych powiązań usługi Azure Table Storage.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania magazynu tabeli znajdują się w [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) pakietu NuGet w wersji 2.x. Kod źródłowy dla pakietu znajduje się w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) repozytorium GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania magazynu tabeli znajdują się w [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) pakietu NuGet w wersji 3.x. Kod źródłowy dla pakietu znajduje się w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) repozytorium GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Dane wejściowe

Użyj powiązania danych wejściowych magazynu tabel Azure, aby odczytać tabeli na koncie usługi Azure Storage.

## <a name="input---example"></a>Dane wejściowe — przykład

Zobacz przykład specyficzny dla języka:

* [Jeden podmiot odczyt C#](#input---c-example---one-entity)
* [Element IQueryable powiązania C#](#input---c-example---iqueryable)
* [CloudTable powiązania C#](#input---c-example---cloudtable)
* [Jeden podmiot odczyt skrypt języka C#](#input---c-script-example---one-entity)
* [Powiązania skryptu języka C# do elementu IQueryable](#input---c-script-example---iqueryable)
* [C# script powiązania CloudTable](#input---c-script-example---cloudtable)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)
* [Java](#input---java-example)

### <a name="input---c-example---one-entity"></a>Dane wejściowe — przykład C# — jedną jednostkę.

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , odczytuje wiersz pojedynczej tabeli. 

Wartość klucza w wierszu "{queueTrigger}" wskazuje, że klucz wiersza pochodzi z ciągu kolejki wiadomości.

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

### <a name="input---c-example---iqueryable"></a>IQueryable — przykład C# — dane wejściowe

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , odczytuje wielu wierszy tabeli. Należy pamiętać, że `MyPoco` klasa pochodzi od `TableEntity`.

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

### <a name="input---c-example---cloudtable"></a>Dane wejściowe — przykład C# — CloudTable

`IQueryable` nie jest obsługiwane w [środowisko uruchomieniowe usługi Functions w wersji 2](functions-versions.md). Alternatywą jest użycie `CloudTable` parametru metody, aby odczytać tabeli przy użyciu zestawu SDK usługi Azure Storage. Oto przykład funkcji 2.x, który odpytuje tabelę dziennika usługi Azure Functions:

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

Aby uzyskać więcej informacji o sposobie używania CloudTable, zobacz [Rozpoczynanie pracy z usługą Azure Table storage](../cosmos-db/table-storage-how-to-use-dotnet.md).

Jeśli próbujesz powiązać `CloudTable` i komunikat o błędzie, upewnij się, że odwołanie do [poprawnej wersji zestawu SDK usługi Storage](#azure-storage-sdk-version-in-functions-1x).

### <a name="input---c-script-example---one-entity"></a>Dane wejściowe — C# przykład skryptu — jeden podmiot

W poniższym przykładzie pokazano tabelę powiązania danych wejściowych w *function.json* pliku i [skrypt języka C#](functions-reference-csharp.md) kodu korzystającego z powiązania. Funkcja używa wyzwalacz kolejki można odczytać wiersza pojedynczej tabeli. 

*Function.json* plik Określa `partitionKey` i `rowKey`. `rowKey` Wartość "{queueTrigger}" wskazuje, że klucz wiersza pochodzi z ciągu kolejki wiadomości.

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

[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

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

### <a name="input---c-script-example---iqueryable"></a>IQueryable — C# przykładowy skrypt — dane wejściowe

W poniższym przykładzie pokazano tabelę powiązania danych wejściowych w *function.json* pliku i [skrypt języka C#](functions-reference-csharp.md) kodu korzystającego z powiązania. Funkcja odczytuje jednostki dla klucza partycji, który jest określony w komunikacie kolejki.

Oto *function.json* pliku:

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

[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Kod skryptu języka C# dodaje odwołanie do zestawu SDK usługi Azure Storage, tak aby typ jednostki może pochodzić z `TableEntity`:

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

### <a name="input---c-script-example---cloudtable"></a>Dane wejściowe — C# przykładowy skrypt - CloudTable

`IQueryable` nie jest obsługiwane w [środowisko uruchomieniowe usługi Functions w wersji 2](functions-versions.md). Alternatywą jest użycie `CloudTable` parametru metody, aby odczytać tabeli przy użyciu zestawu SDK usługi Azure Storage. Oto przykład funkcji 2.x, który odpytuje tabelę dziennika usługi Azure Functions:

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

Aby uzyskać więcej informacji o sposobie używania CloudTable, zobacz [Rozpoczynanie pracy z usługą Azure Table storage](../cosmos-db/table-storage-how-to-use-dotnet.md).

Jeśli próbujesz powiązać `CloudTable` i komunikat o błędzie, upewnij się, że odwołanie do [poprawnej wersji zestawu SDK usługi Storage](#azure-storage-sdk-version-in-functions-1x).

### <a name="input---f-example"></a>Dane wejściowe - F# przykład

W poniższym przykładzie pokazano tabelę powiązania danych wejściowych w *function.json* pliku i [ F# skryptu](functions-reference-fsharp.md) kodu korzystającego z powiązania. Funkcja używa wyzwalacz kolejki można odczytać wiersza pojedynczej tabeli. 

*Function.json* plik Określa `partitionKey` i `rowKey`. `rowKey` Wartość "{queueTrigger}" wskazuje, że klucz wiersza pochodzi z ciągu kolejki wiadomości.

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

[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Oto F# kodu:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.LogInformation(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.LogInformation(sprintf "Name in Person entity: %s" personEntity.Name)
```

### <a name="input---javascript-example"></a>Dane wejściowe — przykład JavaScript

W poniższym przykładzie pokazano tabelę powiązania danych wejściowych w *function.json* pliku i [kod JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja używa wyzwalacz kolejki można odczytać wiersza pojedynczej tabeli. 

*Function.json* plik Określa `partitionKey` i `rowKey`. `rowKey` Wartość "{queueTrigger}" wskazuje, że klucz wiersza pochodzi z ciągu kolejki wiadomości.

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

[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

### <a name="input---java-example"></a>Dane wejściowe - przykładzie w języku Java

Poniższy przykład przedstawia funkcję wyzwalaną przez protokół HTTP, która zwraca łączną liczbę elementów w określonej partycji w usłudze Table storage.

```java
@FunctionName("getallcount")
public int run(
   @HttpTrigger(name = "req",
                 methods = {HttpMethod.GET},
                 authLevel = AuthorizationLevel.ANONYMOUS) Object dummyShouldNotBeUsed,
   @TableInput(name = "items",
                tableName = "mytablename",  partitionKey = "myparkey",
                connection = "myconnvarname") MyItem[] items
) {
    return items.length;
}
```


## <a name="input---attributes"></a>Dane wejściowe — atrybuty
 
W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyć następujących atrybutów, aby skonfigurować powiązania danych wejściowych w tabeli:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Konstruktor atrybutu ma nazwę tabeli klucza partycji i klucza wiersza. Może służyć w parametrze wyjściowym lub wartość zwracaną funkcji, jak pokazano w poniższym przykładzie:

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

  Możesz ustawić `Connection` właściwości w celu określenia konta magazynu do użycia, jak pokazano w poniższym przykładzie:

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

  Pełny przykład można wyświetlić w danych wejściowych - C# przykład.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Udostępnia inny sposób, aby określić konto magazynu do użycia. Konstruktor przyjmuje nazwę ustawienia aplikacji zawierającego parametry połączenia magazynu. Ten atrybut można zastosować na parametrów, metody lub poziomie klasy. Poniższy przykład pokazuje poziom klasy i metody:

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

Konto magazynu do użycia, jest określana w następującej kolejności:

* `Table` Atrybutu `Connection` właściwości.
* `StorageAccount` Zastosowany do tego samego parametru jako `Table` atrybutu.
* `StorageAccount` Zastosowany do funkcji.
* `StorageAccount` Zastosowany do klasy.
* Domyślne konto magazynu dla aplikacji funkcji (ustawienie aplikacji "AzureWebJobsStorage").

## <a name="input---java-annotations"></a>Dane wejściowe — Java adnotacji

W [Java funkcje biblioteki środowiska uruchomieniowego](/java/api/overview/azure/functions/runtime), użyj `@TableInput` adnotacji w parametrach, którego wartość może pochodzić z usługi Table storage.  Ta adnotacja mogą być używane z natywnych typów języka Java, obiektów typu Pojo lub wartości dopuszczających wartości null przy użyciu opcjonalnie<T>. 

## <a name="input---configuration"></a>Dane wejściowe — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `Table` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | Musi być równa `table`. Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w witrynie Azure portal.|
|**direction** | Nie dotyczy | Musi być równa `in`. Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w witrynie Azure portal. |
|**name** | Nie dotyczy | Nazwa zmiennej, która reprezentuje tabelą lub obiektem, w kodzie funkcji. | 
|**tableName** | **TableName** | Nazwa tabeli.| 
|**partitionKey** | **PartitionKey** |Opcjonalny. Klucz partycji jednostkę tabeli do odczytu. Zobacz [użycia](#input---usage) sekcji, aby uzyskać wskazówki dotyczące sposobu używania tej właściwości.| 
|**rowKey** |**RowKey** | Opcjonalny. Klucz wiersza jednostki tabeli do odczytu. Zobacz [użycia](#input---usage) sekcji, aby uzyskać wskazówki dotyczące sposobu używania tej właściwości.| 
|**Wypełnij** |**Take** | Opcjonalny. Maksymalna liczba jednostek do odczytu w języku JavaScript. Zobacz [użycia](#input---usage) sekcji, aby uzyskać wskazówki dotyczące sposobu używania tej właściwości.| 
|**Filtr** |**Filtr** | Opcjonalny. Wyrażenie filtru OData dla tabeli danych wejściowych w języku JavaScript. Zobacz [użycia](#input---usage) sekcji, aby uzyskać wskazówki dotyczące sposobu używania tej właściwości.| 
|**połączenia** |**połączenia** | Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji rozpoczyna się od "AzureWebJobs", można określić tylko pozostałą część nazwy w tym miejscu. Na przykład jeśli ustawisz `connection` do "Mój_magazyn", środowisko uruchomieniowe usługi Functions wyszukuje ustawienie aplikacji o nazwie "AzureWebJobsMyStorage." Jeśli pozostawisz `connection` pusta, środowisko uruchomieniowe usługi Functions korzysta z domyślne parametry połączenia magazynu w ustawieniach aplikacji, który nosi nazwę `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Dane wejściowe — użycie

Powiązania danych wejściowych magazyn tabel obsługuje następujące scenariusze:

* **Przeczytaj jeden wiersz w języku C# lub skrypt języka C#**

  Ustaw `partitionKey` i `rowKey`. Dostęp do danych tabel przy użyciu parametru metody `T <paramName>`. W języku C# `paramName` jest wartością określoną w `name` właściwość *function.json*. `T` Zazwyczaj jest to typ, który implementuje `ITableEntity` lub pochodzi od klasy `TableEntity`. `filter` i `take` właściwości nie są używane w tym scenariuszu. 

* **Przeczytaj, co najmniej jeden wiersz w języku C# lub skrypt języka C#**

  Dostęp do danych tabel przy użyciu parametru metody `IQueryable<T> <paramName>`. W języku C# `paramName` jest wartością określoną w `name` właściwość *function.json*. `T` musi być typu, który implementuje `ITableEntity` lub pochodzi od klasy `TableEntity`. Możesz użyć `IQueryable` metody żadnego filtrowania wymagane. `partitionKey`, `rowKey`, `filter`, I `take` właściwości nie są używane w tym scenariuszu.  

  > [!NOTE]
  > `IQueryable` nie jest obsługiwane w [środowisko uruchomieniowe usługi Functions w wersji 2](functions-versions.md). Alternatywą jest [Użyj parametru metody paramName CloudTable](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) odczytać tabeli przy użyciu zestawu SDK usługi Azure Storage. Jeśli próbujesz powiązać `CloudTable` i komunikat o błędzie, upewnij się, że odwołanie do [poprawnej wersji zestawu SDK usługi Storage](#azure-storage-sdk-version-in-functions-1x).

* **Przeczytaj, co najmniej jeden wiersz w języku JavaScript**

  Ustaw `filter` i `take` właściwości. Nie należy ustawiać `partitionKey` lub `rowKey`. Uzyskaj dostęp do danych wejściowych tabeli jednostki (lub jednostek) przy użyciu `context.bindings.<name>`. Po deserializacji obiekty mają `RowKey` i `PartitionKey` właściwości.

## <a name="output"></a>Output

Użyj usługi Azure Table storage powiązania danych wyjściowych usługi można zapisać jednostki do tabeli na koncie usługi Azure Storage.

> [!NOTE]
> To powiązanie danych wyjściowych nie obsługuje ich aktualizowania istniejących jednostek. Użyj `TableOperation.Replace` operacji [z zestawu SDK usługi Azure Storage](https://docs.microsoft.com/azure/cosmos-db/table-storage-how-to-use-dotnet#replace-an-entity) do zaktualizowania istniejącej jednostki.   

## <a name="output---example"></a>Dane wyjściowe — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#output---c-example)
* [Skryptu C# (csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Dane wyjściowe — przykład w języku C#

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) używającej wyzwalacz HTTP do zapisania pojedynczej tabeli Wiersz. 

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

### <a name="output---c-script-example"></a>Dane wyjściowe — przykładowy skrypt w języku C#

Poniższy przykład przedstawia dane wyjściowe tabeli powiązanie w *function.json* pliku i [skrypt języka C#](functions-reference-csharp.md) kodu korzystającego z powiązania. Funkcja zapisuje wiele jednostek tabeli.

Oto *function.json* pliku:

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

[Konfiguracji](#output---configuration) sekcji opisano te właściwości.

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

### <a name="output---f-example"></a>Dane wyjściowe — F# przykład

Poniższy przykład przedstawia dane wyjściowe tabeli powiązanie w *function.json* pliku i [ F# skryptu](functions-reference-fsharp.md) kodu korzystającego z powiązania. Funkcja zapisuje wiele jednostek tabeli.

Oto *function.json* pliku:

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

[Konfiguracji](#output---configuration) sekcji opisano te właściwości.

Oto F# kodu:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: ILogger) =
    for i = 1 to 10 do
        log.LogInformation(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

### <a name="output---javascript-example"></a>Dane wyjściowe — przykład JavaScript

Poniższy przykład przedstawia dane wyjściowe tabeli powiązanie w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja zapisuje wiele jednostek tabeli.

Oto *function.json* pliku:

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

[Konfiguracji](#output---configuration) sekcji opisano te właściwości.

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

## <a name="output---attributes"></a>Dane wyjściowe — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Konstruktor atrybutu ma nazwę tabeli. Może służyć w `out` parametru lub zwracanej wartości funkcji, jak pokazano w poniższym przykładzie:

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

Możesz ustawić `Connection` właściwości w celu określenia konta magazynu do użycia, jak pokazano w poniższym przykładzie:

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

Aby uzyskać kompletny przykład, zobacz [dane wyjściowe — przykład w języku C#](#output---c-example).

Możesz użyć `StorageAccount` atrybutu, aby określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji, zobacz [wprowadzenia - atrybuty](#input---attributes).

## <a name="output---configuration"></a>Dane wyjściowe — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `Table` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | Musi być równa `table`. Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w witrynie Azure portal.|
|**direction** | Nie dotyczy | Musi być równa `out`. Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w witrynie Azure portal. |
|**name** | Nie dotyczy | Nazwa zmiennej użytą w kodzie funkcji, który reprezentuje tabelą lub obiektem. Ustaw `$return` odwoływać się do wartości zwracanej funkcji.| 
|**tableName** |**TableName** | Nazwa tabeli.| 
|**partitionKey** |**PartitionKey** | Klucz partycji tabeli jednostki do zapisania. Zobacz [sekcji](#output---usage) wskazówki dotyczące sposobu używania tej właściwości.| 
|**rowKey** |**RowKey** | Klucz wiersza jednostki tabeli do zapisu. Zobacz [sekcji](#output---usage) wskazówki dotyczące sposobu używania tej właściwości.| 
|**połączenia** |**połączenia** | Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji rozpoczyna się od "AzureWebJobs", można określić tylko pozostałą część nazwy w tym miejscu. Na przykład jeśli ustawisz `connection` do "Mój_magazyn", środowisko uruchomieniowe usługi Functions wyszukuje ustawienie aplikacji o nazwie "AzureWebJobsMyStorage." Jeśli pozostawisz `connection` pusta, środowisko uruchomieniowe usługi Functions korzysta z domyślne parametry połączenia magazynu w ustawieniach aplikacji, który nosi nazwę `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

Magazyn tabel danych wyjściowych powiązanie obsługuje następujące scenariusze:

* **Zapis jeden wiersz w dowolnym języku**

  W języku C# i skrypt języka C#, dostęp do jednostki tabeli danych wyjściowych za pomocą parametru metody, takie jak `out T paramName` lub wartości zwracanej przez funkcję. W języku C# `paramName` jest wartością określoną w `name` właściwość *function.json*. `T` mogą być dowolnego typu serializacji, jeśli klucz partycji i klucz wiersza są dostarczane przez *function.json* pliku lub `Table` atrybutu. W przeciwnym razie `T` musi być typem, który zawiera `PartitionKey` i `RowKey` właściwości. W tym scenariuszu `T` zwykle implementuje `ITableEntity` lub pochodzi od klasy `TableEntity`, ale nie musi.

* **Wpisz jeden lub więcej wierszy w C# lub C# skryptu**

  W języku C# i skrypt języka C#, dostęp do jednostki tabeli danych wyjściowych przy użyciu parametru metody `ICollector<T> paramName` lub `IAsyncCollector<T> paramName`. W języku C# `paramName` jest wartością określoną w `name` właściwość *function.json*. `T` Określa schemat jednostki, które chcesz dodać. Zazwyczaj `T` pochodzi od klasy `TableEntity` lub implementuje `ITableEntity`, ale nie musi. Klucz partycji i wiersza klucza wartości w *function.json* lub `Table` atrybut konstruktora nie są używane w tym scenariuszu.

  Alternatywą jest użycie `CloudTable` parametru metody, aby zapisać do tabeli przy użyciu zestawu SDK usługi Azure Storage. Jeśli próbujesz powiązać `CloudTable` i komunikat o błędzie, upewnij się, że odwołanie do [poprawnej wersji zestawu SDK usługi Storage](#azure-storage-sdk-version-in-functions-1x). Przykładowy kod, który wiąże `CloudTable`, zapoznaj się z przykładami powiązania danych wejściowych dla [C#](#input---c-example---cloudtable) lub [skrypt języka C#](#input---c-script-example---cloudtable) we wcześniejszej części tego artykułu.

* **Zapisanie co najmniej jeden wiersz w języku JavaScript**

  W funkcji języka JavaScript, dostęp do tabeli danych wyjściowych za pomocą `context.bindings.<name>`.

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie | Tematy pomocy |
|---|---|
| Tabela | [Kody błędów w tabeli](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Obiekt blob, tabela, kolejka | [Kody błędów magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Obiekt blob, tabela, kolejka | [Rozwiązywanie problemów](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)
