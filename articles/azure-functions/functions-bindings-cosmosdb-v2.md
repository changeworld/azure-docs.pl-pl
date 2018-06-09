---
title: Azure DB rozwiązania Cosmos powiązania dla funkcji 2.x (wersja zapoznawcza)
description: Zrozumienie sposobu korzystania z bazy danych Azure rozwiązania Cosmos wyzwalaczy i powiązań w funkcji platformy Azure.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: funkcje usługi Azure, funkcje, przetwarzania zdarzeń, dynamiczne obliczeń niekorzystającą architektury
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: tdykstra
ms.openlocfilehash: e40ba6bcfa1b6247f62849626d4a7803a76362a1
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234873"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x-preview"></a>Azure DB rozwiązania Cosmos powiązania dla usługi Azure Functions 2.x (wersja zapoznawcza)

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Wersja 1 — ogólnie dostępna](functions-bindings-cosmosdb.md)
> * [Wersja 2 — wersja zapoznawcza](functions-bindings-cosmosdb-v2.md)

W tym artykule opisano sposób pracy z [bazy danych Azure rozwiązania Cosmos](..\cosmos-db\serverless-computing-database.md) powiązania usługi Azure Functions 2.x. Usługi Azure Functions obsługuje wyzwolenia, danych wejściowych i wyjściowych powiązania dla bazy danych Azure rozwiązania Cosmos.

> [!NOTE]
> Ten artykuł dotyczy [wersji usługi Azure Functions 2.x](functions-versions.md), która jest w wersji zapoznawczej.  Aby uzyskać informacje o sposobie używania tych powiązań w funkcjach 1.x, zobacz [bazy danych Azure rozwiązania Cosmos powiązania dla usługi Azure Functions 1.x](functions-bindings-cosmosdb.md).
>
> To powiązanie pierwotnie nosiła nazwę usługi DocumentDB. W wersji funkcji 2.x wyzwalacza, powiązania i pakietu są wszystkie o nazwie DB rozwiązania Cosmos.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania bazy danych Azure rozwiązania Cosmos funkcje wersji 2.x znajdują się w [Microsoft.Azure.WebJobs.Extensions.CosmosDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) pakietu NuGet w wersji 3.x. Kod źródłowy dla powiązania jest w [azure-zadań webjob sdk rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) repozytorium GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Wyzwalacz

Wyzwalacz DB rozwiązania Cosmos Azure wykorzystuje [Azure rozwiązania Cosmos DB zmiany źródła danych](../cosmos-db/change-feed.md) do nasłuchiwania wstawia i aktualizacje na partycje. Zmiana źródła strumieniowego publikuje wstawia i aktualizacji i usunięć nie.

## <a name="trigger---example"></a>Wyzwalacz — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [C#](#trigger---c-example)
* [Skryptu C# (csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

[Pomiń przykłady wyzwalacza](#trigger---attributes)

### <a name="trigger---c-example"></a>Wyzwalacz — przykład C#

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) który wywoływane, gdy istnieją wstawia lub aktualizuje bazę danych i kolekcji.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

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
            TraceWriter log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.Info($"Documents modified: {documents.Count}");
                log.Info($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

[Pomiń przykłady wyzwalacza](#trigger---attributes)

### <a name="trigger---c-script-example"></a>Wyzwalacz — przykładowy skrypt w języku C#

W poniższym przykładzie przedstawiono wyzwalacz DB rozwiązania Cosmos powiązanie w *function.json* pliku i [funkcji skryptu C#](functions-reference-csharp.md) używającą powiązania. Funkcja zapisuje komunikaty dziennika po zmodyfikowaniu rekordów DB rozwiązania Cosmos.

W tym miejscu jest powiązanie danych *function.json* pliku:

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

Oto kod skryptu C#:
 
```cs 
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
      log.Verbose("Documents modified " + documents.Count);
      log.Verbose("First document Id " + documents[0].Id);
    }
```

[Pomiń przykłady wyzwalacza](#trigger---attributes)

### <a name="trigger---javascript-example"></a>Wyzwalacz — przykład JavaScript

W poniższym przykładzie przedstawiono wyzwalacz DB rozwiązania Cosmos powiązanie w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) używającą powiązania. Funkcja zapisuje komunikaty dziennika po zmodyfikowaniu rekordów DB rozwiązania Cosmos.

W tym miejscu jest powiązanie danych *function.json* pliku:

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

Oto kod JavaScript:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

## <a name="trigger---attributes"></a>Wyzwalacz — atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) atrybutu.

Konstruktor atrybutu ma nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i inne właściwości, które można skonfigurować, zobacz [wyzwalacza - konfiguracji](#trigger---configuration). Oto `CosmosDBTrigger` przykład atrybutu w podpisie metody:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

Pełny przykład, zobacz [wyzwalacza — przykład C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Wyzwalacz — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `CosmosDBTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** || należy wybrać opcję `cosmosDBTrigger`. |
|**direction** || należy wybrać opcję `in`. Ten parametr jest ustawiany automatycznie, podczas tworzenia wyzwalacza w portalu Azure. |
|**Nazwa** || Nazwa zmiennej używany w funkcji kod, który reprezentuje listy dokumentów o zmiany. | 
|**connectionStringSetting**|**ConnectionStringSetting** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia używane do nawiązania połączenia konta bazy danych Azure rozwiązania Cosmos monitorowane. |
|**databaseName**|**DatabaseName**  | Nazwa bazy danych DB rozwiązania Cosmos Azure z tą kolekcją monitorowane. |
|**CollectionName** |**CollectionName** | Nazwa kolekcji monitorowane. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Opcjonalnie) Nazwa ustawienia aplikacji, które zawiera parametry połączenia do usługi, która posiada kolekcji dzierżawy. Gdy nie są ustawione, `connectionStringSetting` wartość jest używana. Ten parametr ma wartość automatycznie, podczas tworzenia powiązania w portalu. Parametry połączenia dla kolekcji dzierżawy musi mieć uprawnienia do zapisu.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Opcjonalnie) Nazwa bazy danych, która przechowuje kolekcji używany do przechowywania dzierżawy. Gdy nie są ustawione, wartość `databaseName` ustawienie jest używane. Ten parametr ma wartość automatycznie, podczas tworzenia powiązania w portalu. |
|**leaseCollectionName** | **LeaseCollectionName** | (Opcjonalnie) Nazwa kolekcji używany do przechowywania dzierżawy. Gdy nie są ustawione, wartość `leases` jest używany. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Opcjonalnie) Jeśli wartość `true`, kolekcji dzierżawy jest tworzony automatycznie podczas już nie istnieje. Wartość domyślna to `false`. |
|**LeasesCollectionThroughput**| **LeasesCollectionThroughput**| (Opcjonalnie) Definiuje liczbę jednostek żądań można przypisać podczas tworzenia kolekcji dzierżawy. To ustawienie jest tylko do użycia podczas `createLeaseCollectionIfNotExists` ma ustawioną wartość `true`. Ten parametr ma wartość automatycznie, podczas tworzenia powiązania za pomocą portalu.
|**LeaseCollectionPrefix**| **LeaseCollectionPrefix**| (Opcjonalnie) Po ustawieniu dodaje prefiksu do dzierżawy utworzone w kolekcji dzierżawy dla tej funkcji, efektywnie stosowanie dwóch osobnych funkcji Azure na współużytkowanie tej samej kolekcji dzierżawy przy użyciu różnych prefiksy.
|**FeedPollDelay**| **FeedPollDelay**| (Opcjonalnie) Gdy zestawu, które definiuje, w milisekundach, opóźnienie pomiędzy sondowania partycji dla nowych zmian w źródle danych są opróżnione po wszystkich bieżących zmian. Domyślna to 5000 (5 sekund).
|**LeaseAcquireInterval**| **LeaseAcquireInterval**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał, aby rozpocząć wyłączyć zadania do obliczenia, jeśli partycje są rozmieszczone równomiernie wystąpień znane hosta. Domyślnie jest 13000 (w sekundach 13).
|**LeaseExpirationInterval**| **LeaseExpirationInterval**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał, dla którego podjęto dzierżawy na dzierżawę reprezentujący partycji. Jeśli dzierżawa nie zostanie odnowiony w tym przedziale czasu, spowoduje jego wygaśnięcia i własność partycji przejdzie do innego wystąpienia. Domyślnie jest 60000 (60 sekund).
|**LeaseRenewInterval**| **LeaseRenewInterval**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwału odnawiania dla wszystkich dzierżaw dla partycji aktualnie utrzymywane przez wystąpienie. Domyślnie jest 17000 (17 w sekundach).
|**CheckpointFrequency**| **CheckpointFrequency**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał między punktami kontrolnymi dzierżawy. Domyślnie jest zawsze po pomyślnym wywołaniem funkcji.
|**MaxItemsPerInvocation**| **MaxItemsPerInvocation**| (Opcjonalnie) Po ustawieniu dostosowuje maksymalna ilość elementów odebranych na wywołanie funkcji.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Wyzwalacz — użycie

Druga kolekcja, która jest używana do przechowywania wymaga wyzwalacza _dzierżawy_ na partycje. Zarówno kolekcji monitorowane i kolekcji, która zawiera dzierżawy musi być dostępny dla wyzwalacza do pracy.

>[!IMPORTANT]
> Jeśli wiele funkcji są skonfigurowane do używania wyzwalacz rozwiązania Cosmos bazy danych dla tej samej kolekcji, każdej funkcji należy użyć kolekcji dedykowanym dzierżawy lub określić inną `LeaseCollectionPrefix` dla każdej funkcji. W przeciwnym razie tylko jeden funkcji zostanie wyzwolone. Aby uzyskać informacje o prefiks, zobacz [sekcji konfiguracji](#trigger---configuration).

Wyzwalacz nie wskazują, czy zaktualizowane lub wstawić dokumentu, tylko zawiera samego dokumentu. Jeśli wymagana jest obsługa aktualizacji i wstawia inaczej, możesz to zrobić z zastosowaniem pola sygnatury czasowej do wstawienia lub aktualizacji.

## <a name="input"></a>Dane wejściowe

Powiązania wejściowego bazy danych Azure rozwiązania Cosmos pobiera jeden lub więcej dokumentów bazy danych Azure rozwiązania Cosmos i przekazuje je do parametr wejściowy funkcji. Można określić parametry identyfikator lub kwerendę dokumentu oparte na wyzwalacz, który wywołuje funkcję. 

>[!NOTE]
> Nie używasz bazy danych Azure rozwiązania Cosmos w danych wejściowych lub wyjściowych powiązań, jeśli używasz bazy danych MongoDB interfejsu API na koncie DB rozwiązania Cosmos. Możliwe jest uszkodzenia danych.

## <a name="input---examples"></a>Dane wejściowe — przykłady

Przykłady specyficzne dla języka, które zapoznały pojedynczego dokumentu, określając wartość Identyfikatora:

* [C#](#input---c-examples)
* [Skryptu C# (csx)](#input---c-script-examples)
* [JavaScript](#input---javascript-examples)
* [F#](#input---f-examples)

[Pomiń przykłady wejściowych](#input---attributes)

### <a name="input---c-examples"></a>Dane wejściowe — przykłady w języku C#

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz kolejki, Sprawdź identyfikator JSON](#queue-trigger-look-up-id-from-json-c)
* [Wyzwalacz protokołu HTTP, wyszukiwanie identyfikator ciągu zapytania](#http-trigger-look-up-id-from-query-string-c)
* [Wyzwalacz protokołu HTTP, wyszukiwanie identyfikator dane trasy](#http-trigger-look-up-id-from-route-data-c)
* [Wyzwalacz protokołu HTTP, wyszukiwanie identyfikator danych trasy, używając SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP wyzwalania, Pobierz wielu dokumentów, przy użyciu SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP wyzwalania, Pobierz wielu dokumentów, przy użyciu DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Przykłady dotyczą prostą `ToDoItem` typu:

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

[Pomiń przykłady wejściowych](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-c"></a>Wyzwalacz kolejki, Sprawdź identyfikator JSON (C#)

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) , który pobiera pojedynczego dokumentu. Funkcja jest wyzwalany przez komunikat z kolejki, która zawiera obiekt JSON. Wyzwalacz kolejki analizuje dane JSON do obiektu o nazwie `ToDoItemLookup`, który zawiera identyfikator do wyszukania. Czy identyfikator jest używany do pobierania `ToDoItem` dokument z określoną bazą danych i kolekcji.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;

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
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

[Pomiń przykłady wejściowych](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c"></a>Wyzwalacz protokołu HTTP, wyszukiwanie identyfikator ciągu zapytania (C#)

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) , który pobiera pojedynczego dokumentu. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania do określenia Identyfikatora do wyszukiwania. Czy identyfikator jest używany do pobierania `ToDoItem` dokument z określoną bazą danych i kolekcji.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;

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
                Id = "{Query.id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

[Pomiń przykłady wejściowych](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c"></a>Wyzwalacz protokołu HTTP, wyszukiwanie identyfikator danych trasy (C#)

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) , który pobiera pojedynczego dokumentu. Funkcja jest wyzwalana przez żądanie HTTP, że używa przekazywanie danych do określenia Identyfikatora do wyszukiwania. Czy identyfikator jest używany do pobierania `ToDoItem` dokument z określoną bazą danych i kolekcji.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", 
                Route = "todoitems/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection", 
                Id = "{id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

[Pomiń przykłady wejściowych](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>Wyzwalacz protokołu HTTP, wyszukiwanie identyfikator danych trasy, używając SqlQuery (C#)

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) , który pobiera pojedynczego dokumentu. Funkcja jest wyzwalana przez żądanie HTTP, że używa przekazywanie danych do określenia Identyfikatora do wyszukiwania. Czy identyfikator jest używany do pobierania `ToDoItem` dokument z określoną bazą danych i kolekcji. 

W przykładzie pokazano, jak używać wyrażenia powiązania w `SqlQuery` parametru. Można przekazać dane trasy do `SqlQuery` parametru, co zostało pokazane, ale obecnie [nie można przekazać wartości ciągu zapytania](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).


```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

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
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

[Pomiń przykłady wejściowych](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c"></a>HTTP wyzwalania, Pobierz wielu dokumentów, przy użyciu SqlQuery (C#)

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) , który pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Zapytanie jest określona w `SqlQuery` właściwością atrybutu.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

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
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

[Pomiń przykłady wejściowych](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP wyzwalania, Pobierz wielu dokumentów, przy użyciu DocumentClient (C#)

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) , który pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. W kodzie użyto `DocumentClient` wystąpienia przez powiązanie bazy danych Azure rozwiązania Cosmos można odczytać listy dokumentów. `DocumentClient` Wystąpienia może także służyć do operacji zapisu.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
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
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.Info($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.Info(result.Description);
                }
            }
            return new OkResult();
        }
    }
}
```

[Pomiń przykłady wejściowych](#input---attributes)

### <a name="input---c-script-examples"></a>Dane wejściowe — przykłady skryptów w języku C#

Ta sekcja zawiera następujące przykłady, które zapoznały pojedynczego dokumentu, określając wartość Identyfikatora z różnych źródeł:

* Wyzwalacz kolejki, wyszukiwanie identyfikator komunikatu w kolejce
* Wyzwalacz kolejki, wyszukiwanie identyfikator komunikatu w kolejce, za pomocą SqlQuery

[Pomiń przykłady wejściowych](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-queue-message-c-script"></a>Wyzwalacz kolejki, wyszukiwanie identyfikator komunikatu w kolejce (skryptu C#)

W poniższym przykładzie przedstawiono powiązania wejściowego DB rozwiązania Cosmos w *function.json* pliku i [funkcji skryptu C#](functions-reference-csharp.md) używającą powiązania. Funkcja odczytuje pojedynczego dokumentu i aktualizuje wartości tekstowej dokumentu.

W tym miejscu jest powiązanie danych *function.json* pliku:

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
[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Oto kod skryptu C#:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding 
    public static void Run(string myQueueItem, dynamic inputDocument)
    {   
      inputDocument.text = "This has changed.";
    }
```

[Pomiń przykłady wejściowych](#input---attributes)

#### <a name="queue-trigger-look-up-id-queue-message-using-sqlquery-c-script"></a>Wyzwalacz kolejki, wyszukiwanie komunikatu w kolejce ID, za pomocą SqlQuery (skryptu C#)

W poniższym przykładzie przedstawiono powiązania wejściowego bazy danych Azure rozwiązania Cosmos w *function.json* pliku i [funkcji skryptu C#](functions-reference-csharp.md) używającą powiązania. Funkcja pobiera wielu dokumentów określonych przez zapytanie SQL, aby dostosować parametry zapytania przy użyciu wyzwalacza kolejki.

Wyzwalacz kolejki zawiera parametr `departmentId`. Komunikat z kolejki z `{ "departmentId" : "Finance" }` zwróci wszystkie rekordy dotyczące działu finansowego. 

W tym miejscu jest powiązanie danych *function.json* pliku:

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

[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Oto kod skryptu C#:

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

[Pomiń przykłady wejściowych](#input---attributes)

### <a name="input---javascript-examples"></a>Dane wejściowe — przykłady kodu JavaScript

Ta sekcja zawiera następujące przykłady, które zapoznały pojedynczego dokumentu, określając wartość Identyfikatora z różnych źródeł:

* Wyzwalacz kolejki, wyszukiwanie identyfikator komunikatu w kolejce
* Wyzwalacz kolejki, wyszukiwanie identyfikator komunikatu w kolejce, za pomocą SqlQuery

[Pomiń przykłady wejściowych](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-queue-message-javascript"></a>Wyzwalacz kolejki, wyszukiwanie identyfikator komunikatu w kolejce (JavaScript)

W poniższym przykładzie przedstawiono powiązania wejściowego DB rozwiązania Cosmos w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) używającą powiązania. Funkcja odczytuje pojedynczego dokumentu i aktualizuje wartości tekstowej dokumentu.

W tym miejscu jest powiązanie danych *function.json* pliku:

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
[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Oto kod JavaScript:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {   
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

[Pomiń przykłady wejściowych](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-queue-message-using-sqlquery-javascript"></a>Wyzwalacz kolejki, wyszukiwanie identyfikator komunikatu w kolejce, za pomocą SqlQuery (JavaScript)

W poniższym przykładzie przedstawiono powiązania wejściowego bazy danych Azure rozwiązania Cosmos w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) używającą powiązania. Funkcja pobiera wielu dokumentów określonych przez zapytanie SQL, aby dostosować parametry zapytania przy użyciu wyzwalacza kolejki.

Wyzwalacz kolejki zawiera parametr `departmentId`. Komunikat z kolejki z `{ "departmentId" : "Finance" }` zwróci wszystkie rekordy dotyczące działu finansowego. 

W tym miejscu jest powiązanie danych *function.json* pliku:

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

[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

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

[Pomiń przykłady wejściowych](#input---attributes)

<a name="infsharp"></a>

### <a name="input---f-examples"></a>Dane wejściowe — przykłady F #

W poniższym przykładzie przedstawiono powiązania wejściowego DB rozwiązania Cosmos w *function.json* pliku i [F # funkcja](functions-reference-fsharp.md) używającą powiązania. Funkcja odczytuje pojedynczego dokumentu i aktualizuje wartości tekstowej dokumentu.

W tym miejscu jest powiązanie danych *function.json* pliku:

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connectionStringSetting": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```

[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Oto kod F #:

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

W tym przykładzie wymaga `project.json` pliku, który określa `FSharp.Interop.Dynamic` i `Dynamitey` zależności NuGet:

```json
{
    "frameworks": {
        "net46": {
            "dependencies": {
                "Dynamitey": "1.0.2",
                "FSharp.Interop.Dynamic": "3.0.0"
            }
        }
    }
}
```

Aby dodać `project.json` plików, zobacz [zarządzania pakietami F #](functions-reference-fsharp.md#package).

## <a name="input---attributes"></a>Dane wejściowe — atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) atrybutu.

Konstruktor atrybutu ma nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i inne właściwości, które można skonfigurować, zobacz [następującą sekcję konfiguracji](#input---configuration). 

## <a name="input---configuration"></a>Dane wejściowe — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `CosmosDB` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type**     || należy wybrać opcję `cosmosDB`.        |
|**direction**     || należy wybrać opcję `in`.         |
|**Nazwa**     || Nazwa parametru powiązania, który reprezentuje dokument w funkcji.  |
|**databaseName** |**DatabaseName** |Baza danych zawierająca dokumentu.        |
|**CollectionName** |**CollectionName** | Nazwa kolekcji, która zawiera dokument. |
|**id**    | **Identyfikator** | Identyfikator dokumentu do pobrania. Ta właściwość obsługuje [wyrażenia powiązania](functions-triggers-bindings.md#binding-expressions-and-patterns). Nie należy ustawiać zarówno **identyfikator** i **sqlQuery** właściwości. Jeśli nie zostanie ustawiona jedną, są pobierane całą kolekcję. |
|**sqlQuery**  |**SqlQuery**  | Zapytanie Azure rozwiązania Cosmos bazy danych SQL, używane do pobierania wielu dokumentów. Właściwość obsługuje powiązań czasu wykonywania, jak w poniższym przykładzie: `SELECT * FROM c where c.departmentId = {departmentId}`. Nie należy ustawiać zarówno **identyfikator** i **sqlQuery** właściwości. Jeśli nie zostanie ustawiona jedną, są pobierane całą kolekcję.|
|**connectionStringSetting**     |**ConnectionStringSetting**|Nazwa ustawienia aplikacji zawierający parametry połączenia bazy danych Azure rozwiązania Cosmos.        |
|**PartitionKey**|**PartitionKey**|Określa wartość klucza partycji do wyszukiwania. Mogą zawierać parametrów wiązania.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Dane wejściowe — użycie

W języku C# i F # funkcjach gdy funkcja kończy się pomyślnie, wszelkie zmiany wprowadzone do dokument wejściowy za pośrednictwem nazwanych parametrów wejściowych automatycznie są zachowywane. 

W funkcji JavaScript aktualizacje nie będą automatycznie po wyjściu z funkcji. Zamiast tego należy użyć `context.bindings.<documentName>In` i `context.bindings.<documentName>Out` aby aktualizacje. Zobacz [przykład JavaScript](#input---javascript-example).

## <a name="output"></a>Dane wyjściowe

Dane wyjściowe bazy danych Azure rozwiązania Cosmos powiązanie umożliwia nowy dokument zapisu do bazy danych Azure DB rozwiązania Cosmos. 

>[!NOTE]
> Nie używasz bazy danych Azure rozwiązania Cosmos w danych wejściowych lub wyjściowych powiązań, jeśli używasz bazy danych MongoDB interfejsu API na koncie DB rozwiązania Cosmos. Możliwe jest uszkodzenia danych.

## <a name="output---example"></a>OUTPUT — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [C#](#output---c-examples)
* [Skryptu C# (csx)](#output---c-script-examples)
* [JavaScript](#output---javascript-examples)
* [F#](#output---f-examples)

Zobacz też [przykład wejściowych](#input---c-examples) używającą `DocumentClient`.

[Pomiń przykładowe dane wyjściowe](#output---attributes)

### <a name="ouput---c-examples"></a>Dane wyjściowe — przykłady w języku C#

Ta sekcja zawiera następujące przykłady:

* Wyzwalacz kolejki, doc jednego zapisu
* Wyzwalacz kolejki, przy użyciu IAsyncCollector docs zapisu

Przykłady dotyczą prostą `ToDoItem` typu:

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

[Pomiń przykładowe dane wyjściowe](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c"></a>Wyzwalacz kolejki, doc jednego zapisu (C#)

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) dodaje dokumentu do bazy danych, przy użyciu danych zawartych w wiadomości z kolejki magazynu.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
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
            TraceWriter log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.Info($"C# Queue trigger function inserted one row");
            log.Info($"Description={queueMessage}");
        }
    }
}
```

[Pomiń przykładowe dane wyjściowe](#output---attributes)

#### <a name="queue-trigger-write-docs-using-iasynccollector-c"></a>Wyzwalacz kolejki, dokumentów zapisu przy użyciu IAsyncCollector (C#)

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) dodaje kolekcję dokumentów do bazy danych, przy użyciu danych podane w komunikacie kolejki JSON.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;

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
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.Info($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

[Pomiń przykładowe dane wyjściowe](#output---attributes)

### <a name="output---c-script-examples"></a>Dane wyjściowe — przykłady skryptów w języku C#

W poniższym przykładzie przedstawiono dane wyjściowe z bazy danych Azure rozwiązania Cosmos powiązanie w *function.json* pliku i [funkcji skryptu C#](functions-reference-csharp.md) używającą powiązania. Funkcja używa powiązania wejściowego kolejki dla kolejki, który odbiera JSON w następującym formacie:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funkcja ta umożliwia tworzenie dokumentów bazy danych Azure rozwiązania Cosmos w następującym formacie, dla każdego rekordu:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

W tym miejscu jest powiązanie danych *function.json* pliku:

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

[Konfiguracji](#output---configuration) sekcji opisano te właściwości.

Oto kod skryptu C#:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
      log.Info($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

Aby utworzyć wiele dokumentów, można powiązać `ICollector<T>` lub `IAsyncCollector<T>` gdzie `T` jest jednym z obsługiwanych typów.

[Pomiń przykładowe dane wyjściowe](#output---attributes)

### <a name="output---javascript-examples"></a>Dane wyjściowe — przykłady kodu JavaScript

W poniższym przykładzie przedstawiono dane wyjściowe z bazy danych Azure rozwiązania Cosmos powiązanie w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) używającą powiązania. Funkcja używa powiązania wejściowego kolejki dla kolejki, który odbiera JSON w następującym formacie:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funkcja ta umożliwia tworzenie dokumentów bazy danych Azure rozwiązania Cosmos w następującym formacie, dla każdego rekordu:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

W tym miejscu jest powiązanie danych *function.json* pliku:

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

[Konfiguracji](#output---configuration) sekcji opisano te właściwości.

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

[Pomiń przykładowe dane wyjściowe](#output---attributes)

### <a name="output---f-examples"></a>Dane wyjściowe — przykłady F #

W poniższym przykładzie przedstawiono dane wyjściowe z bazy danych Azure rozwiązania Cosmos powiązanie w *function.json* pliku i [F # funkcja](functions-reference-fsharp.md) używającą powiązania. Funkcja używa powiązania wejściowego kolejki dla kolejki, który odbiera JSON w następującym formacie:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funkcja ta umożliwia tworzenie dokumentów bazy danych Azure rozwiązania Cosmos w następującym formacie, dla każdego rekordu:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

W tym miejscu jest powiązanie danych *function.json* pliku:

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
[Konfiguracji](#output---configuration) sekcji opisano te właściwości.

Oto kod F #:

```fsharp
    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
      id: string
      name: string
      employeeId: string
      address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
      log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
      let employee = JObject.Parse(myQueueItem)
      employeeDocument <-
        { id = sprintf "%s-%s" employee?name employee?employeeId
          name = employee?name
          employeeId = employee?employeeId
          address = employee?address }
```

W tym przykładzie wymaga `project.json` pliku, który określa `FSharp.Interop.Dynamic` i `Dynamitey` zależności NuGet:

```json
{
    "frameworks": {
        "net46": {
          "dependencies": {
            "Dynamitey": "1.0.2",
            "FSharp.Interop.Dynamic": "3.0.0"
           }
        }
    }
}
```

Aby dodać `project.json` plików, zobacz [zarządzania pakietami F #](functions-reference-fsharp.md#package).

## <a name="output---attributes"></a>Dane wyjściowe — atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) atrybutu.

Konstruktor atrybutu ma nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i inne właściwości, które można skonfigurować, zobacz [wyjście - konfiguracji](#output---configuration). Oto `CosmosDB` przykład atrybutu w podpisie metody:

```csharp
    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Pełny przykład, zobacz [dane wyjściowe — przykład C#](#output---c-example).

## <a name="output---configuration"></a>OUTPUT — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `CosmosDB` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type**     || należy wybrać opcję `cosmosDB`.        |
|**direction**     || należy wybrać opcję `out`.         |
|**Nazwa**     || Nazwa parametru powiązania, który reprezentuje dokument w funkcji.  |
|**databaseName** | **DatabaseName**|Baza danych zawierający kolekcję, do której jest tworzony dokumentu.     |
|**CollectionName** |**CollectionName**  | Nazwa kolekcji, których tworzone jest dokumentu. |
|**CreateIfNotExists**  |**CreateIfNotExists**    | Wartość logiczna wskazująca, czy kolekcja jest tworzony, gdy nie istnieje. Wartość domyślna to *false* ponieważ nowe kolekcje są tworzone z zarezerwowaną przepływnością, co ma koszt skutki. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**PartitionKey**|**PartitionKey** |Gdy `CreateIfNotExists` ma wartość true, określa ścieżkę klucza partycji dla utworzonej kolekcji.|
|**CollectionThroughput**|**CollectionThroughput**| Gdy `CreateIfNotExists` ma wartość true, określa [przepływności](../cosmos-db/set-throughput.md) utworzonej kolekcji.|
|**connectionStringSetting**    |**ConnectionStringSetting** |Nazwa ustawienia aplikacji zawierający parametry połączenia bazy danych Azure rozwiązania Cosmos.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

Domyślnie podczas zapisywania do parametru wyjściowego w funkcji, dokument jest tworzony w bazie danych. Ten dokument ma automatycznie generowanym identyfikatorze GUID jako identyfikatora dokumentu. Identyfikator dokumentu dokumentu wyjściowego można określić, podając `id` właściwości w obiekcie JSON przekazanego do parametru wyjściowego. 

> [!Note]  
> Po określeniu Identyfikatora istniejącego dokumentu pobiera zastąpione przez nowy dokument danych wyjściowych. 

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie | Informacje ogólne |
|---|---|
| CosmosDB | [Kody błędów CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przejdź do szybkiego startu, używającej wyzwalacz DB rozwiązania Cosmos](functions-create-cosmos-db-triggered-function.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o bez serwera bazy danych, przetwarzania danych z bazy danych rozwiązania Cosmos](..\cosmos-db\serverless-computing-database.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o usługę Azure functions wyzwalaczy i powiązań](functions-triggers-bindings.md)
