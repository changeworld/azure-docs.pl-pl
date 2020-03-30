---
title: Powiązania usługi Azure Cosmos DB dla funkcji 1.x
description: Dowiedz się, jak używać wyzwalaczy i powiązań usługi Azure Cosmos DB w usłudze Azure Functions.
author: craigshoemaker
ms.author: cshoe
ms.topic: reference
ms.date: 11/21/2017
ms.custom: seodec18
ms.openlocfilehash: e30b256d9fa43402c3b2c444aa1a0e0dc16cfdcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277546"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Powiązania usługi Azure Cosmos DB dla usługi Azure Functions 1.x

> [!div class="op_single_selector" title1="Wybierz wersję używanego środowiska wykonawczego usługi Azure Functions: "]
> * [Wersja 1](functions-bindings-cosmosdb.md)
> * [Wersja 2](functions-bindings-cosmosdb-v2.md)

W tym artykule wyjaśniono, jak pracować z powiązaniami [usługi Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) w usłudze Azure Functions. Usługa Azure Functions obsługuje powiązania wyzwalania, wprowadzania i danych wyjściowych dla usługi Azure Cosmos DB.

> [!NOTE]
> Ten artykuł dotyczy usługi Azure Functions 1.x. Aby uzyskać informacje dotyczące używania tych powiązań w usługach 2.x lub [nowszych, zobacz Powiązania usługi Azure Cosmos DB dla usługi Azure Functions 2.x](functions-bindings-cosmosdb-v2.md).
>
>To powiązanie pierwotnie nazwano DocumentDB. W funkcji w wersji 1.x tylko wyzwalacz został przemianowany na Cosmos DB; powiązanie wejściowe, powiązanie danych wyjściowych i pakiet NuGet zachowują nazwę Bazy danych DocumentDB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Użycie powiązań usługi Azure Cosmos DB jest obsługiwane tylko w przypadku interfejsu API SQL. W przypadku wszystkich innych interfejsów API bazy danych usługi Azure Cosmos należy uzyskać dostęp do bazy danych z funkcji przy użyciu klienta statycznego dla interfejsu API, w tym [interfejsu API usługi Azure Cosmos DB dla mongodb,](../cosmos-db/mongodb-introduction.md)interfejsu API [Cassandra](../cosmos-db/cassandra-introduction.md), [interfejsu API Gremlin](../cosmos-db/graph-introduction.md)i [interfejsu API tabeli](../cosmos-db/table-introduction.md).

## <a name="packages---functions-1x"></a>Pakiety - Funkcje 1.x

Powiązania usługi Azure Cosmos DB dla funkcji w wersji 1.x znajdują się w pakiecie [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) NuGet w wersji 1.x. Kod źródłowy dla powiązań znajduje się w repozytorium GitHub w [zakresie azure-webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Wyzwalacz

Wyzwalacz usługi Azure Cosmos DB używa [kanału informacyjnego usługi Azure Cosmos DB Change Feed](../cosmos-db/change-feed.md) do nasłuchiwać wstawia i aktualizacji między partycjami. Kanał zmian publikuje wstawia i aktualizuje, a nie usuwa.

## <a name="trigger---example"></a>Wyzwalacz — przykład

# <a name="c"></a>[C #](#tab/csharp)

Poniższy przykład pokazuje [funkcję Języka C#,](functions-dotnet-class-library.md) która jest wywoływana, gdy istnieją wstawia lub aktualizacje w określonej bazie danych i kolekcji.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV1
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

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

W poniższym przykładzie pokazano powiązanie wyzwalacza usługi Cosmos DB w pliku *function.json* i [funkcję skryptu Języka C#,](functions-reference-csharp.md) która używa powiązania. Funkcja zapisuje komunikaty dziennika, gdy rekordy usługi Cosmos DB są modyfikowane.

Oto dane powiązania w pliku *function.json:*

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

Oto kod skryptu języka C#:

```cs
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Info("Documents modified " + documents.Count);
        log.Info("First document Id " + documents[0].Id);
    }
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza usługi Cosmos DB w pliku *function.json* i [funkcję JavaScript,](functions-reference-node.md) która używa powiązania. Funkcja zapisuje komunikaty dziennika, gdy rekordy usługi Cosmos DB są modyfikowane.

Oto dane powiązania w pliku *function.json:*

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

---

## <a name="trigger---attributes"></a>Trigger - atrybuty

# <a name="c"></a>[C #](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [CosmosDBTrigger.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs)

Konstruktor atrybutu przyjmuje nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i innych właściwościach, które można skonfigurować, zobacz [Wyzwalacz — konfiguracja](#trigger---configuration). Oto przykład `CosmosDBTrigger` atrybutu w podpisie metody:

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

Aby uzyskać pełny przykład, zobacz [Trigger - C# przykład](#trigger).

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

---

## <a name="trigger---configuration"></a>Trigger - konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `CosmosDBTrigger` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typu** | Nie dotyczy | Musi być `cosmosDBTrigger`ustawiona na . |
|**Kierunku** | Nie dotyczy | Musi być `in`ustawiona na . Ten parametr jest ustawiany automatycznie podczas tworzenia wyzwalacza w witrynie Azure portal. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej używana w kodzie funkcji reprezentująca listę dokumentów ze zmianami. |
|**connectionStringSetting**|**ConnectionStringSetting (Ustawienia połączeń)** | Nazwa ustawienia aplikacji, który zawiera parametry połączenia używane do łączenia się z kontem usługi Azure Cosmos DB monitorowane. |
|**Databasename**|**DatabaseName**  | Nazwa bazy danych usługi Azure Cosmos DB z kolekcji są monitorowane. |
|**Collectionname** |**CollectionName** | Nazwa monitorowanej kolekcji. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Opcjonalnie) Nazwa ustawienia aplikacji, który zawiera parametry połączenia z usługą, która przechowuje kolekcję dzierżawy. Gdy nie jest `connectionStringSetting` ustawiona, używana jest wartość. Ten parametr jest ustawiany automatycznie podczas tworzenia powiązania w portalu. Parametry połączenia dla kolekcji dzierżaw muszą mieć uprawnienia do zapisu.|
|**leaseDatabaseName** |**LeaseDatabaseName (Nazwa danych umowy dzierżawy)** | (Opcjonalnie) Nazwa bazy danych, która przechowuje kolekcję używaną do przechowywania dzierżaw. Gdy nie jest ustawiona, używana jest wartość `databaseName` ustawienia. Ten parametr jest ustawiany automatycznie podczas tworzenia powiązania w portalu. |
|**leaseCollectionName** | **LeaseCollectionName** | (Opcjonalnie) Nazwa kolekcji używanej do przechowywania dzierżaw. Gdy nie jest `leases` ustawiona, używana jest wartość. |
|**tworzenieLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Opcjonalnie) Po ustawieniu `true`kolekcji dzierżaw jest automatycznie tworzony, gdy jeszcze nie istnieje. Wartością domyślną jest `false`. |
|**leasesCollectionThroughput**| **DzierżawaCollectionThroughput**| (Opcjonalnie) Określa ilość jednostek żądań do przypisania podczas tworzenia kolekcji dzierżaw. To ustawienie jest `createLeaseCollectionIfNotExists` używane tylko `true`wtedy, gdy jest ustawione na . Ten parametr jest ustawiany automatycznie podczas tworzenia powiązania za pomocą portalu.
|**leaseCollectionPrefix**| **Umowa najmuPrefix**| (Opcjonalnie) Po ustawieniu dodaje prefiks do dzierżaw utworzonych w kolekcji dzierżawy dla tej funkcji, skutecznie umożliwiając dwa oddzielne usługi Azure Functions do udziału w tej samej kolekcji dzierżawy przy użyciu różnych prefiksów.
|**feedPollDelay**| **FeedPollDelay (Polski)**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, opóźnienie między sondowania partycji dla nowych zmian w paszy, po wszystkich bieżących zmian są opróżniane. Wartość domyślna to 5000 (5 sekund).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Opcjonalnie) Po ustawieniu definiuje w milisekundach interwał do rozpoczęcia zadania do obliczenia, jeśli partycje są rozłożone równomiernie między znane wystąpienia hosta. Wartość domyślna to 13000 (13 sekund).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Opcjonalnie) Po ustawieniu definiuje w milisekundach interwał, dla którego dzierżawa jest pobierana na dzierżawę reprezentującą partycję. Jeśli dzierżawa nie zostanie odnowiona w tym przedziale czasu, spowoduje to jej wygaśnięcie, a własność partycji zostanie przesunięta do innego wystąpienia. Wartość domyślna to 60000 (60 sekund).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Opcjonalnie) Po ustawieniu definiuje w milisekundach interwał odnawiania dla wszystkich dzierżaw dla partycji aktualnie posiadanych przez wystąpienie. Wartość domyślna to 17000 (17 sekund).
|**częstotliwość punktów kontrolnych**| **Częstotliwość punktów kontrolnych**| (Opcjonalnie) Po ustawieniu definiuje w milisekundach interwał między punktami kontrolnymi dzierżawy. Wartość domyślna jest zawsze po każdym wywołaniu funkcji.
|**maxItemsPerInvocation**| **Maksymalnezawodowanie Maksymalnewywodowanie**| (Opcjonalnie) Po ustawieniu dostosowuje maksymalną ilość elementów odebranych na wywołanie funkcji.
|**startFromBeginning**| **StartFromBeginning**| (Opcjonalnie) Po ustawieniu informuje Wyzwalacz, aby rozpocząć odczytywanie zmian od początku historii kolekcji zamiast bieżącego czasu. Działa to tylko przy pierwszym uruchomieniu wyzwalacza, ponieważ w kolejnych uruchomieniach punkty kontrolne są już przechowywane. Ustawienie tego `true` na czas, gdy istnieją dzierżawy już utworzone nie ma wpływu.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger - użycie

Wyzwalacz wymaga drugiej kolekcji, która używa do przechowywania _dzierżaw_ za pomocą partycji. Zarówno kolekcji monitorowane i kolekcji, która zawiera dzierżawy muszą być dostępne dla wyzwalacza do pracy.

>[!IMPORTANT]
> Jeśli wiele funkcji są skonfigurowane do używania wyzwalacza usługi Cosmos DB dla tej samej `LeaseCollectionPrefix` kolekcji, każda z funkcji należy użyć dedykowanej kolekcji dzierżawy lub określić inny dla każdej funkcji. W przeciwnym razie zostanie wyzwolona tylko jedna z funkcji. Aby uzyskać informacje o prefiksie, zobacz [sekcję Konfiguracja](#trigger---configuration).

Wyzwalacz nie wskazuje, czy dokument został zaktualizowany lub wstawiony, po prostu udostępnia sam dokument. Jeśli chcesz obsługiwać aktualizacje i wstawia inaczej, można to zrobić, implementując pola sygnatury czasowe do wstawiania lub aktualizacji.

## <a name="input"></a>Dane wejściowe

Powiązanie wejściowe usługi Azure Cosmos DB używa interfejsu API SQL, aby pobrać co najmniej jeden dokument usługi Azure Cosmos DB, a następnie przekazuje go do parametru wejściowego funkcji. Identyfikator dokumentu lub parametry zapytania można określić na podstawie wyzwalacza wywołującego funkcję.

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
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Wyzwalacz kolejki, wyszukuj identyfikator z JSON

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez komunikat kolejki, który zawiera obiekt JSON. Wyzwalacz kolejki analizuje JSON do obiektu `ToDoItemLookup`o nazwie , który zawiera identyfikator do wyszukania. Ten identyfikator jest używany do `ToDoItem` pobierania dokumentu z określonej bazy danych i kolekcji.

```cs
namespace CosmosDBSamplesV1
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

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [DocumentDB(
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

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Wyzwalacz HTTP, wyszukuj identyfikator z ciągu zapytania

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania do określenia wyszukiwanego identyfikatora. Ten identyfikator jest używany do `ToDoItem` pobierania dokumentu z określonej bazy danych i kolekcji.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
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
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Wyzwalacz HTTP, wyszukuj identyfikator z danych trasy

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa danych trasy do określenia identyfikatora do wyszukania. Ten identyfikator jest używany do `ToDoItem` pobierania dokumentu z określonej bazy danych i kolekcji.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static HttpResponseMessage Run(
            [HttpTrigger(
                AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{id}")]HttpRequestMessage req,
            [DocumentDB(
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
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Pomiń przykłady wprowadzania](#input---attributes)

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Wyzwalacz HTTP, wyszukuj identyfikator z danych trasy przy użyciu programu SqlQuery

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa danych trasy do określenia identyfikatora do wyszukania. Ten identyfikator jest używany do `ToDoItem` pobierania dokumentu z określonej bazy danych i kolekcji.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")] IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Pomiń przykłady wprowadzania](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Wyzwalacz HTTP, pobierz wiele dokumentów, używając SqlQuery

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Kwerenda jest określona we właściwości atrybutu. `SqlQuery`

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequestMessage req,
            [DocumentDB(
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
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Pomiń przykłady wprowadzania](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>Wyzwalacz HTTP, pobierz wiele dokumentów, używając dokumentu DocumentClient (C#)

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Kod używa wystąpienia `DocumentClient` dostarczonego przez powiązanie usługi Azure Cosmos DB do odczytu listy dokumentów. Wystąpienie `DocumentClient` może być również używane do operacji zapisu.

```cs
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System;
using System.Linq;
using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
            string searchterm = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
                .Value;

            if (searchterm == null)
            {
                return req.CreateResponse(HttpStatusCode.NotFound);
            }

            log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
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
            return req.CreateResponse(HttpStatusCode.OK);
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
namespace CosmosDBSamplesV1
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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
}
```

W sekcji [konfiguracji](#input---configuration) opisano te właściwości.

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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

W sekcji [konfiguracji](#input---configuration) opisano te właściwości.

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

W poniższym przykładzie przedstawiono [funkcję skryptu Języka C#,](functions-reference-csharp.md) która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania do określenia wyszukiwanego identyfikatora. Ten identyfikator jest używany do `ToDoItem` pobierania dokumentu z określonej bazy danych i kolekcji.

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
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Oto kod skryptu języka C#:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
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
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Wyzwalacz HTTP, wyszukuj identyfikator z danych trasy

W poniższym przykładzie przedstawiono [funkcję skryptu Języka C#,](functions-reference-csharp.md) która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa danych trasy do określenia identyfikatora do wyszukania. Ten identyfikator jest używany do `ToDoItem` pobierania dokumentu z określonej bazy danych i kolekcji.

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
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Oto kod skryptu języka C#:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
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
      "type": "documentDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
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

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.Info(toDoItem.Description);
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
      "type": "documentDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
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

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
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
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Ta sekcja zawiera następujące przykłady:

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

W sekcji [konfiguracji](#input---configuration) opisano te właściwości.

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

W poniższym przykładzie przedstawiono [funkcję JavaScript,](functions-reference-node.md) która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania do określenia wyszukiwanego identyfikatora. Ten identyfikator jest używany do `ToDoItem` pobierania dokumentu z określonej bazy danych i kolekcji.

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
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
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

W poniższym przykładzie przedstawiono [funkcję JavaScript,](functions-reference-node.md) która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania do określenia wyszukiwanego identyfikatora. Ten identyfikator jest używany do `ToDoItem` pobierania dokumentu z określonej bazy danych i kolekcji.

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
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

W sekcji [konfiguracji](#input---configuration) opisano te właściwości.

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

---

## <a name="input---attributes"></a>Dane wejściowe - atrybuty

# <a name="c"></a>[C #](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [DocumentDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs)

Konstruktor atrybutu przyjmuje nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i innych właściwościach, które można skonfigurować, zobacz [następującą sekcję konfiguracji](#input---configuration).

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

---

## <a name="input---configuration"></a>Wejście - konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `DocumentDB` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typu**     | Nie dotyczy | Musi być `documentdb`ustawiona na .        |
|**Kierunku**     | Nie dotyczy | Musi być `in`ustawiona na .         |
|**Nazwa**     | Nie dotyczy | Nazwa parametru wiązania reprezentującego dokument w funkcji.  |
|**Databasename** |**DatabaseName** |Baza danych zawierająca dokument.        |
|**Collectionname** |**CollectionName** | Nazwa kolekcji, która zawiera dokument. |
|**Identyfikator**    | **Identyfikator** | Identyfikator dokumentu do pobrania. Ta właściwość obsługuje [wyrażenia wiązania](./functions-bindings-expressions-patterns.md). Nie ustawiaj zarówno **właściwości id, jak** i **sqlQuery.** Jeśli nie ustawisz jednego, cała kolekcja zostanie pobrana. |
|**sqlQuery ( sqlQuery )**  |**Zapytania SqlQuery**  | Kwerenda SQL usługi Azure Cosmos DB używana do pobierania wielu dokumentów. Właściwość obsługuje powiązania środowiska wykonawczego, jak `SELECT * FROM c where c.departmentId = {departmentId}`w tym przykładzie: . Nie ustawiaj zarówno **właściwości id, jak** i **sqlQuery.** Jeśli nie ustawisz jednego, cała kolekcja zostanie pobrana.|
|**Połączenia**     |**ConnectionStringSetting (Ustawienia połączeń)**|Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi Azure Cosmos DB.        |
|**partitionKey (klucz)**|**PartitionKey**|Określa wartość klucza partycji dla wyszukiwania. Może zawierać parametry wiązania.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Wejście - użycie

# <a name="c"></a>[C #](#tab/csharp)

Gdy funkcja kończy się pomyślnie, wszelkie zmiany wprowadzone do dokumentu wejściowego za pośrednictwem nazwanych parametrów wejściowych są automatycznie utrwalone.

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Gdy funkcja kończy się pomyślnie, wszelkie zmiany wprowadzone do dokumentu wejściowego za pośrednictwem nazwanych parametrów wejściowych są automatycznie utrwalone.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Aktualizacje nie są dokonywane automatycznie po zamknięciu funkcji. Zamiast tego `context.bindings.<documentName>In` użyj `context.bindings.<documentName>Out` i do aktualizacji. Zobacz [przykład wprowadzania](#input).

---

## <a name="output"></a>Dane wyjściowe

Powiązanie danych wyjściowych usługi Azure Cosmos DB umożliwia pisanie nowego dokumentu w bazie danych usługi Azure Cosmos DB przy użyciu interfejsu API SQL.

# <a name="c"></a>[C #](#tab/csharp)

Ta sekcja zawiera następujące przykłady:

* Wyzwalacz kolejki, napisz jeden doc
* Wyzwalacz kolejki, pisanie dokumentów przy użyciu`IAsyncCollector`

Przykłady odnoszą się `ToDoItem` do prostego typu:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

### <a name="queue-trigger-write-one-doc"></a>Wyzwalacz kolejki, napisz jeden doc

W poniższym przykładzie przedstawiono [funkcję Języka C#,](functions-dotnet-class-library.md) która dodaje dokument do bazy danych przy użyciu danych dostarczonych w wiadomości z magazynu kolejki.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System;

namespace CosmosDBSamplesV1
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [DocumentDB(
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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wyzwalacz kolejki, zapis dokumentów przy użyciu IAsyncCollector

W poniższym przykładzie przedstawiono [funkcję Języka C#,](functions-dotnet-class-library.md) która dodaje kolekcję dokumentów do bazy danych przy użyciu danych podanych w wiadomości kolejki JSON.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [DocumentDB(
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

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Ta sekcja zawiera następujące przykłady:

* Wyzwalacz kolejki, napisz jeden doc
* Wyzwalacz kolejki, pisanie dokumentów przy użyciu`IAsyncCollector`

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

W sekcji [konfiguracji](#output---configuration) opisano te właściwości.

Oto kod skryptu języka C#:

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wyzwalacz kolejki, zapis dokumentów przy użyciu IAsyncCollector

Aby utworzyć wiele dokumentów, `ICollector<T>` można `IAsyncCollector<T>` `T` powiązać z lub gdzie jest jednym z obsługiwanych typów.

W tym przykładzie `ToDoItem` odnosi się do prostego typu:

```cs
namespace CosmosDBSamplesV1
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
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "documentDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Oto kod skryptu języka C#:

```cs
using System;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.Info($"Description={toDoItem.Description}");
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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

W sekcji [konfiguracji](#output---configuration) opisano te właściwości.

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

---

## <a name="output---attributes"></a>Dane wyjściowe - atrybuty

# <a name="c"></a>[C #](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [DocumentDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs)

Konstruktor atrybutu przyjmuje nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i innych właściwościach, które można skonfigurować, zobacz [Dane wyjściowe — konfiguracja](#output---configuration). Oto przykład `DocumentDB` atrybutu w podpisie metody:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Aby uzyskać pełny przykład, zobacz [Dane wyjściowe](#output).

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

---

## <a name="output---configuration"></a>Wyjście - konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `DocumentDB` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typu**     | Nie dotyczy | Musi być `documentdb`ustawiona na .        |
|**Kierunku**     | Nie dotyczy | Musi być `out`ustawiona na .         |
|**Nazwa**     | Nie dotyczy | Nazwa parametru wiązania reprezentującego dokument w funkcji.  |
|**Databasename** | **DatabaseName**|Baza danych zawierająca kolekcję, w której tworzony jest dokument.     |
|**Collectionname** |**CollectionName**  | Nazwa kolekcji, w której tworzony jest dokument. |
|**tworzenieIfNotExists**  |**CreateIfNotExists**    | Wartość logiczna wskazująca, czy kolekcja jest tworzona, gdy nie istnieje. Wartość domyślna jest *false,* ponieważ nowe kolekcje są tworzone z przepływnością zarezerwowaną, co ma wpływ na koszty. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**partitionKey (klucz)**|**PartitionKey** |Gdy `CreateIfNotExists` jest true, definiuje ścieżkę klucza partycji dla utworzonej kolekcji.|
|**kolekcjaDuchnia**|**KolekcjaWyjęcie**| Gdy `CreateIfNotExists` jest true, definiuje [przepływność](../cosmos-db/set-throughput.md) utworzonej kolekcji.|
|**Połączenia**    |**ConnectionStringSetting (Ustawienia połączeń)** |Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Wyjście - użycie

Domyślnie podczas zapisu do parametru wyjściowego w funkcji, dokument jest tworzony w bazie danych. Ten dokument ma automatycznie generowany identyfikator GUID jako identyfikator dokumentu. Można określić identyfikator dokumentu wyjściowego, określając `id` właściwość w obiekcie JSON przekazanym parametrowi wyjściowemu.

> [!Note]
> Po określeniu identyfikatora istniejącego dokumentu zostanie on zastąpiony przez nowy dokument wyjściowy.

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody zwrotne

| Wiązanie | Tematy pomocy |
|---|---|
| CosmosDB | [Kody błędów usługi CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o przetwarzaniu bezserwerowych baz danych w usłudze Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Dowiedz się więcej o wyzwalaczach i powiązaniach funkcji platformy Azure](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
