---
title: Powiązania Azure Cosmos DB dla funkcji 2. x
description: Dowiedz się, jak używać usługi Azure Cosmos DB, wyzwalaczy i powiązań w usłudze Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 3ef2fdcaefeedb0769eac34d292e67a99524a6f2
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168065"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x"></a>Powiązania Azure Cosmos DB dla Azure Functions 2. x

> [!div class="op_single_selector" title1="Wybierz wersję środowiska uruchomieniowego Azure Functions, którego używasz: "]
> * [Wersja 1](functions-bindings-cosmosdb.md)
> * [Wersja 2](functions-bindings-cosmosdb-v2.md)

W tym artykule opisano sposób pracy z powiązaniami [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) w Azure Functions 2. x. Usługi Azure Functions obsługuje wyzwalanie, dane wejściowe i wyjściowe powiązań usługi Azure Cosmos DB.

> [!NOTE]
> Ten artykuł dotyczy [Azure Functions wersji 2. x](functions-versions.md).  Aby uzyskać informacje o sposobach używania tych powiązań w funkcjach 1. x, zobacz [Azure Cosmos DB powiązania dla Azure Functions 1. x](functions-bindings-cosmosdb.md).
>
> To powiązanie pierwotnie nosiła nazwę bazy danych DocumentDB. W funkcjach w wersji 2. x wyzwalacz, powiązania i pakiet mają nazwę Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-apis"></a>Obsługiwane interfejsy API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania Azure Cosmos DB dla funkcji w wersji 2. x są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) w wersji 3. x. Kod źródłowy powiązań znajduje się w repozytorium [Azure-WebJobs-SDK-Extensions — rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Wyzwalacz

Wyzwalacz Azure Cosmos DB używa [źródła zmian Azure Cosmos DB](../cosmos-db/change-feed.md) do nasłuchiwania operacji wstawiania i aktualizacji między partycjami. Kanał informacyjny zmian publikuje wstawienia i aktualizacje, nie do usunięcia.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

W poniższym przykładzie pokazano [ C# funkcję](functions-dotnet-class-library.md) , która jest wywoływana, gdy w określonej bazie danych i kolekcji są wstawiane lub aktualizowane.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Poniższy przykład przedstawia powiązanie wyzwalacza Cosmos DB w pliku *Function. JSON* i [ C# funkcji skryptu](functions-reference-csharp.md) , która używa powiązania. Funkcja zapisuje komunikaty dziennika po dodaniu lub zmodyfikowaniu Cosmos DB rekordów.

Oto dane powiązania w pliku *Function. JSON* :

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Poniżej przedstawiono kod skryptu języka C#:

```cs
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza Cosmos DB w pliku *Function. JSON* oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja zapisuje komunikaty dziennika po dodaniu lub zmodyfikowaniu Cosmos DB rekordów.

Oto dane powiązania w pliku *Function. JSON* :

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Poniżej przedstawiono kod JavaScript:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Poniższy przykład przedstawia powiązanie wyzwalacza Cosmos DB w pliku *Function. JSON* i [funkcji języka Python](functions-reference-python.md) , która używa powiązania. Funkcja zapisuje komunikaty dziennika, po zmodyfikowaniu rekordy usługi Cosmos DB.

Oto dane powiązania w pliku *Function. JSON* :

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Oto kod języka Python:

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

# <a name="javatabjava"></a>[Java](#tab/java)

Ta funkcja jest wywoływana, gdy w określonej bazie danych i kolekcji są wstawiane lub aktualizowane aktualizacje.

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj adnotacji `@CosmosDBTrigger` w przypadku parametrów, których wartość pochodzi z Cosmos DB.  Tej adnotacji można używać w przypadku natywnych typów Java, Pojo lub wartości null przy użyciu `Optional<T>`.

---

## <a name="trigger---attributes-and-annotations"></a>Wyzwalacz — atrybuty i adnotacje

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj atrybutu [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) .

Konstruktor atrybutu ma nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i innych właściwościach, które można skonfigurować, zobacz [wyzwalacz-konfiguracja](#trigger---configuration). Oto przykład atrybutu `CosmosDBTrigger` w sygnaturze metody:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

Pełny przykład można znaleźć w temacie [Trigger](#trigger).

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Atrybuty nie są obsługiwane przez C# skrypt.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="javatabjava"></a>[Java](#tab/java)

W [bibliotece środowiska uruchomieniowego usługi Java Functions](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)Użyj adnotacji `@CosmosDBInput` dla parametrów, które odczytują dane z Cosmos DB.

---

## <a name="trigger---configuration"></a>Wyzwalacz — Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* i `CosmosDBTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | musi być ustawiony na `cosmosDBTrigger`. |
|**direction** | Nie dotyczy | musi być ustawiony na `in`. Ten parametr jest ustawiany automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. |
|**Nazwij** | Nie dotyczy | Nazwa zmiennej, używany w kodzie funkcji, który reprezentuje listę dokumentów za pomocą zmian. |
|**connectionStringSetting**|**ConnectionStringSetting** | Nazwa ustawienia aplikacji zawierającego parametry połączenia używane do łączenia z konta usługi Azure Cosmos DB są monitorowane. |
|**Bazy**|**Bazy**  | Nazwa bazy danych Azure Cosmos DB za pomocą kolekcji są monitorowane. |
|**CollectionName** |**CollectionName** | Nazwa kolekcji są monitorowane. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Obowiązkowe Nazwa ustawienia aplikacji zawierającego parametry połączenia do konta Azure Cosmos DB, które przechowuje kolekcję dzierżawy. Gdy nie jest ustawiona, zostanie użyta wartość `connectionStringSetting`. Ten parametr jest automatycznie ustawiana podczas tworzenia powiązania w portalu. Parametry połączenia dla kolekcji dzierżaw musi mieć uprawnienia do zapisu.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Opcjonalnie) Nazwa bazy danych, który zawiera kolekcję używaną do przechowywania dzierżaw. Gdy nie jest ustawiona, zostanie użyta wartość ustawienia `databaseName`. Ten parametr jest automatycznie ustawiana podczas tworzenia powiązania w portalu. |
|**leaseCollectionName** | **LeaseCollectionName** | (Opcjonalnie) Nazwa kolekcji, używany do przechowywania dzierżaw. Gdy nie jest ustawiona, wartość `leases` jest używana. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Obowiązkowe Po ustawieniu na `true`, kolekcja dzierżaw zostanie automatycznie utworzona, gdy jeszcze nie istnieje. Wartością domyślną jest `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Obowiązkowe Określa liczbę jednostek żądania, które mają zostać przypisane podczas tworzenia kolekcji dzierżaw. To ustawienie jest używane tylko wtedy, gdy `createLeaseCollectionIfNotExists` jest ustawiona na `true`. Ten parametr jest automatycznie ustawiana, jeśli wiązanie jest tworzony przy użyciu portalu.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Obowiązkowe Po ustawieniu wartość jest dodawana jako prefiks do dzierżaw utworzonych w kolekcji dzierżawy dla tej funkcji. Użycie prefiksu umożliwia dwóm osobom Azure Functions współużytkowanie tej samej kolekcji dzierżawy przy użyciu różnych prefiksów.
|**feedPollDelay**| **FeedPollDelay**| Obowiązkowe Czas (w milisekundach) opóźnienia między sondowaniem partycji o nowe zmiany w strumieniu, po opróżnieniu wszystkich bieżących zmian. Wartość domyślna to 5 000 milisekund lub 5 sekund.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał można uruchamiać zadania obliczeniowe, jeśli partycje są dystrybuowane równomiernie między wystąpieniami znanych hostów. Wartość domyślna to 13000 (w sekundach 13).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał, dla której dzierżawy jest pobierany podczas dzierżawy, reprezentujący partycji. Jeśli dzierżawa nie zostanie odnowiony w tym przedziale czasu, spowoduje jego wygaśnięcia i własność partycji przejdzie do innego wystąpienia. Domyślna to 60 000 (60 sekund).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał odnawiania dla wszystkich dzierżaw dla partycji aktualnie trzymana przez wystąpienie. Wartość domyślna to 17000 (17 w sekundach).
|**checkpointFrequency**| **CheckpointFrequency**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał między punktami kontrolnymi dzierżawy. Wartość domyślna to zawsze po każdym wywołaniu funkcji.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Obowiązkowe Po ustawieniu ta właściwość ustawia maksymalną liczbę elementów odebranych na wywołanie funkcji. Jeśli operacje w monitorowanej kolekcji są wykonywane za pomocą procedur składowanych, [zakres transakcji](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) jest zachowywany podczas odczytywania elementów ze źródła zmian. W związku z tym liczba odebranych elementów może być wyższa niż określona wartość, dzięki czemu elementy zmienione przez tę samą transakcję są zwracane jako część jednej niepodzielnej partii.
|**startFromBeginning**| **StartFromBeginning**| Obowiązkowe Ta opcja informuje wyzwalacz, aby odczytał zmiany od początku historii zmian kolekcji zamiast od bieżącego czasu. Odczyt od początku działa tylko podczas pierwszego uruchomienia wyzwalacza, jak w kolejnych uruchomieniach, punkty kontrolne są już przechowywane. Ustawienie tej opcji na `true`, gdy istnieją już utworzone dzierżawy, nie mają żadnego efektu.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Wyzwalacz — użycie

Wyzwalacz wymaga drugiej kolekcji, która używa do przechowywania _dzierżaw_ w ramach partycji. Kolekcja monitorowanych i kolekcji, która zawiera dzierżaw musi być dostępny dla wyzwalacza do pracy.

>[!IMPORTANT]
> Jeśli wiele funkcji jest skonfigurowanych do korzystania z wyzwalacza Cosmos DB dla tej samej kolekcji, każda z tych funkcji powinna używać dedykowanej kolekcji dzierżaw lub określić inną `LeaseCollectionPrefix` dla każdej funkcji. W przeciwnym razie tylko jeden funkcje zostaną wyzwolone. Informacje na temat prefiksu znajdują się w [sekcji Konfiguracja](#trigger---configuration).

Wyzwalacz nie wskazuje, czy zaktualizowane lub wstawić dokumentu, po prostu zapewnia samego dokumentu. Jeśli wymagana jest obsługa aktualizacje i wstawienia w różny sposób, możesz można zrobić, implementując pola sygnatury czasowej dla wstawiania lub aktualizacji.

## <a name="input"></a>Dane wejściowe

Powiązania danych wejściowych usługi Azure Cosmos DB używa interfejsu API SQL do pobrania jednego lub więcej dokumentów usługi Azure Cosmos DB i przekazuje je do parametr wejściowy funkcji. Można określić parametry identyfikator lub kwerendę dokumentu oparte na wyzwalacz, który wywołuje funkcję.

> [!NOTE]
> Jeśli kolekcja jest [podzielona na partycje](../cosmos-db/partition-data.md#logical-partitions), operacje wyszukiwania muszą również określać wartość klucza partycji.
>

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz kolejki, wyszukiwanie identyfikatora z poziomu JSON](#queue-trigger-look-up-id-from-json-c)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania](#http-trigger-look-up-id-from-query-string-c)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy](#http-trigger-look-up-id-from-route-data-c)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy przy użyciu sqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [Wyzwalacz HTTP, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [Wyzwalacz HTTP, pobieranie wielu dokumentów przy użyciu DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Przykłady odnoszą się do prostego typu `ToDoItem`:

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

### <a name="queue-trigger-look-up-id-from-json"></a>Wyzwalacz kolejki, wyszukiwanie identyfikatora z poziomu JSON 

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez komunikatu w kolejce, która zawiera obiekt JSON. Wyzwalacz kolejki analizuje kod JSON w obiekcie typu `ToDoItemLookup`, który zawiera identyfikator i wartość klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania dokumentu `ToDoItem` z określonej bazy danych i kolekcji.

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

### <a name="http-trigger-look-up-id-from-query-string"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania, aby określić identyfikator i wartość klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania dokumentu `ToDoItem` z określonej bazy danych i kolekcji.

>[!NOTE]
>W parametrze ciągu zapytania HTTP jest rozróżniana wielkość liter.
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

### <a name="http-trigger-look-up-id-from-route-data"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa danych trasy do określenia identyfikatora i wartości klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania dokumentu `ToDoItem` z określonej bazy danych i kolekcji.

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

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy przy użyciu sqlQuery

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, że używa kierować dane do określenia Identyfikatora do wyszukania. Ten identyfikator jest używany do pobierania dokumentu `ToDoItem` z określonej bazy danych i kolekcji.

W przykładzie pokazano, jak używać wyrażenia powiązania w parametrze `SqlQuery`. Dane trasy można przekazać do parametru `SqlQuery`, jak pokazano, ale obecnie [nie można przekazać wartości ciągu zapytania](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).

> [!NOTE]
> Jeśli zachodzi potrzeba przeszukiwania tylko identyfikatora, zaleca się użycie wyszukiwania, podobnie jak w [poprzednich przykładach](#http-trigger-look-up-id-from-query-string-c), ponieważ będzie zużywał mniej [jednostek żądania](../cosmos-db/request-units.md). Operacje odczytu punktu (GET) są [bardziej wydajne](../cosmos-db/optimize-cost-queries.md) niż zapytania według identyfikatora.
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

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Wyzwalacz HTTP, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Zapytanie jest określone we właściwości atrybutu `SqlQuery`.

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

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>Wyzwalacz HTTP, pobieranie wielu dokumentów przy użyciu DocumentClient

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Kod używa wystąpienia `DocumentClient` dostarczonego przez powiązanie Azure Cosmos DB do odczytywania listy dokumentów. Wystąpienia `DocumentClient` można również użyć dla operacji zapisu.

> [!NOTE]
> Aby ułatwić testowanie, można również użyć interfejsu [IDocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.idocumentclient?view=azure-dotnet) .

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

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz kolejki, wyszukiwanie identyfikatora z ciągu](#queue-trigger-look-up-id-from-string-c-script)
* [Wyzwalacz kolejki, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania](#http-trigger-look-up-id-from-query-string-c-script)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy](#http-trigger-look-up-id-from-route-data-c-script)
* [Wyzwalacz HTTP, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Wyzwalacz HTTP, pobieranie wielu dokumentów przy użyciu DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Przykłady wyzwalacza HTTP odnoszą się do prostego typu `ToDoItem`:

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

### <a name="queue-trigger-look-up-id-from-string"></a>Wyzwalacz kolejki, wyszukiwanie identyfikatora z ciągu

Poniższy przykład przedstawia Cosmos DB dane wejściowe w pliku *Function. JSON* i [ C# funkcji skryptu](functions-reference-csharp.md) , która używa powiązania. Funkcja odczytuje pojedynczy dokument i aktualizuje wartość tekstu dokumentu.

Oto dane powiązania w pliku *Function. JSON* :

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
W sekcji [Konfiguracja](#input---configuration) objaśniono te właściwości.

Poniżej przedstawiono kod skryptu języka C#:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Wyzwalacz kolejki, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery

W poniższym przykładzie pokazano powiązanie danych wejściowych Azure Cosmos DB w pliku *Function. JSON* i [ C# funkcji skryptu](functions-reference-csharp.md) , która używa powiązania. Funkcja pobiera wiele dokumentów, określonych przez zapytanie SQL, przy użyciu wyzwalacza kolejki, aby dostosować parametry zapytania.

Wyzwalacz kolejki zawiera parametr `departmentId`. Komunikat w kolejce `{ "departmentId" : "Finance" }` zwróci wszystkie rekordy działu finansowego.

Oto dane powiązania w pliku *Function. JSON* :

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

W sekcji [Konfiguracja](#input---configuration) objaśniono te właściwości.

Poniżej przedstawiono kod skryptu języka C#:

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

### <a name="http-trigger-look-up-id-from-query-string"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania

Poniższy przykład pokazuje [ C# funkcję skryptu](functions-reference-csharp.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania, aby określić identyfikator i wartość klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania dokumentu `ToDoItem` z określonej bazy danych i kolekcji.

Oto plik *Function. JSON* :

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

Poniżej przedstawiono kod skryptu języka C#:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy

Poniższy przykład pokazuje [ C# funkcję skryptu](functions-reference-csharp.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa danych trasy do określenia identyfikatora i wartości klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania dokumentu `ToDoItem` z określonej bazy danych i kolekcji.

Oto plik *Function. JSON* :

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

Poniżej przedstawiono kod skryptu języka C#:

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

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Wyzwalacz HTTP, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery

Poniższy przykład pokazuje [ C# funkcję skryptu](functions-reference-csharp.md) , która pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Zapytanie jest określone we właściwości atrybutu `SqlQuery`.

Oto plik *Function. JSON* :

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

Poniżej przedstawiono kod skryptu języka C#:

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

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>Wyzwalacz HTTP, pobieranie wielu dokumentów przy użyciu DocumentClient

Poniższy przykład pokazuje [ C# funkcję skryptu](functions-reference-csharp.md) , która pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Kod używa wystąpienia `DocumentClient` dostarczonego przez powiązanie Azure Cosmos DB do odczytywania listy dokumentów. Wystąpienia `DocumentClient` można również użyć dla operacji zapisu.

Oto plik *Function. JSON* :

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

Poniżej przedstawiono kod skryptu języka C#:

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Ta sekcja zawiera następujące przykłady odczytywania pojedynczego dokumentu przez określenie wartości identyfikatora z różnych źródeł:

* [Wyzwalacz kolejki, wyszukiwanie identyfikatora z poziomu JSON](#queue-trigger-look-up-id-from-json-javascript)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania](#http-trigger-look-up-id-from-query-string-javascript)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy](#http-trigger-look-up-id-from-route-data-javascript)
* [Wyzwalacz kolejki, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Wyzwalacz kolejki, wyszukiwanie identyfikatora z poziomu JSON

Poniższy przykład przedstawia Cosmos DB dane wejściowe w pliku *Function. JSON* oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja odczytuje pojedynczy dokument i aktualizuje wartość tekstu dokumentu.

Oto dane powiązania w pliku *Function. JSON* :

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

W sekcji [Konfiguracja](#input---configuration) objaśniono te właściwości.

Poniżej przedstawiono kod JavaScript:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania

Poniższy przykład pokazuje [funkcję języka JavaScript](functions-reference-node.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania, aby określić identyfikator i wartość klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania dokumentu `ToDoItem` z określonej bazy danych i kolekcji.

Oto plik *Function. JSON* :

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

Poniżej przedstawiono kod JavaScript:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy

Poniższy przykład pokazuje [funkcję języka JavaScript](functions-reference-node.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa danych trasy do określenia identyfikatora i wartości klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania dokumentu `ToDoItem` z określonej bazy danych i kolekcji.

Oto plik *Function. JSON* :

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

Poniżej przedstawiono kod JavaScript:

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

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Wyzwalacz kolejki, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery

W poniższym przykładzie pokazano powiązanie danych wejściowych Azure Cosmos DB w pliku *Function. JSON* oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja pobiera wiele dokumentów, określonych przez zapytanie SQL, przy użyciu wyzwalacza kolejki, aby dostosować parametry zapytania.

Wyzwalacz kolejki zawiera parametr `departmentId`. Komunikat w kolejce `{ "departmentId" : "Finance" }` zwróci wszystkie rekordy działu finansowego.

Oto dane powiązania w pliku *Function. JSON* :

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

W sekcji [Konfiguracja](#input---configuration) objaśniono te właściwości.

Poniżej przedstawiono kod JavaScript:

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Ta sekcja zawiera następujące przykłady odczytywania pojedynczego dokumentu przez określenie wartości identyfikatora z różnych źródeł:

* [Wyzwalacz kolejki, wyszukiwanie identyfikatora z poziomu JSON](#queue-trigger-look-up-id-from-json-python)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania](#http-trigger-look-up-id-from-query-string-python)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy](#http-trigger-look-up-id-from-route-data-python)
* [Wyzwalacz kolejki, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Wyzwalacz kolejki, wyszukiwanie identyfikatora z poziomu JSON

Poniższy przykład przedstawia Cosmos DB dane wejściowe w pliku *Function. JSON* oraz [funkcja języka Python](functions-reference-python.md) , która używa powiązania. Funkcja odczytuje pojedynczy dokument i aktualizuje wartość tekstu dokumentu.

Oto dane powiązania w pliku *Function. JSON* :

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

W sekcji [Konfiguracja](#input---configuration) objaśniono te właściwości.

Oto kod języka Python:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania

Poniższy przykład pokazuje funkcję języka [Python](functions-reference-python.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania, aby określić identyfikator i wartość klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania dokumentu `ToDoItem` z określonej bazy danych i kolekcji.

Oto plik *Function. JSON* :

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

Oto kod języka Python:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy

Poniższy przykład pokazuje funkcję języka [Python](functions-reference-python.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa danych trasy do określenia identyfikatora i wartości klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania dokumentu `ToDoItem` z określonej bazy danych i kolekcji.

Oto plik *Function. JSON* :

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

Oto kod języka Python:

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

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Wyzwalacz kolejki, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery

W poniższym przykładzie pokazano powiązanie danych wejściowych Azure Cosmos DB w pliku *Function. JSON* oraz funkcja języka [Python](functions-reference-python.md) , która używa powiązania. Funkcja pobiera wiele dokumentów, określonych przez zapytanie SQL, przy użyciu wyzwalacza kolejki, aby dostosować parametry zapytania.

Wyzwalacz kolejki zawiera parametr `departmentId`. Komunikat w kolejce `{ "departmentId" : "Finance" }` zwróci wszystkie rekordy działu finansowego.

Oto dane powiązania w pliku *Function. JSON* :

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

W sekcji [Konfiguracja](#input---configuration) objaśniono te właściwości.

Oto kod języka Python:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

# <a name="javatabjava"></a>[Java](#tab/java)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz HTTP, wyszukiwanie identyfikatora na podstawie parametru ciągu zapytania](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania — parametr POJO](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy](#http-trigger-look-up-id-from-route-data-java)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy przy użyciu sqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [Wyzwalacz HTTP, pobieranie wielu dokumentów z danych trasy przy użyciu sqlQuery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

Przykłady odnoszą się do prostego typu `ToDoItem`:

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

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora na podstawie parametru ciągu zapytania

Poniższy przykład pokazuje funkcję języka Java, która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania, aby określić identyfikator i wartość klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania dokumentu z określonej bazy danych i kolekcji w postaci ciągu.

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

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj adnotacji `@CosmosDBInput` w przypadku parametrów funkcji, których wartość pochodzi z Cosmos DB.  Tej adnotacji można używać w przypadku natywnych typów Java, Pojo lub wartości null przy użyciu `Optional<T>`.

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania — parametr POJO

Poniższy przykład pokazuje funkcję języka Java, która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania, aby określić identyfikator i wartość klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji używane do pobierania dokumentu z określonej bazy danych i kolekcji. Dokument zostanie następnie przekonwertowany do instancji ```ToDoItem``` POJO poprzednio utworzone i przeszedł jako argument do funkcji.

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

### <a name="http-trigger-look-up-id-from-route-data"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy

Poniższy przykład pokazuje funkcję języka Java, która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które korzysta z parametru trasy, aby określić identyfikator i wartość klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania dokumentu z określonej bazy danych i kolekcji, zwracając ją jako ```Optional<String>```.

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

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy przy użyciu sqlQuery

Poniższy przykład pokazuje funkcję języka Java, która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa parametru trasy do określenia identyfikatora do wyszukania. Ten identyfikator jest używany do pobierania dokumentu z określonej bazy danych i kolekcji, co umożliwia przekonwertowanie zestawu wyników na ```ToDoItem[]```, ponieważ w zależności od kryteriów zapytania można zwrócić wiele dokumentów.

> [!NOTE]
> Jeśli zachodzi potrzeba przeszukiwania tylko identyfikatora, zaleca się użycie wyszukiwania, podobnie jak w [poprzednich przykładach](#http-trigger-look-up-id-from-query-string---pojo-parameter-java), ponieważ będzie zużywał mniej [jednostek żądania](../cosmos-db/request-units.md). Operacje odczytu punktu (GET) są [bardziej wydajne](../cosmos-db/optimize-cost-queries.md) niż zapytania według identyfikatora.
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

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>Wyzwalacz HTTP, pobieranie wielu dokumentów z danych trasy przy użyciu sqlQuery

Poniższy przykład pokazuje funkcję języka Java, która pobiera wiele dokumentów. Funkcja jest wyzwalana przez żądanie HTTP, które używa ```desc``` parametru trasy, aby określić ciąg do wyszukania w polu ```description```. Termin wyszukiwania służy do pobrania kolekcji dokumentów z określonej bazy danych i kolekcji, przekonwertowanie zestawu wyników na ```ToDoItem[]``` i przekazanie go jako argumentu do funkcji.

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

## <a name="input---attributes-and-annotations"></a>Input — atrybuty i adnotacje

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj atrybutu [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) .

Konstruktor atrybutu ma nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i innych właściwościach, które można skonfigurować, zobacz [następującą sekcję konfiguracyjną](#input---configuration).

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Atrybuty nie są obsługiwane przez C# skrypt.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="javatabjava"></a>[Java](#tab/java)

W [bibliotece środowiska uruchomieniowego usługi Java Functions](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)Użyj adnotacji `@CosmosDBOutput` dla parametrów, które zapisują w Cosmos DB. Typ parametru adnotacji powinien mieć wartość `OutputBinding<T>`, gdzie `T` jest natywnym typem Java lub POJO.

---

## <a name="input---configuration"></a>Dane wejściowe — Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* i `CosmosDB` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type**     | Nie dotyczy | musi być ustawiony na `cosmosDB`.        |
|**direction**     | Nie dotyczy | musi być ustawiony na `in`.         |
|**Nazwij**     | Nie dotyczy | Nazwa parametru powiązania, który reprezentuje dokument w funkcji.  |
|**Bazy** |**Bazy** |Baza danych zawiera dokument.        |
|**CollectionName** |**CollectionName** | Nazwa kolekcji, która zawiera dokument. |
|**#**    | **Identyfikator** | Identyfikator dokumentu do pobrania. Ta właściwość obsługuje [wyrażenia powiązań](./functions-bindings-expressions-patterns.md). Nie ustawiaj właściwości `id` i **sqlQuery** . Jeśli nie ustawisz pojedynczo, zostanie pobrana całą kolekcję. |
|**sqlQuery**  |**SqlQuery**  | Zapytania SQL usługi Azure Cosmos DB używane do pobierania wiele dokumentów. Właściwość obsługuje powiązania środowiska uruchomieniowego, jak w tym przykładzie: `SELECT * FROM c where c.departmentId = {departmentId}`. Nie ustawiaj właściwości `id` i `sqlQuery`. Jeśli nie ustawisz pojedynczo, zostanie pobrana całą kolekcję.|
|**connectionStringSetting**     |**ConnectionStringSetting**|Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi Azure Cosmos DB.        |
|**partitionKey**|**PartitionKey**|Określa wartość klucza partycji do wyszukiwania. Może zawierać parametrów wiązania. Jest to wymagane w przypadku wyszukiwań w kolekcjach [partycjonowanych](../cosmos-db/partition-data.md#logical-partitions) .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Dane wejściowe — użycie

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Gdy funkcja zostanie zakończona pomyślnie, wszelkie zmiany wprowadzone w dokumencie wejściowym za pośrednictwem nazwanych parametrów wejściowych są utrwalane automatycznie.

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Gdy funkcja zostanie zakończona pomyślnie, wszelkie zmiany wprowadzone w dokumencie wejściowym za pośrednictwem nazwanych parametrów wejściowych są utrwalane automatycznie.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Aktualizacje nie są wykonywane automatycznie po zamknięciu funkcji. Zamiast tego należy używać `context.bindings.<documentName>In` i `context.bindings.<documentName>Out` do aktualizowania. Zapoznaj się z przykładem JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Dane są udostępniane funkcji za pośrednictwem parametru `DocumentList`. Zmiany wprowadzone do dokumentu nie są automatycznie utrwalane.

# <a name="javatabjava"></a>[Java](#tab/java)

W [bibliotece środowiska uruchomieniowego usługi Java Functions](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)adnotacja [@CosmosDBInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) uwidacznia Cosmos DB dane do funkcji. Tej adnotacji można używać w przypadku natywnych typów Java, Pojo lub wartości null przy użyciu `Optional<T>`.

---

## <a name="output"></a>Dane wyjściowe

Dane wyjściowe usługi Azure Cosmos DB, powiązania pozwala zapisać nowy dokument z bazą danych Azure Cosmos DB przy użyciu interfejsu API SQL.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz kolejki, napisz jeden dokument](#queue-trigger-write-one-doc-c)
* [Wyzwalacz kolejki, Zapisz dokumenty przy użyciu IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c)

Przykłady odnoszą się do prostego typu `ToDoItem`:

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

### <a name="queue-trigger-write-one-doc"></a>Wyzwalacz kolejki, doc jeden zapisu

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która dodaje dokument do bazy danych przy użyciu danych z usługi queue storage.

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wyzwalacz kolejki, dokumentacja zapisu przy użyciu IAsyncCollector

W poniższym przykładzie pokazano [ C# funkcję](functions-dotnet-class-library.md) , która dodaje kolekcję dokumentów do bazy danych przy użyciu danych udostępnionych w formacie JSON komunikatu w kolejce.

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

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz kolejki, napisz jeden dokument](#queue-trigger-write-one-doc-c-script)
* [Wyzwalacz kolejki, Zapisz dokumenty przy użyciu IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>Wyzwalacz kolejki, doc jeden zapisu

Poniższy przykład pokazuje Azure Cosmos DB powiązanie danych wyjściowych w pliku *Function. JSON* i [ C# funkcji skryptu](functions-reference-csharp.md) , która używa powiązania. Funkcja używa powiązania danych wejściowych w kolejce dla kolejki, która odbiera JSON w następującym formacie:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funkcja tworzy dokumentów usługi Azure Cosmos DB w następującym formacie, dla każdego rekordu:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Oto dane powiązania w pliku *Function. JSON* :

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

W sekcji [Konfiguracja](#output---configuration) objaśniono te właściwości.

Poniżej przedstawiono kod skryptu języka C#:

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wyzwalacz kolejki, dokumentacja zapisu przy użyciu IAsyncCollector

Aby utworzyć wiele dokumentów, można powiązać z `ICollector<T>` lub `IAsyncCollector<T>`, gdzie `T` jest jednym z obsługiwanych typów.

Ten przykład dotyczy prostego typu `ToDoItem`:

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

Poniżej przedstawiono plik function.json:

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

Poniżej przedstawiono kod skryptu języka C#:

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład pokazuje Azure Cosmos DB powiązanie danych wyjściowych w pliku *Function. JSON* i [funkcję języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja używa powiązania danych wejściowych w kolejce dla kolejki, która odbiera JSON w następującym formacie:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funkcja tworzy dokumentów usługi Azure Cosmos DB w następującym formacie, dla każdego rekordu:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Oto dane powiązania w pliku *Function. JSON* :

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

W sekcji [Konfiguracja](#output---configuration) objaśniono te właściwości.

Poniżej przedstawiono kod JavaScript:

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

# <a name="pythontabpython"></a>[Python](#tab/python)

W poniższym przykładzie pokazano, jak napisać dokument do bazy danych usługi Azure CosmosDB jako dane wyjściowe funkcji.

Definicja powiązania jest zdefiniowana w *funkcji Function. JSON* , w której *typ* ma wartość `cosmosDB`.

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

Aby zapisać dane w bazie danych, przekaż obiekt dokumentu do metody `set` parametru Database.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

# <a name="javatabjava"></a>[Java](#tab/java)

* [Wyzwalacz kolejki, Zapisz komunikat do bazy danych za pośrednictwem wartości zwracanej](#queue-trigger-save-message-to-database-via-return-value-java)
* [Wyzwalacz HTTP, Zapisz jeden dokument do bazy danych za pośrednictwem wartości zwracanej](#http-trigger-save-one-document-to-database-via-return-value-java)
* [Wyzwalacz HTTP, Zapisz jeden dokument do bazy danych za pośrednictwem kodu Wyjściowegobinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [Wyzwalacz HTTP, zapisanie wielu dokumentów do bazy danych za pośrednictwem kodu Wyjściowegobinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>Wyzwalacz kolejki, Zapisz komunikat do bazy danych za pośrednictwem wartości zwracanej

Poniższy przykład pokazuje funkcję języka Java, która dodaje dokument do bazy danych z danymi z wiadomości w usłudze queue storage.

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

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>Wyzwalacz HTTP, Zapisz jeden dokument do bazy danych za pośrednictwem wartości zwracanej

Poniższy przykład pokazuje funkcję języka Java, której podpis jest oznaczony ```@CosmosDBOutput``` i ma zwracaną wartość typu ```String```. Dokument JSON zwrócony przez funkcję zostanie automatycznie zapisany w odpowiedniej kolekcji CosmosDB.

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

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>Wyzwalacz HTTP, Zapisz jeden dokument do bazy danych za pośrednictwem kodu Wyjściowegobinding

Poniższy przykład pokazuje funkcję języka Java, która zapisuje dokument do CosmosDB za pośrednictwem parametru wyjściowego ```OutputBinding<T>```. W tym przykładzie do parametru ```outputItem``` należy dodać adnotację przy użyciu ```@CosmosDBOutput```, a nie sygnatury funkcji. Korzystanie z ```OutputBinding<T>``` umożliwia korzystanie z funkcji powiązania w celu zapisania dokumentu w CosmosDB, a jednocześnie pozwala zwrócić inną wartość do obiektu wywołującego funkcji, takiego jak dokument JSON lub XML.

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

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>Wyzwalacz HTTP, zapisanie wielu dokumentów do bazy danych za pośrednictwem kodu Wyjściowegobinding

Poniższy przykład pokazuje funkcję języka Java, która zapisuje wiele dokumentów do CosmosDB za pośrednictwem parametru wyjściowego ```OutputBinding<T>```. W tym przykładzie parametr ```outputItem``` jest oznaczony przy użyciu ```@CosmosDBOutput```, a nie sygnatury funkcji. Parametr wyjściowy, ```outputItem``` ma listę obiektów ```ToDoItem``` jako typ parametru szablonu. Używanie ```OutputBinding<T>``` umożliwia korzystanie z funkcji powiązania w celu zapisywania dokumentów w CosmosDB, a jednocześnie pozwala zwrócić inną wartość do obiektu wywołującego funkcji, takiego jak dokument JSON lub XML.

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

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj adnotacji `@CosmosDBOutput` w parametrach, które będą zapisywane w Cosmos DB.  Typ parametru adnotacji powinien mieć wartość ```OutputBinding<T>```, gdzie T jest natywnym typem Java lub POJO.

---

## <a name="output---attributes-and-annotations"></a>Dane wyjściowe — atrybuty i adnotacje

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj atrybutu [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) .

Konstruktor atrybutu ma nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i innych właściwościach, które można skonfigurować, zobacz [wyjście-konfiguracja](#output---configuration). Oto przykład atrybutu `CosmosDB` w sygnaturze metody:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Atrybuty nie są obsługiwane przez C# skrypt.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Adnotacja `CosmosDBOutput` jest dostępna do zapisywania danych do Cosmos DB. Adnotację można zastosować do funkcji lub do poszczególnych parametrów funkcji. W przypadku użycia na metodzie funkcji zwracana wartość funkcji jest zapisywana w Cosmos DB. Jeśli używasz adnotacji z parametrem, typ parametru musi być zadeklarowany jako `OutputBinding<T>`, gdzie `T` natywny typ Java lub POJO.

---

## <a name="output---configuration"></a>Dane wyjściowe — Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* i `CosmosDB` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type**     | Nie dotyczy | musi być ustawiony na `cosmosDB`.        |
|**direction**     | Nie dotyczy | musi być ustawiony na `out`.         |
|**Nazwij**     | Nie dotyczy | Nazwa parametru powiązania, który reprezentuje dokument w funkcji.  |
|**Bazy** | **Bazy**|Baza danych, zawierający kolekcję, w którym zostanie utworzona dokumentu.     |
|**CollectionName** |**CollectionName**  | Nazwa kolekcji jest tworzona dokumentu. |
|**Metodę createifnotexists**  |**Metodę createifnotexists**    | Wartość logiczna, aby wskazać, czy kolekcja jest tworzona, gdy nie istnieje. Wartość domyślna to *false* , ponieważ nowe kolekcje są tworzone z zarezerwowaną przepływność, która ma wpływ na koszty. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey**|**PartitionKey** |Gdy `CreateIfNotExists` ma wartość true, definiuje ścieżkę klucza partycji dla utworzonej kolekcji.|
|**collectionThroughput**|**CollectionThroughput**| Gdy `CreateIfNotExists` ma wartość true, definiuje [przepływność](../cosmos-db/set-throughput.md) utworzonej kolekcji.|
|**connectionStringSetting**    |**ConnectionStringSetting** |Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

Domyślnie podczas zapisu do parametru wyjściowego w funkcji, tworzony jest dokument w bazie danych. Ten dokument ma automatycznie generowanym identyfikatorze GUID jako identyfikatora dokumentu. Możesz określić identyfikator dokumentu dla dokumentu wyjściowego, określając właściwość `id` w obiekcie JSON przekazaną do parametru Output.

> [!Note]
> Po określeniu identyfikator istniejącego dokumentu, pobiera to zastąpione przez nowy dokument danych wyjściowych.

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie | Dokumentacja |
|---|---|
| CosmosDB | [Kody błędów CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>Ustawienia host.JSON

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersji 2.x. Aby uzyskać więcej informacji na temat ustawień konfiguracji globalnej w wersji 2. x, zobacz informacje dotyczące pliku [host. JSON dla Azure Functions wersji 2. x](functions-host-json.md).

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
|GatewayMode|Brama|Tryb połączenia używany przez funkcję podczas nawiązywania połączenia z usługą Azure Cosmos DB. Opcje są `Direct` i `Gateway`|
|Protokół|Schemat|Protokół połączenia używany przez funkcję podczas nawiązywania połączenia z usługą Azure Cosmos DB.  Przeczytaj [tutaj, aby uzyskać wyjaśnienie obu trybów](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|Nie dotyczy|Prefiks dzierżawy do użycia we wszystkich funkcjach w aplikacji.|

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o liczbie bezserwerowych baz danych z Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Dowiedz się więcej o wyzwalaczach i powiązaniach usługi Azure Functions](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
