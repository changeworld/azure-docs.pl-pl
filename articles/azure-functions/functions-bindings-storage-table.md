---
title: Azure tabeli powiązania magazynu dla usługi Azure Functions
description: Zrozumienie, jak używać powiązania magazynu tabel Azure w usługi Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: funkcje usługi Azure, funkcje, przetwarzania zdarzeń, dynamiczne obliczeń niekorzystającą architektury
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: tdykstra
ms.openlocfilehash: e6d2891a8ea531bf5c7cc7e1c74b890e01f2b56b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure tabeli powiązania magazynu dla usługi Azure Functions

W tym artykule opisano sposób pracy z magazynu tabel Azure powiązania usługi Azure Functions. Azure Functions obsługuje wejściowa i wyjściowa powiązania dla magazynu tabel Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Pakiety

Powiązania magazynu tabeli znajdują się w [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) pakietu NuGet. Kod źródłowy dla pakietu jest w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/) repozytorium GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="input"></a>Dane wejściowe

Użyj powiązania wejściowego magazynu tabel Azure, aby odczytać tabeli na koncie magazynu Azure.

## <a name="input---example"></a>Dane wejściowe — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [C# odczytu jedną jednostkę](#input---c-example-1)
* [C# odczytu wiele jednostek](#input---c-example-2)
* [C# skrypt - odczytu jedną jednostkę](#input---c-script-example-1)
* [C# skrypt - odczytu wiele jednostek](#input---c-script-example-2)
* [F#](#input---f-example-2)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example-1"></a>Dane wejściowe — przykład C# 1

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) które odczytuje wiersz pojedynczej tabeli. 

Wartość klucza wiersza "{queueTrigger}" wskazuje, czy klucz wiersza pochodzą z ciągu kolejki wiadomości.

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
        TraceWriter log)
    {
        log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="input---c-example-2"></a>Dane wejściowe — przykład C# 2

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) które odczytuje wiele wierszy tabeli. Należy pamiętać, że `MyPoco` pochodną klasy `TableEntity`.

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
        TraceWriter log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="input---c-script-example-1"></a>Dane wejściowe - C# przykładowy skrypt 1

W poniższym przykładzie przedstawiono powiązanie wejściowej tabeli w *function.json* pliku i [skryptu C#](functions-reference-csharp.md) kodu korzystającego z powiązania. Funkcja używa wyzwalacz kolejki można odczytać wiersza pojedynczej tabeli. 

*Function.json* plik Określa `partitionKey` i `rowKey`. `rowKey` Wartość "{queueTrigger}" wskazuje, czy klucz wiersza pochodzą z ciągu kolejki wiadomości.

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

Oto kod skryptu C#:

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="input---c-script-example-2"></a>Dane wejściowe - C# przykładowy skrypt 2

W poniższym przykładzie przedstawiono powiązanie wejściowej tabeli w *function.json* pliku i [skryptu C#](functions-reference-csharp.md) kodu korzystającego z powiązania. Funkcja odczytuje jednostek dla klucza partycji, który jest określony w komunikacie kolejki.

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

Kod skryptu C# dodaje odwołanie do zestawu SDK usługi Magazyn Azure, aby typ jednostki mogą dziedziczyć po `TableEntity`:

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="input---f-example"></a>Dane wejściowe — przykład F #

W poniższym przykładzie przedstawiono powiązanie wejściowej tabeli w *function.json* pliku i [F # skrypt](functions-reference-fsharp.md) kodu korzystającego z powiązania. Funkcja używa wyzwalacz kolejki można odczytać wiersza pojedynczej tabeli. 

*Function.json* plik Określa `partitionKey` i `rowKey`. `rowKey` Wartość "{queueTrigger}" wskazuje, czy klucz wiersza pochodzą z ciągu kolejki wiadomości.

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

Oto kod F #:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

### <a name="input---javascript-example"></a>Dane wejściowe — przykład JavaScript

W poniższym przykładzie przedstawiono powiązanie wejściowej tabeli w *function.json* plików i [kod JavaScript] (funkcje — odwołanie node.md), która używa powiązania. Funkcja używa wyzwalacz kolejki można odczytać wiersza pojedynczej tabeli. 

*Function.json* plik Określa `partitionKey` i `rowKey`. `rowKey` Wartość "{queueTrigger}" wskazuje, czy klucz wiersza pochodzą z ciągu kolejki wiadomości.

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

Oto kod JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

## <a name="input---attributes"></a>Dane wejściowe — atrybuty
 
W [bibliotek klas C#](functions-dotnet-class-library.md), umożliwia skonfigurowanie powiązania wejściowego tabeli następujące atrybuty:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs)

  Konstruktor atrybutu ma nazwę tabeli klucza partycji i klucz wiersza. Może służyć parametrem out lub wartości zwracanej funkcji, jak pokazano w poniższym przykładzie:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      TraceWriter log)
  {
      ...
  }
  ```

  Można ustawić `Connection` właściwości w celu określenia konta magazynu do użycia, jak pokazano w poniższym przykładzie:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      TraceWriter log)
  {
      ...
  }
  ```

  Pełny przykład, zobacz [Input - C# przykład](#input---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Udostępnia innym sposobem określania konta magazynu do użycia. Konstruktor przyjmuje nazwę ustawienia aplikacji, która zawiera parametry połączenia magazynu. Ten atrybut można stosować na poziomie klasy parametrów, metody lub. W poniższym przykładzie przedstawiono poziom klasy i metody:

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

Konta magazynu do użycia jest określany w następującej kolejności:

* `Table` Atrybutu `Connection` właściwości.
* `StorageAccount` Atrybut zastosowany do tego samego parametru jako `Table` atrybutu.
* `StorageAccount` Atrybut zastosowany do funkcji.
* `StorageAccount` Atrybut zastosowany do klasy.
* Domyślne konto magazynu dla funkcji aplikacji (ustawienie aplikacji "AzureWebJobsStorage").

## <a name="input---configuration"></a>Dane wejściowe — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `Table` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typ** | Nie dotyczy | należy wybrać opcję `table`. Ta właściwość ma wartość automatycznie, podczas tworzenia powiązania w portalu Azure.|
|**Kierunek** | Nie dotyczy | należy wybrać opcję `in`. Ta właściwość ma wartość automatycznie, podczas tworzenia powiązania w portalu Azure. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej, która reprezentuje tabeli lub obiektu kodu funkcji. | 
|**tableName** | **TableName** | Nazwa tabeli.| 
|**PartitionKey** | **PartitionKey** |Opcjonalny. Klucz partycji tabeli jednostki do odczytu. Zobacz [użycia](#input---usage) sekcji, aby uzyskać wskazówki dotyczące sposobu używania tej właściwości.| 
|**rowKey** |**RowKey** | Opcjonalny. Klucz wiersza jednostki tabeli do odczytu. Zobacz [użycia](#input---usage) sekcji, aby uzyskać wskazówki dotyczące sposobu używania tej właściwości.| 
|**podejmij** |**podejmij** | Opcjonalny. Maksymalna liczba jednostek do odczytu w języku JavaScript. Zobacz [użycia](#input---usage) sekcji, aby uzyskać wskazówki dotyczące sposobu używania tej właściwości.| 
|**Filtr** |**Filtr** | Opcjonalny. Wyrażenie filtru OData dla tabeli danych wejściowych w języku JavaScript. Zobacz [użycia](#input---usage) sekcji, aby uzyskać wskazówki dotyczące sposobu używania tej właściwości.| 
|**Połączenia** |**Połączenia** | Nazwa ustawienia aplikacji, która zawiera parametry połączenia magazynu do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji rozpoczyna się od "AzureWebJobs", można określić tylko w pozostałej części nazwy w tym miejscu. Na przykład jeśli ustawisz `connection` do "MyStorage" środowisko uruchomieniowe Functions szuka ustawienie aplikacji o nazwie "AzureWebJobsMyStorage." Jeśli opuścisz `connection` pusta, środowisko uruchomieniowe Functions używa domyślnego ciągu połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Dane wejściowe — użycie

Powiązania wejściowego magazyn tabel obsługuje następujące scenariusze:

* **Przeczytaj jeden wiersz w języku C# lub skryptu C#**

  Ustaw `partitionKey` i `rowKey`. Dostęp do danych tabeli za pomocą parametru metody `T <paramName>`. W języku C# skryptu `paramName` jest wartością określoną w `name` właściwość *function.json*. `T` Zazwyczaj jest typu, który implementuje `ITableEntity` pochodną `TableEntity`. `filter` i `take` właściwości nie są używane w tym scenariuszu. 

* **Przeczytaj, co najmniej jeden wiersz w języku C# lub skryptu C#**

  Dostęp do danych tabeli za pomocą parametru metody `IQueryable<T> <paramName>`. W języku C# skryptu `paramName` jest wartością określoną w `name` właściwość *function.json*. `T` musi być typu, który implementuje `ITableEntity` pochodną `TableEntity`. Można użyć `IQueryable` metody do dowolnego filtrowanie wymagane. `partitionKey`, `rowKey`, `filter`, I `take` właściwości nie są używane w tym scenariuszu.  

> [!NOTE]
> `IQueryable` nie jest obsługiwany w [środowisko uruchomieniowe Functions v2](functions-versions.md). Alternatywą jest [użyć parametru metody paramName CloudTable](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) odczytać tabeli za pomocą zestawu SDK usługi Magazyn Azure.

* **Co najmniej jeden wiersz w języku JavaScript do odczytu**

  Ustaw `filter` i `take` właściwości. Nie należy ustawiać `partitionKey` lub `rowKey`. Dostęp do wprowadzania tabeli jednostki (lub jednostek) przy użyciu `context.bindings.<name>`. Zdeserializowana obiekty mają `RowKey` i `PartitionKey` właściwości.

## <a name="output"></a>Dane wyjściowe

Za pomocą raportu magazynu tabel Azure powiązanie do zapisywania jednostek w tabeli na koncie magazynu Azure.

> [!NOTE]
> To powiązanie danych wyjściowych nie obsługuje ich aktualizowania istniejących obiektów. Użyj `TableOperation.Replace` operacji [z zestawu SDK usługi Magazyn Azure](https://docs.microsoft.com/en-us/azure/cosmos-db/table-storage-how-to-use-dotnet#replace-an-entity) do zaktualizowania istniejącej jednostki.   

## <a name="output---example"></a>OUTPUT — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [C#](#output---c-example)
* [Skryptu C# (csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Dane wyjściowe — przykład C#

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) używającą wyzwalacz protokołu HTTP do zapisania pojedynczej tabeli wiersza. 

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
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

### <a name="output---c-script-example"></a>Dane wyjściowe — przykładowy skrypt w języku C#

W poniższym przykładzie przedstawiono powiązanie w tabeli danych wyjściowych *function.json* pliku i [skryptu C#](functions-reference-csharp.md) kodu korzystającego z powiązania. Funkcja zapisuje wiele jednostek tabeli.

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

Oto kod skryptu C#:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
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

### <a name="output---f-example"></a>Dane wyjściowe — przykład F #

W poniższym przykładzie przedstawiono powiązanie w tabeli danych wyjściowych *function.json* pliku i [F # skrypt](functions-reference-fsharp.md) kodu korzystającego z powiązania. Funkcja zapisuje wiele jednostek tabeli.

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

Oto kod F #:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

### <a name="output---javascript-example"></a>Dane wyjściowe — przykład JavaScript

W poniższym przykładzie przedstawiono powiązanie w tabeli danych wyjściowych *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) używającą powiązania. Funkcja zapisuje wiele jednostek tabeli.

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

## <a name="output---attributes"></a>Dane wyjściowe — atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs).

Konstruktor atrybutu ma nazwy tabeli. Mogą być używane na `out` parametrów lub wartości zwracanej funkcji, jak pokazano w poniższym przykładzie:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
{
    ...
}
```

Można ustawić `Connection` właściwości w celu określenia konta magazynu do użycia, jak pokazano w poniższym przykładzie:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
{
    ...
}
```

Pełny przykład, zobacz [dane wyjściowe — przykład C#](#output---c-example).

Można użyć `StorageAccount` atrybutu, aby określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji, zobacz [Input - atrybutów](#input---attributes).

## <a name="output---configuration"></a>OUTPUT — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `Table` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typ** | Nie dotyczy | należy wybrać opcję `table`. Ta właściwość ma wartość automatycznie, podczas tworzenia powiązania w portalu Azure.|
|**Kierunek** | Nie dotyczy | należy wybrać opcję `out`. Ta właściwość ma wartość automatycznie, podczas tworzenia powiązania w portalu Azure. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej używany w funkcji kod, który reprezentuje tabelę lub jednostki. Ustaw `$return` odwoływać się do wartości zwracane funkcji.| 
|**tableName** |**TableName** | Nazwa tabeli.| 
|**PartitionKey** |**PartitionKey** | Klucz partycji tabeli jednostki do zapisania. Zobacz [sekcji użycia](#output---usage) wskazówki dotyczące sposobu używania tej właściwości.| 
|**rowKey** |**RowKey** | Klucz wiersza jednostki tabeli do zapisu. Zobacz [sekcji użycia](#output---usage) wskazówki dotyczące sposobu używania tej właściwości.| 
|**Połączenia** |**Połączenia** | Nazwa ustawienia aplikacji, która zawiera parametry połączenia magazynu do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji rozpoczyna się od "AzureWebJobs", można określić tylko w pozostałej części nazwy w tym miejscu. Na przykład jeśli ustawisz `connection` do "MyStorage" środowisko uruchomieniowe Functions szuka ustawienie aplikacji o nazwie "AzureWebJobsMyStorage." Jeśli opuścisz `connection` pusta, środowisko uruchomieniowe Functions używa domyślnego ciągu połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

Magazyn tabel danych wyjściowych powiązanie obsługuje następujące scenariusze:

* **Zapisanie jednego wiersza w dowolnym języku**

  W języku C# i skryptu C#, dostęp do jednostki tabeli danych wyjściowych przy użyciu parametru metody takie jak `out T paramName` ani funkcja zwracanej wartości. W języku C# skryptu `paramName` jest wartością określoną w `name` właściwość *function.json*. `T` mogą być dowolnego typu serializacji, jeśli klucz partycji i klucz wiersza są dostarczane przez *function.json* pliku lub `Table` atrybutu. W przeciwnym razie `T` musi być typem, który zawiera `PartitionKey` i `RowKey` właściwości. W tym scenariuszu `T` zwykle implementuje `ITableEntity` lub pochodzi z `TableEntity`, ale nie ma.

* **Wpisz jeden lub więcej wierszy w C# lub języka C#**

  W języku C# i skryptu C#, dostęp do jednostki tabeli danych wyjściowych przy użyciu parametru metody `ICollector<T> paramName` lub `IAsyncCollector<T> paramName`. W języku C# skryptu `paramName` jest wartością określoną w `name` właściwość *function.json*. `T` Określa schemat jednostek, które chcesz dodać. Zazwyczaj `T` pochodzi z `TableEntity` lub implementuje `ITableEntity`, ale nie ma. Klucz partycji i wiersza wartości w klucza *function.json* lub `Table` konstruktora atrybutów nie są używane w tym scenariuszu.

  Alternatywą jest użycie `CloudTable paramName` parametru metody, aby zapisać go do tabeli przy użyciu zestawu SDK usługi Magazyn Azure.

* **Zapisanie co najmniej jeden wiersz w języku JavaScript**

  W funkcji JavaScript, dostęp do danych wyjściowych za pomocą tabeli `context.bindings.<name>`.

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie | Informacje ogólne |
|---|---|
| Tabela | [Kody błędów w tabeli](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Obiekt blob, tabeli, kolejki | [Kody błędów magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Obiekt blob, tabeli, kolejki | [Rozwiązywanie problemów](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o usługę Azure functions wyzwalaczy i powiązań](functions-triggers-bindings.md)
