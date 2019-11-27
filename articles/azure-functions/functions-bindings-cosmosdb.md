---
title: Azure Cosmos DB powiązania funkcji 1.x
description: Dowiedz się, jak używać usługi Azure Cosmos DB, wyzwalaczy i powiązań w usłudze Azure Functions.
author: craigshoemaker
ms.author: cshoe
ms.topic: reference
ms.date: 11/21/2017
ms.custom: seodec18
ms.openlocfilehash: 18bbfd1b54947bb88ba8f06c65a17b90430b38a3
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74305222"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Azure Cosmos DB powiązania usługi Azure Functions 1.x

> [!div class="op_single_selector" title1="Wybierz wersję środowiska uruchomieniowego Azure Functions, którego używasz: "]
> * [Wersja 1](functions-bindings-cosmosdb.md)
> * [Wersja 2](functions-bindings-cosmosdb-v2.md)

W tym artykule opisano sposób pracy z powiązaniami [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) w programie Azure Functions. Usługi Azure Functions obsługuje wyzwalanie, dane wejściowe i wyjściowe powiązań usługi Azure Cosmos DB.

> [!NOTE]
> Ten artykuł jest przeznaczony dla usługi Azure Functions 1.x. Aby uzyskać informacje o sposobach korzystania z tych powiązań w funkcjach 2. x, zobacz [Azure Cosmos DB powiązania dla Azure Functions 2. x](functions-bindings-cosmosdb-v2.md).
>
>To powiązanie pierwotnie nosiła nazwę bazy danych DocumentDB. Funkcje wersji 1.x, tylko wyzwalacz został zmieniona Cosmos DB; powiązania danych wejściowych, powiązania danych wyjściowych i pakietu NuGet należy zachować nazwę bazy danych DocumentDB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Użycie powiązań usługi Azure Cosmos DB jest obsługiwane tylko w przypadku interfejsu API SQL. W przypadku wszystkich innych Azure Cosmos DB interfejsów API należy uzyskać dostęp do bazy danych z funkcji przy użyciu klienta statycznego dla interfejsu API, w tym [interfejsu api Azure Cosmos DB dla MongoDB](../cosmos-db/mongodb-introduction.md), [interfejs API Cassandra](../cosmos-db/cassandra-introduction.md), [interfejsu API Gremlin](../cosmos-db/graph-introduction.md)i [interfejs API tabel](../cosmos-db/table-introduction.md).

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania Azure Cosmos DB dla funkcji w wersji 1. x są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) w wersji 1. x. Kod źródłowy powiązań znajduje się w repozytorium [Azure-WebJobs-SDK-Extensions — rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB) GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Wyzwalacz

Wyzwalacz Azure Cosmos DB używa [źródła zmian Azure Cosmos DB](../cosmos-db/change-feed.md) do nasłuchiwania operacji wstawiania i aktualizacji między partycjami. Kanał informacyjny zmian publikuje wstawienia i aktualizacje, nie do usunięcia.

## <a name="trigger---example"></a>Wyzwalacz — przykład

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

W poniższym przykładzie pokazano [ C# funkcję](functions-dotnet-class-library.md) , która jest wywoływana, gdy w określonej bazie danych i kolekcji są wstawiane lub aktualizowane.

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

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Poniższy przykład przedstawia powiązanie wyzwalacza Cosmos DB w pliku *Function. JSON* i [ C# funkcji skryptu](functions-reference-csharp.md) , która używa powiązania. Funkcja zapisuje komunikaty dziennika, po zmodyfikowaniu rekordy usługi Cosmos DB.

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza Cosmos DB w pliku *Function. JSON* oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja zapisuje komunikaty dziennika, po zmodyfikowaniu rekordy usługi Cosmos DB.

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

---

## <a name="trigger---attributes"></a>Wyzwalacz — atrybuty

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj atrybutu [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) .

Konstruktor atrybutu ma nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i innych właściwościach, które można skonfigurować, zobacz [wyzwalacz-konfiguracja](#trigger---configuration). Oto przykład atrybutu `CosmosDBTrigger` w sygnaturze metody:

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

Aby zapoznać się z kompletnym przykładem, zobacz [wyzwalacz- C# example](#trigger).

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Atrybuty nie są obsługiwane przez C# skrypt.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

---

## <a name="trigger---configuration"></a>Wyzwalacz — Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* i `CosmosDBTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** || Musi być ustawiony na `cosmosDBTrigger`. |
|**direction** || Musi być ustawiony na `in`. Ten parametr jest ustawiany automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. |
|**Nazwij** || Nazwa zmiennej, używany w kodzie funkcji, który reprezentuje listę dokumentów za pomocą zmian. |
|**connectionStringSetting**|**ConnectionStringSetting** | Nazwa ustawienia aplikacji zawierającego parametry połączenia używane do łączenia z konta usługi Azure Cosmos DB są monitorowane. |
|**Bazy**|**Bazy**  | Nazwa bazy danych Azure Cosmos DB za pomocą kolekcji są monitorowane. |
|**CollectionName** |**CollectionName** | Nazwa kolekcji są monitorowane. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Opcjonalnie) Nazwa ustawienia aplikacji zawierającego parametry połączenia z usługą, która zawiera kolekcję dzierżaw. Gdy nie jest ustawiona, zostanie użyta wartość `connectionStringSetting`. Ten parametr jest automatycznie ustawiana podczas tworzenia powiązania w portalu. Parametry połączenia dla kolekcji dzierżaw musi mieć uprawnienia do zapisu.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Opcjonalnie) Nazwa bazy danych, który zawiera kolekcję używaną do przechowywania dzierżaw. Gdy nie jest ustawiona, zostanie użyta wartość ustawienia `databaseName`. Ten parametr jest automatycznie ustawiana podczas tworzenia powiązania w portalu. |
|**leaseCollectionName** | **LeaseCollectionName** | (Opcjonalnie) Nazwa kolekcji, używany do przechowywania dzierżaw. Gdy nie jest ustawiona, wartość `leases` jest używana. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Obowiązkowe Po ustawieniu na `true`, kolekcja dzierżaw zostanie automatycznie utworzona, gdy jeszcze nie istnieje. Wartość domyślna to `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| (Opcjonalnie) Definiuje liczbę jednostek żądania do przypisania, po utworzeniu kolekcji dzierżaw. To ustawienie jest używane tylko wtedy, gdy `createLeaseCollectionIfNotExists` jest ustawiona na `true`. Ten parametr jest ustawiany automatycznie podczas tworzenia powiązania przy użyciu portalu.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| (Opcjonalnie) Po ustawieniu dodaje prefiks do dzierżawy utworzone w kolekcję dzierżaw dla tej funkcji, co skutecznie dwie oddzielne funkcje platformy Azure na udostępnianie tej samej kolekcji dzierżawy przy użyciu różnych prefiksów.
|**feedPollDelay**| **FeedPollDelay**| (Opcjonalnie) Gdy zestaw, definiuje, w milisekundach, opóźnienie między sondowaniem partycji dla nowych zmian w źródle danych, gdy wszystkie bieżące zmiany są opróżniane. Wartością domyślną jest 5000 (5 sekund).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał można uruchamiać zadania obliczeniowe, jeśli partycje są dystrybuowane równomiernie między wystąpieniami znanych hostów. Wartość domyślna to 13000 (w sekundach 13).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał, dla której dzierżawy jest pobierany podczas dzierżawy, reprezentujący partycji. Jeśli dzierżawa nie zostanie odnowiony w tym przedziale czasu, spowoduje jego wygaśnięcia i własność partycji przejdzie do innego wystąpienia. Domyślna to 60 000 (60 sekund).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał odnawiania dla wszystkich dzierżaw dla partycji aktualnie trzymana przez wystąpienie. Wartość domyślna to 17000 (17 w sekundach).
|**checkpointFrequency**| **CheckpointFrequency**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał między punktami kontrolnymi dzierżawy. Wartość domyślna to zawsze po każdym wywołaniu funkcji.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| (Opcjonalnie) Po ustawieniu dostosowuje maksymalna ilość elementów odebranych na wywołanie funkcji.
|**startFromBeginning**| **StartFromBeginning**| Obowiązkowe Po ustawieniu nakazuje wyzwalaczowi rozpoczęcie odczytywania zmian od początku historii kolekcji zamiast bieżącego czasu. Działa to tylko po pierwszym uruchomieniu wyzwalacza, tak jak w kolejnych uruchomieniach, punkty kontrolne są już przechowywane. Ustawienie tej opcji na `true`, jeśli już utworzono dzierżawy, nie ma żadnego wpływu.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Wyzwalacz — użycie

Wyzwalacz wymaga drugiej kolekcji, która używa do przechowywania _dzierżaw_ w ramach partycji. Kolekcja monitorowanych i kolekcji, która zawiera dzierżaw musi być dostępny dla wyzwalacza do pracy.

>[!IMPORTANT]
> Jeśli wiele funkcji jest skonfigurowanych do korzystania z wyzwalacza Cosmos DB dla tej samej kolekcji, każda z tych funkcji powinna używać dedykowanej kolekcji dzierżaw lub określić inną `LeaseCollectionPrefix` dla każdej funkcji. W przeciwnym razie tylko jeden funkcje zostaną wyzwolone. Informacje na temat prefiksu znajdują się w [sekcji Konfiguracja](#trigger---configuration).

Wyzwalacz nie wskazuje, czy zaktualizowane lub wstawić dokumentu, po prostu zapewnia samego dokumentu. Jeśli wymagana jest obsługa aktualizacje i wstawienia w różny sposób, możesz można zrobić, implementując pola sygnatury czasowej dla wstawiania lub aktualizacji.

## <a name="input"></a>Dane wejściowe

Powiązania danych wejściowych usługi Azure Cosmos DB używa interfejsu API SQL do pobrania jednego lub więcej dokumentów usługi Azure Cosmos DB i przekazuje je do parametr wejściowy funkcji. Można określić parametry identyfikator lub kwerendę dokumentu oparte na wyzwalacz, który wywołuje funkcję.

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

### <a name="queue-trigger-look-up-id-from-json"></a>Wyzwalacz kolejki, wyszukiwanie identyfikatora z poziomu JSON

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez komunikatu w kolejce, która zawiera obiekt JSON. Wyzwalacz kolejki analizuje kod JSON w obiekcie o nazwie `ToDoItemLookup`, który zawiera identyfikator do wyszukania. Ten identyfikator jest używany do pobierania dokumentu `ToDoItem` z określonej bazy danych i kolekcji.

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

### <a name="http-trigger-look-up-id-from-query-string"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, która wykorzystuje ciąg kwerendy do określenia Identyfikatora do wyszukania. Ten identyfikator jest używany do pobierania dokumentu `ToDoItem` z określonej bazy danych i kolekcji.

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

### <a name="http-trigger-look-up-id-from-route-data"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, że używa kierować dane do określenia Identyfikatora do wyszukania. Ten identyfikator jest używany do pobierania dokumentu `ToDoItem` z określonej bazy danych i kolekcji.

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

[Pomiń przykładowe dane wejściowe](#input---attributes)

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy przy użyciu sqlQuery

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, że używa kierować dane do określenia Identyfikatora do wyszukania. Ten identyfikator jest używany do pobierania dokumentu `ToDoItem` z określonej bazy danych i kolekcji.

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

[Pomiń przykładowe dane wejściowe](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Wyzwalacz HTTP, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Zapytanie jest określone we właściwości atrybutu `SqlQuery`.

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

[Pomiń przykładowe dane wejściowe](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP wyzwalacza, Uzyskaj dokumentację wielu, za pomocą DocumentClient (C#)

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Kod używa wystąpienia `DocumentClient` dostarczonego przez powiązanie Azure Cosmos DB do odczytywania listy dokumentów. Wystąpienia `DocumentClient` można również użyć dla operacji zapisu.

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

### <a name="queue-trigger-look-up-id-from-string"></a>Wyzwalacz kolejki, wyszukiwanie identyfikatora z ciągu

Poniższy przykład przedstawia Cosmos DB dane wejściowe w pliku *Function. JSON* i [ C# funkcji skryptu](functions-reference-csharp.md) , która używa powiązania. Funkcja odczytuje pojedynczy dokument i aktualizuje wartość tekstu dokumentu.

Oto dane powiązania w pliku *Function. JSON* :

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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
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

Poniższy przykład pokazuje [ C# funkcję skryptu](functions-reference-csharp.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, która wykorzystuje ciąg kwerendy do określenia Identyfikatora do wyszukania. Ten identyfikator jest używany do pobierania dokumentu `ToDoItem` z określonej bazy danych i kolekcji.

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

Poniżej przedstawiono kod skryptu języka C#:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy

Poniższy przykład pokazuje [ C# funkcję skryptu](functions-reference-csharp.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, że używa kierować dane do określenia Identyfikatora do wyszukania. Ten identyfikator jest używany do pobierania dokumentu `ToDoItem` z określonej bazy danych i kolekcji.

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

Poniżej przedstawiono kod skryptu języka C#:

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

Poniżej przedstawiono kod skryptu języka C#:

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

Poniżej przedstawiono kod skryptu języka C#:

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Ta sekcja zawiera następujące przykłady:

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

Poniższy przykład pokazuje [funkcję języka JavaScript](functions-reference-node.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, która wykorzystuje ciąg kwerendy do określenia Identyfikatora do wyszukania. Ten identyfikator jest używany do pobierania dokumentu `ToDoItem` z określonej bazy danych i kolekcji.

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

Poniższy przykład pokazuje [funkcję języka JavaScript](functions-reference-node.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, która wykorzystuje ciąg kwerendy do określenia Identyfikatora do wyszukania. Ten identyfikator jest używany do pobierania dokumentu `ToDoItem` z określonej bazy danych i kolekcji.

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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
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

---

## <a name="input---attributes"></a>Dane wejściowe — atrybuty

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj atrybutu [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) .

Konstruktor atrybutu ma nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i innych właściwościach, które można skonfigurować, zobacz [następującą sekcję konfiguracyjną](#input---configuration).

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Atrybuty nie są obsługiwane przez C# skrypt.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

---

## <a name="input---configuration"></a>Dane wejściowe — Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* i `DocumentDB` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type**     || Musi być ustawiony na `documentdb`.        |
|**direction**     || Musi być ustawiony na `in`.         |
|**Nazwij**     || Nazwa parametru powiązania, który reprezentuje dokument w funkcji.  |
|**Bazy** |**Bazy** |Baza danych zawiera dokument.        |
|**CollectionName** |**CollectionName** | Nazwa kolekcji, która zawiera dokument. |
|**#**    | **Identyfikator** | Identyfikator dokumentu do pobrania. Ta właściwość obsługuje [wyrażenia powiązań](./functions-bindings-expressions-patterns.md). Nie ustawiaj zarówno właściwości **ID** , jak i **sqlQuery** . Jeśli nie ustawisz pojedynczo, zostanie pobrana całą kolekcję. |
|**sqlQuery**  |**SqlQuery**  | Zapytania SQL usługi Azure Cosmos DB używane do pobierania wiele dokumentów. Właściwość obsługuje powiązania środowiska uruchomieniowego, jak w tym przykładzie: `SELECT * FROM c where c.departmentId = {departmentId}`. Nie ustawiaj zarówno właściwości **ID** , jak i **sqlQuery** . Jeśli nie ustawisz pojedynczo, zostanie pobrana całą kolekcję.|
|**połączenia**     |**ConnectionStringSetting**|Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi Azure Cosmos DB.        |
|**partitionKey**|**PartitionKey**|Określa wartość klucza partycji do wyszukiwania. Może zawierać parametrów wiązania.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Dane wejściowe — użycie

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Gdy funkcja zostanie zakończona pomyślnie, wszelkie zmiany wprowadzone w dokumencie wejściowym za pośrednictwem nazwanych parametrów wejściowych są utrwalane automatycznie.

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Gdy funkcja zostanie zakończona pomyślnie, wszelkie zmiany wprowadzone w dokumencie wejściowym za pośrednictwem nazwanych parametrów wejściowych są utrwalane automatycznie.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Aktualizacje nie są wykonywane automatycznie po zamknięciu funkcji. Zamiast tego należy używać `context.bindings.<documentName>In` i `context.bindings.<documentName>Out` do aktualizowania. Zobacz [przykład danych wejściowych](#input).

---

## <a name="output"></a>Dane wyjściowe

Dane wyjściowe usługi Azure Cosmos DB, powiązania pozwala zapisać nowy dokument z bazą danych Azure Cosmos DB przy użyciu interfejsu API SQL.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Ta sekcja zawiera następujące przykłady:

* Wyzwalacz kolejki, doc jeden zapisu
* Wyzwalacz kolejki, Zapisz dokumenty przy użyciu `IAsyncCollector`

Przykłady odnoszą się do prostego typu `ToDoItem`:

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

### <a name="queue-trigger-write-one-doc"></a>Wyzwalacz kolejki, doc jeden zapisu

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która dodaje dokument do bazy danych przy użyciu danych z usługi queue storage.

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wyzwalacz kolejki, dokumentacja zapisu przy użyciu IAsyncCollector

W poniższym przykładzie pokazano [ C# funkcję](functions-dotnet-class-library.md) , która dodaje kolekcję dokumentów do bazy danych przy użyciu danych udostępnionych w formacie JSON komunikatu w kolejce.

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

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Ta sekcja zawiera następujące przykłady:

* Wyzwalacz kolejki, doc jeden zapisu
* Wyzwalacz kolejki, Zapisz dokumenty przy użyciu `IAsyncCollector`

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

W sekcji [Konfiguracja](#output---configuration) objaśniono te właściwości.

Poniżej przedstawiono kod skryptu języka C#:

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wyzwalacz kolejki, dokumentacja zapisu przy użyciu IAsyncCollector

Aby utworzyć wiele dokumentów, można powiązać z `ICollector<T>` lub `IAsyncCollector<T>`, gdzie `T` jest jednym z obsługiwanych typów.

Ten przykład dotyczy prostego typu `ToDoItem`:

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

Poniżej przedstawiono plik function.json:

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

Poniżej przedstawiono kod skryptu języka C#:

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
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

---

## <a name="output---attributes"></a>Dane wyjściowe — atrybuty

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj atrybutu [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) .

Konstruktor atrybutu ma nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i innych właściwościach, które można skonfigurować, zobacz [wyjście-konfiguracja](#output---configuration). Oto przykład atrybutu `DocumentDB` w sygnaturze metody:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Aby uzyskać pełny przykład, zobacz [dane wyjściowe](#output).

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Atrybuty nie są obsługiwane przez C# skrypt.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

---

## <a name="output---configuration"></a>Dane wyjściowe — Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* i `DocumentDB` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type**     || Musi być ustawiony na `documentdb`.        |
|**direction**     || Musi być ustawiony na `out`.         |
|**Nazwij**     || Nazwa parametru powiązania, który reprezentuje dokument w funkcji.  |
|**Bazy** | **Bazy**|Baza danych, zawierający kolekcję, w którym zostanie utworzona dokumentu.     |
|**CollectionName** |**CollectionName**  | Nazwa kolekcji jest tworzona dokumentu. |
|**Metodę createifnotexists**  |**Metodę createifnotexists**    | Wartość logiczna, aby wskazać, czy kolekcja jest tworzona, gdy nie istnieje. Wartość domyślna to *false* , ponieważ nowe kolekcje są tworzone z zarezerwowaną przepływność, która ma wpływ na koszty. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**partitionKey**|**PartitionKey** |Gdy `CreateIfNotExists` ma wartość true, definiuje ścieżkę klucza partycji dla utworzonej kolekcji.|
|**collectionThroughput**|**CollectionThroughput**| Gdy `CreateIfNotExists` ma wartość true, definiuje [przepływność](../cosmos-db/set-throughput.md) utworzonej kolekcji.|
|**połączenia**    |**ConnectionStringSetting** |Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

Domyślnie podczas zapisu do parametru wyjściowego w funkcji, tworzony jest dokument w bazie danych. Ten dokument ma automatycznie generowanym identyfikatorze GUID jako identyfikatora dokumentu. Możesz określić identyfikator dokumentu dla dokumentu wyjściowego, określając właściwość `id` w obiekcie JSON przekazaną do parametru Output.

> [!Note]
> Po określeniu identyfikator istniejącego dokumentu, pobiera to zastąpione przez nowy dokument danych wyjściowych.

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie | Dokumentacja |
|---|---|
| CosmosDB | [Kody błędów CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o liczbie bezserwerowych baz danych z Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Dowiedz się więcej o wyzwalaczach i powiązaniach usługi Azure Functions](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
