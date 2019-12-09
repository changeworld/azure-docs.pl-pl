---
title: Powiązania usługi Azure Table Storage dla Azure Functions
description: Dowiedz się, jak używać powiązań usługi Azure Table Storage w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: 1308463694754231aa6d770bf716fd3def219981
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74925321"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Powiązania usługi Azure Table Storage dla Azure Functions

W tym artykule opisano sposób pracy z powiązaniami w usłudze Azure Table Storage w programie Azure Functions. Azure Functions obsługuje powiązania danych wejściowych i wyjściowych dla usługi Azure Table Storage.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania magazynu tabel są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) w wersji 2. x. Kod źródłowy dla pakietu znajduje się w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) repozytorium GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakiety — funkcje 2. x i nowsze

Powiązania magazynu tabel są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) w wersji 3. x. Kod źródłowy dla pakietu znajduje się w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) repozytorium GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Dane wejściowe

Użyj powiązania danych wejściowych usługi Azure Table Storage, aby odczytać tabelę na koncie usługi Azure Storage.

## <a name="input---example"></a>Dane wejściowe — przykład

Zobacz przykład specyficzny dla języka:

* [C#Odczytaj jedną jednostkę](#input---c-example---one-entity)
* [C#Powiąż z interfejsem IQueryable](#input---c-example---iqueryable)
* [C#Powiąż z chmurą](#input---c-example---cloudtable)
* [C#skrypt odczytał jedną jednostkę](#input---c-script-example---one-entity)
* [C#Powiązanie skryptu z interfejsem IQueryable](#input---c-script-example---iqueryable)
* [C#Powiązanie skryptu z chmurą](#input---c-script-example---cloudtable)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)
* [Java](#input---java-example)

### <a name="input---c-example---one-entity"></a>Input- C# przykład-jedna jednostka

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która odczytuje pojedynczy wiersz tabeli. 

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

### <a name="input---c-example---iqueryable"></a>Dane wejściowe C# — przykład — IQueryable

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) odczytującą wiele wierszy tabeli. Należy zauważyć, że Klasa `MyPoco` dziedziczy z `TableEntity`.

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

### <a name="input---c-example---cloudtable"></a>Dane wejściowe C# — przykład — chmura

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

### <a name="input---c-script-example---one-entity"></a>Dane wejściowe C# — przykład skryptu — jedna jednostka

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

### <a name="input---c-script-example---iqueryable"></a>Input- C# Script — przykład — IQueryable

Poniższy przykład przedstawia powiązanie danych wejściowych tabeli w pliku *Function. JSON* i [ C# kodzie skryptu](functions-reference-csharp.md) , który używa powiązania. Funkcja odczytuje jednostki dla klucza partycji, który jest określony w komunikacie kolejki.

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

### <a name="input---c-script-example---cloudtable"></a>Dane wejściowe C# — przykład skryptu — chmura

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

### <a name="input---f-example"></a>Dane wejściowe F# — przykład

Poniższy przykład przedstawia powiązanie danych wejściowych tabeli w pliku *Function. JSON* i [ F# kodzie skryptu](functions-reference-fsharp.md) , który używa powiązania. Funkcja używa wyzwalacza kolejki do odczytywania pojedynczego wiersza tabeli. 

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

### <a name="input---javascript-example"></a>Przykład Input-JavaScript

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

[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

### <a name="input---java-example"></a>Dane wejściowe — przykład Java

Poniższy przykład pokazuje funkcję wyzwalaną przez protokół HTTP, która zwraca łączną liczbę elementów w określonej partycji w magazynie tabel.

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
 
W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj następujących atrybutów, aby skonfigurować powiązanie danych wejściowych tabeli:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Konstruktor atrybutu przyjmuje nazwę tabeli, klucz partycji i klucz wiersza. Może być używana dla parametru out lub zwracanej wartości funkcji, jak pokazano w następującym przykładzie:

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

* `Table` Atrybutu `Connection` właściwości.
* `StorageAccount` Zastosowany do tego samego parametru jako `Table` atrybutu.
* `StorageAccount` Zastosowany do funkcji.
* `StorageAccount` Zastosowany do klasy.
* Domyślne konto magazynu dla aplikacji funkcji (ustawienie aplikacji "AzureWebJobsStorage").

## <a name="input---java-annotations"></a>Input — adnotacje języka Java

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj adnotacji `@TableInput` w przypadku parametrów, których wartość pochodzi z magazynu tabel.  Tej adnotacji można używać w przypadku natywnych typów Java, Pojo lub wartości null przy użyciu opcjonalnych >\<T. 

## <a name="input---configuration"></a>Dane wejściowe — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `Table` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | nd. | Musi być równa `table`. Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w Azure Portal.|
|**direction** | nd. | Musi być równa `in`. Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w Azure Portal. |
|**Nazwa** | nd. | Nazwa zmiennej, która reprezentuje tabelę lub jednostkę w kodzie funkcji. | 
|**tableName** | **TableName** | Nazwa tabeli.| 
|**partitionKey** | **PartitionKey** |Opcjonalny. Klucz partycji jednostki tabeli do odczytania. Zapoznaj się z sekcją [użycie](#input---usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**rowKey** |**RowKey** | Opcjonalny. Klucz wiersza jednostki tabeli, który ma zostać odczytany. Zapoznaj się z sekcją [użycie](#input---usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**take** |**Take** | Opcjonalny. Maksymalna liczba jednostek do odczytania w języku JavaScript. Zapoznaj się z sekcją [użycie](#input---usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**filter (filtrowanie)** |**Filtr** | Opcjonalny. Wyrażenie filtru OData dla danych wejściowych tabeli w języku JavaScript. Zapoznaj się z sekcją [użycie](#input---usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**połączenia** |**Połączenie** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia magazynu, które będą używane dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", w tym miejscu możesz określić tylko resztę nazwy. Na przykład jeśli ustawisz `connection` na "WebStorage", środowisko uruchomieniowe funkcji szuka ustawienia aplikacji o nazwie "AzureWebJobsMyStorage". W przypadku pozostawienia `connection` pustego środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Dane wejściowe — użycie

Powiązanie danych wejściowych magazynu tabel obsługuje następujące scenariusze:

* **Odczytaj jeden wiersz C# w C# lub skrypt**

  Ustaw `partitionKey` i `rowKey`. Dostęp do danych tabeli przy użyciu `T <paramName>`parametru metody. W C# skrypcie `paramName` jest wartością określoną we właściwości `name` *funkcji Function. JSON*. `T` jest zazwyczaj typem implementującym `ITableEntity` lub pochodzącym z `TableEntity`. W tym scenariuszu nie są używane właściwości `filter` i `take`. 

* **Odczytaj jeden lub więcej wierszy C# w C# lub skrypcie**

  Dostęp do danych tabeli przy użyciu `IQueryable<T> <paramName>`parametru metody. W C# skrypcie `paramName` jest wartością określoną we właściwości `name` *funkcji Function. JSON*. `T` musi być typem implementującym `ITableEntity` lub pochodną z `TableEntity`. Możesz użyć metod `IQueryable`, aby wykonać dowolne filtrowanie. Właściwości `partitionKey`, `rowKey`, `filter`i `take` nie są używane w tym scenariuszu.  

  > [!NOTE]
  > `IQueryable` nie jest obsługiwana w [środowisku uruchomieniowym funkcji v2](functions-versions.md). Alternatywą jest [użycie parametru metody w chmurze paramName](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) , aby odczytać tabelę przy użyciu zestawu SDK usługi Azure Storage. Jeśli próbujesz powiązać z `CloudTable` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

* **Odczytaj jeden lub więcej wierszy w języku JavaScript**

  Ustaw właściwości `filter` i `take`. Nie ustawiaj `partitionKey` ani `rowKey`. Uzyskaj dostęp do jednostki tabeli wejściowej (lub jednostek) za pomocą `context.bindings.<BINDING_NAME>`. Obiekty deserializowane mają właściwości `RowKey` i `PartitionKey`.

## <a name="output"></a>Dane wyjściowe

Za pomocą powiązania danych wyjściowych usługi Azure Table Storage można pisać jednostki w tabeli na koncie usługi Azure Storage.

> [!NOTE]
> To powiązanie danych wyjściowych nie obsługuje aktualizowania istniejących jednostek. Użyj operacji `TableOperation.Replace` [z zestawu SDK usługi Azure Storage,](https://docs.microsoft.com/azure/cosmos-db/tutorial-develop-table-dotnet#delete-an-entity) aby zaktualizować istniejącą jednostkę.   

## <a name="output---example"></a>Dane wyjściowe — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#output---c-example)
* [Skryptu C# (csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Dane wyjściowe — przykład w języku C#

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

### <a name="output---c-script-example"></a>Dane wyjściowe — przykładowy skrypt w języku C#

Poniższy przykład przedstawia powiązanie danych wyjściowych tabeli w pliku *Function. JSON* i [ C# kodzie skryptu](functions-reference-csharp.md) , który używa powiązania. Funkcja zapisuje wiele jednostek tabeli.

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

Poniższy przykład przedstawia powiązanie danych wyjściowych tabeli w pliku *Function. JSON* i [ F# kodzie skryptu](functions-reference-fsharp.md) , który używa powiązania. Funkcja zapisuje wiele jednostek tabeli.

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

Poniższy przykład przedstawia powiązanie danych wyjściowych tabeli w pliku *Function. JSON* i [funkcji języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja zapisuje wiele jednostek tabeli.

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

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj klasy [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Konstruktor atrybutu przyjmuje nazwę tabeli. Można go użyć na parametrze `out` lub wartości zwracanej funkcji, jak pokazano w następującym przykładzie:

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

Aby uzyskać kompletny przykład, zobacz [dane wyjściowe — przykład w języku C#](#output---c-example).

Można użyć atrybutu `StorageAccount`, aby określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji, zobacz [atrybuty danych wejściowych](#input---attributes).

## <a name="output---configuration"></a>Dane wyjściowe — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `Table` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | nd. | Musi być równa `table`. Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w Azure Portal.|
|**direction** | nd. | Musi być równa `out`. Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w Azure Portal. |
|**Nazwa** | nd. | Nazwa zmiennej używana w kodzie funkcji, która reprezentuje tabelę lub jednostkę. Ustaw `$return`, aby odwoływać się do zwracanej wartości funkcji.| 
|**tableName** |**TableName** | Nazwa tabeli.| 
|**partitionKey** |**PartitionKey** | Klucz partycji jednostki tabeli do zapisania. Zapoznaj się z [sekcją użycie](#output---usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**rowKey** |**RowKey** | Klucz wiersza jednostki tabeli do zapisania. Zapoznaj się z [sekcją użycie](#output---usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**połączenia** |**Połączenie** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia magazynu, które będą używane dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", w tym miejscu możesz określić tylko resztę nazwy. Na przykład jeśli ustawisz `connection` na "WebStorage", środowisko uruchomieniowe funkcji szuka ustawienia aplikacji o nazwie "AzureWebJobsMyStorage". W przypadku pozostawienia `connection` pustego środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

Powiązanie danych wyjściowych magazynu tabel obsługuje następujące scenariusze:

* **Napisz jeden wiersz w dowolnym języku**

  W C# programie C# i można uzyskać dostęp do jednostki tabeli wyjściowej przy użyciu parametru metody, takiego jak `out T paramName` lub wartość zwracaną przez funkcję. W C# skrypcie `paramName` jest wartością określoną we właściwości `name` *funkcji Function. JSON*. `T` może być dowolnym typem możliwym do serializacji, jeśli klucz partycji i klucz wiersza są dostarczane przez plik *Function. JSON* lub atrybut `Table`. W przeciwnym razie `T` musi być typem, który zawiera właściwości `PartitionKey` i `RowKey`. W tym scenariuszu `T` zazwyczaj implementuje `ITableEntity` lub pochodzi z `TableEntity`, ale nie jest to konieczne.

* **Napisz jeden lub więcej wierszy w C# lub C# skrypcie**

  W C# programie C# i skrypt Uzyskuj dostęp do jednostki tabeli wyjściowej przy użyciu parametru metody `ICollector<T> paramName` lub `IAsyncCollector<T> paramName`. W C# skrypcie `paramName` jest wartością określoną we właściwości `name` *funkcji Function. JSON*. `T` określa schemat jednostek, które mają zostać dodane. Zwykle `T` pochodzi od `TableEntity` lub implementuje `ITableEntity`, ale nie jest to konieczne. Wartości klucza partycji i klucza wiersza w *funkcji Function. JSON* lub konstruktora `Table` Attribute nie są używane w tym scenariuszu.

  Alternatywą jest użycie `CloudTable` parametr metody do zapisu w tabeli przy użyciu zestawu SDK usługi Azure Storage. Jeśli próbujesz powiązać z `CloudTable` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](#azure-storage-sdk-version-in-functions-1x). Przykładowy kod, który wiąże się z `CloudTable`, zawiera przykłady powiązań wejściowych dla [C#](#input---c-example---cloudtable) lub [ C# skrypt](#input---c-script-example---cloudtable) wcześniej w tym artykule.

* **Napisz jeden lub więcej wierszy w języku JavaScript**

  W funkcjach języka JavaScript Uzyskuj dostęp do tabeli wyjściowej przy użyciu `context.bindings.<BINDING_NAME>`.

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie | Informacje ogólne |
|---|---|
| Tabela | [Kody błędów tabeli](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Obiekt BLOB, tabela, kolejka | [Kody błędów magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Obiekt BLOB, tabela, kolejka | [Rozwiązywanie problemów](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)
