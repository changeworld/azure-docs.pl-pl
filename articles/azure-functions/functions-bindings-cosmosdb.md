---
title: Azure Cosmos DB powiązania funkcji 1.x
description: Dowiedz się, jak używać usługi Azure Cosmos DB, wyzwalaczy i powiązań w usłudze Azure Functions.
services: functions
author: craigshoemaker
ms.author: cshoe
manager: jeconnoc
keywords: usługi Azure functions, funkcje, przetwarzanie zdarzeń, obliczanie dynamiczne, architektura bez serwera
ms.service: azure-functions
ms.topic: reference
ms.date: 11/21/2017
ms.custom: seodec18
ms.openlocfilehash: 0421ec62d25bbfaba2909d16498cac5afd038a53
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60737282"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Azure Cosmos DB powiązania usługi Azure Functions 1.x

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Wersja 1](functions-bindings-cosmosdb.md)
> * [Wersja 2](functions-bindings-cosmosdb-v2.md)

W tym artykule wyjaśniono, jak pracować z [usługi Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) powiązań w usłudze Azure Functions. Usługi Azure Functions obsługuje wyzwalanie, dane wejściowe i wyjściowe powiązań usługi Azure Cosmos DB.

> [!NOTE]
> Ten artykuł jest przeznaczony dla usługi Azure Functions 1.x. Aby uzyskać informacje o sposobie używania tych powiązań w funkcjach 2.x, zobacz [powiązań usługi Azure Cosmos DB dla usługi Azure Functions 2.x](functions-bindings-cosmosdb-v2.md).
>
>To powiązanie pierwotnie nosiła nazwę bazy danych DocumentDB. Funkcje wersji 1.x, tylko wyzwalacz został zmieniona Cosmos DB; powiązania danych wejściowych, powiązania danych wyjściowych i pakietu NuGet należy zachować nazwę bazy danych DocumentDB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Użycie powiązań usługi Azure Cosmos DB jest obsługiwane tylko w przypadku interfejsu API SQL. Dla wszystkich innych usługi Azure Cosmos DB interfejsów API, należy dostęp do bazy danych ze swojej funkcji przy użyciu statycznych klienta dla interfejsu API, w tym [interfejsu API usługi Azure Cosmos DB, bazy danych mongodb](../cosmos-db/mongodb-introduction.md), [interfejsu API rozwiązania Cassandra](../cosmos-db/cassandra-introduction.md), [ Interfejs API języka gremlin](../cosmos-db/graph-introduction.md), i [interfejs API tabel](../cosmos-db/table-introduction.md).

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania usługi Azure Cosmos DB dla funkcji w wersji 1.x znajdują się w [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) pakietu NuGet w wersji 1.x. Kod źródłowy dla powiązania znajduje się w [zestawu sdk rozszerzenia, usługi azure webjobs w-](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB) repozytorium GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Wyzwalacz

Wyzwalacz usługi Azure Cosmos DB używa [usługi Azure Cosmos DB kanału informacyjnego zmian](../cosmos-db/change-feed.md) do nasłuchiwania pod kątem operacji wstawienia i aktualizacje w różnych partycjach. Kanał informacyjny zmian publikuje wstawienia i aktualizacje, nie do usunięcia.

## <a name="trigger---example"></a>Wyzwalacz — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#trigger---c-example)
* [Skryptu C# (csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

[Pomiń przykłady wyzwalacza](#trigger---attributes)

### <a name="trigger---c-example"></a>Wyzwalacz — przykład w języku C#

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , wywoływany, gdy istnieją wstawia lub aktualizuje w określonej bazy danych i kolekcji.

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

[Pomiń przykłady wyzwalacza](#trigger---attributes)

### <a name="trigger---c-script-example"></a>Wyzwalacz — przykładowy skrypt w języku C#

W poniższym przykładzie pokazano wyzwalacz usługi Cosmos DB, powiązania w *function.json* pliku i [funkcji skryptu w języku C#](functions-reference-csharp.md) powiązania, który używa. Funkcja zapisuje komunikaty dziennika, po zmodyfikowaniu rekordy usługi Cosmos DB.

Oto powiązanie danych w *function.json* pliku:

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

[Pomiń przykłady wyzwalacza](#trigger---attributes)

### <a name="trigger---javascript-example"></a>Wyzwalacz — przykład JavaScript

W poniższym przykładzie pokazano wyzwalacz usługi Cosmos DB, powiązania w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja zapisuje komunikaty dziennika, po zmodyfikowaniu rekordy usługi Cosmos DB.

Oto powiązanie danych w *function.json* pliku:

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

## <a name="trigger---attributes"></a>Wyzwalacz — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) atrybutu.

Konstruktor atrybutu ma nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i inne właściwości, które można skonfigurować, zobacz [wyzwalacza — Konfiguracja](#trigger---configuration). Oto `CosmosDBTrigger` przykład atrybutu w podpisie metody:

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

Aby uzyskać kompletny przykład, zobacz [wyzwalacza — przykład w języku C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Wyzwalacz — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `CosmosDBTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** || Musi być równa `cosmosDBTrigger`. |
|**direction** || Musi być równa `in`. Ten parametr jest ustawiany automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. |
|**Nazwa** || Nazwa zmiennej, używany w kodzie funkcji, który reprezentuje listę dokumentów za pomocą zmian. |
|**connectionStringSetting**|**ConnectionStringSetting** | Nazwa ustawienia aplikacji zawierającego parametry połączenia używane do łączenia z konta usługi Azure Cosmos DB są monitorowane. |
|**databaseName**|**DatabaseName**  | Nazwa bazy danych Azure Cosmos DB za pomocą kolekcji są monitorowane. |
|**collectionName** |**collectionName** | Nazwa kolekcji są monitorowane. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Opcjonalnie) Nazwa ustawienia aplikacji zawierającego parametry połączenia z usługą, która zawiera kolekcję dzierżaw. Gdy nie są ustawione, `connectionStringSetting` wartość jest używana. Ten parametr jest automatycznie ustawiana podczas tworzenia powiązania w portalu. Parametry połączenia dla kolekcji dzierżaw musi mieć uprawnienia do zapisu.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Opcjonalnie) Nazwa bazy danych, który zawiera kolekcję używaną do przechowywania dzierżaw. Jeśli nie ustawiona, wartość `databaseName` ustawienie jest używane. Ten parametr jest automatycznie ustawiana podczas tworzenia powiązania w portalu. |
|**leaseCollectionName** | **LeaseCollectionName** | (Opcjonalnie) Nazwa kolekcji, używany do przechowywania dzierżaw. Kiedy nie ustawiona, wartość `leases` jest używany. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Opcjonalnie) Po ustawieniu `true`, kolekcję dzierżaw zostało automatycznie utworzone po już nie istnieje. Wartość domyślna to `false`. |
|**leasesCollectionThroughput**| **leasesCollectionThroughput**| (Opcjonalnie) Definiuje liczbę jednostek żądania do przypisania, po utworzeniu kolekcji dzierżaw. To ustawienie jest tylko wtedy, gdy używane `createLeaseCollectionIfNotExists` ustawiono `true`. Ten parametr jest automatycznie ustawiana, jeśli wiązanie jest tworzony przy użyciu portalu.
|**leaseCollectionPrefix**| **leaseCollectionPrefix**| (Opcjonalnie) Po ustawieniu dodaje prefiks do dzierżawy utworzone w kolekcję dzierżaw dla tej funkcji, co skutecznie dwie oddzielne funkcje platformy Azure na udostępnianie tej samej kolekcji dzierżawy przy użyciu różnych prefiksów.
|**feedPollDelay**| **feedPollDelay**| (Opcjonalnie) Gdy zestaw, definiuje, w milisekundach, opóźnienie między sondowaniem partycji dla nowych zmian w źródle danych, gdy wszystkie bieżące zmiany są opróżniane. Wartością domyślną jest 5000 (5 sekund).
|**leaseAcquireInterval**| **leaseAcquireInterval**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał można uruchamiać zadania obliczeniowe, jeśli partycje są dystrybuowane równomiernie między wystąpieniami znanych hostów. Wartość domyślna to 13000 (w sekundach 13).
|**leaseExpirationInterval**| **leaseExpirationInterval**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał, dla której dzierżawy jest pobierany podczas dzierżawy, reprezentujący partycji. Jeśli dzierżawa nie zostanie odnowiony w tym przedziale czasu, spowoduje jego wygaśnięcia i własność partycji przejdzie do innego wystąpienia. Domyślna to 60 000 (60 sekund).
|**leaseRenewInterval**| **leaseRenewInterval**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał odnawiania dla wszystkich dzierżaw dla partycji aktualnie trzymana przez wystąpienie. Wartość domyślna to 17000 (17 w sekundach).
|**checkpointFrequency**| **checkpointFrequency**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał między punktami kontrolnymi dzierżawy. Wartością domyślną jest zawsze po każdym wywołaniu funkcji.
|**maxItemsPerInvocation**| **maxItemsPerInvocation**| (Opcjonalnie) Po ustawieniu dostosowuje maksymalna ilość elementów odebranych na wywołanie funkcji.
|**startFromBeginning**| **startFromBeginning**| (Opcjonalnie) Po ustawieniu informuje wyzwalacz, aby rozpocząć odczyt zmiany wprowadzone od początku historii kolekcja zamiast bieżącego czasu. Działa to tylko przy pierwszym uruchomieniu wyzwalacza, tak jak w kolejnych przebiegów, punkty kontrolne są już przechowywane. Ustawienie tej opcji na `true` w przypadku dzierżaw utworzone nie ma wpływu.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Wyzwalacz — użycie

Wyzwalacz wymaga druga kolekcja, która jest używana do przechowywania _dzierżawy_ poszczególnych partycjach. Kolekcja monitorowanych i kolekcji, która zawiera dzierżaw musi być dostępny dla wyzwalacza do pracy.

>[!IMPORTANT]
> Jeśli wiele funkcji są skonfigurowane do użycia wyzwalacz usługi Cosmos DB na potrzeby tej samej kolekcji, każda z tych funkcji należy używać kolekcji bezpiecznych dedykowanych dzierżawy lub określić inną `LeaseCollectionPrefix` dla każdej funkcji. W przeciwnym razie tylko jeden funkcje zostaną wyzwolone. Aby uzyskać informacji na temat prefiksu, zobacz [sekcji konfiguracji](#trigger---configuration).

Wyzwalacz nie wskazuje, czy zaktualizowane lub wstawić dokumentu, po prostu zapewnia samego dokumentu. Jeśli wymagana jest obsługa aktualizacje i wstawienia w różny sposób, możesz można zrobić, implementując pola sygnatury czasowej dla wstawiania lub aktualizacji.

## <a name="input"></a>Dane wejściowe

Powiązania danych wejściowych usługi Azure Cosmos DB używa interfejsu API SQL do pobrania jednego lub więcej dokumentów usługi Azure Cosmos DB i przekazuje je do parametr wejściowy funkcji. Można określić parametry identyfikator lub kwerendę dokumentu oparte na wyzwalacz, który wywołuje funkcję.

## <a name="input---examples"></a>Dane wejściowe — przykłady

Zobacz przykłady specyficzny dla języka, które odczytują pojedynczy dokument, określając wartość Identyfikatora:

* [C#](#input---c-examples)
* [Skryptu C# (csx)](#input---c-script-examples)
* [JavaScript](#input---javascript-examples)
* [F#](#input---f-examples)

[Pomiń przykłady danych wejściowych](#input---attributes)

### <a name="input---c-examples"></a>Dane wejściowe — przykłady w języku C#

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz kolejki, odnośnika identyfikator z formatu JSON](#queue-trigger-look-up-id-from-json-c)
* [Wyzwalacz HTTP, Sprawdź identyfikator z ciągu zapytania](#http-trigger-look-up-id-from-query-string-c)
* [Wyzwalacz HTTP, wyszukiwania Identyfikatora z danych trasy](#http-trigger-look-up-id-from-route-data-c)
* [Wyzwalacz HTTP, wyszukiwania Identyfikatora z danych trasy, używając SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP wyzwalacza, Uzyskaj dokumentację wielu, za pomocą SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP wyzwalacza, Uzyskaj dokumentację wielu, za pomocą DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Przykłady dotyczą prostego `ToDoItem` typu:

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

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-c"></a>Wyzwalacz kolejki, odnośnika identyfikator z formatu JSON (C#)

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , który pobiera pojedynczego dokumentu. Funkcja jest wyzwalana przez komunikatu w kolejce, która zawiera obiekt JSON. Wyzwalacz kolejki analizuje dane JSON na obiekt o nazwie `ToDoItemLookup`, który zawiera identyfikator aby wyszukać. Identyfikator służy do pobierania `ToDoItem` dokument z określonej bazy danych i kolekcji.

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

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c"></a>Wyzwalacz HTTP, Sprawdź identyfikator z ciągu zapytania (C#)

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , który pobiera pojedynczego dokumentu. Funkcja jest wyzwalana przez żądanie HTTP, która wykorzystuje ciąg kwerendy do określenia Identyfikatora do wyszukania. Identyfikator służy do pobierania `ToDoItem` dokument z określonej bazy danych i kolekcji.

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

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c"></a>Wyzwalacz HTTP, wyszukiwania Identyfikatora z danych trasy (C#)

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , który pobiera pojedynczego dokumentu. Funkcja jest wyzwalana przez żądanie HTTP, że używa kierować dane do określenia Identyfikatora do wyszukania. Identyfikator służy do pobierania `ToDoItem` dokument z określonej bazy danych i kolekcji.

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

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>Wyzwalacz HTTP, wyszukiwania Identyfikatora z danych trasy, używając SqlQuery (C#)

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , który pobiera pojedynczego dokumentu. Funkcja jest wyzwalana przez żądanie HTTP, że używa kierować dane do określenia Identyfikatora do wyszukania. Identyfikator służy do pobierania `ToDoItem` dokument z określonej bazy danych i kolekcji.

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

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c"></a>HTTP wyzwalacza, Uzyskaj dokumentację wielu, za pomocą SqlQuery (C#)

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , który pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Zapytanie jest określona w `SqlQuery` atrybutu właściwości.

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

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP wyzwalacza, Uzyskaj dokumentację wielu, za pomocą DocumentClient (C#)

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , który pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Kod używa `DocumentClient` podanego przez powiązanie usługi Azure Cosmos DB można odczytać listy dokumentów wystąpienia. `DocumentClient` Wystąpienia może również służyć do operacji zapisu.

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

[Pomiń przykłady danych wejściowych](#input---attributes)

### <a name="input---c-script-examples"></a>Dane wejściowe — przykłady skryptów w języku C#

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz kolejki, odnośnika identyfikator ciągu](#queue-trigger-look-up-id-from-string-c-script)
* [Wyzwalacz kolejki, Uzyskaj dokumentację wielu, za pomocą SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Wyzwalacz HTTP, Sprawdź identyfikator z ciągu zapytania](#http-trigger-look-up-id-from-query-string-c-script)
* [Wyzwalacz HTTP, wyszukiwania Identyfikatora z danych trasy](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP wyzwalacza, Uzyskaj dokumentację wielu, za pomocą SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP wyzwalacza, Uzyskaj dokumentację wielu, za pomocą DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Przykłady wyzwalacza HTTP można znaleźć prostego `ToDoItem` typu:

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

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-string-c-script"></a>Wyzwalacz kolejki, odnośnika identyfikator ciągu (skrypt języka C#)

Poniższy przykład pokazuje usługi Cosmos DB, powiązania danych wejściowych w *function.json* pliku i [funkcji skryptu w języku C#](functions-reference-csharp.md) powiązania, który używa. Funkcja odczytuje pojedynczy dokument i aktualizuje wartość tekstu dokumentu.

Oto powiązanie danych w *function.json* pliku:

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
[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod skryptu języka C#:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
        inputDocument.text = "This has changed.";
    }
```

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>Wyzwalacz kolejki, Uzyskaj dokumentację wielu, za pomocą SqlQuery (skrypt języka C#)

W poniższym przykładzie pokazano powiązania danych wejściowych usługi Azure Cosmos DB w *function.json* pliku i [funkcji skryptu w języku C#](functions-reference-csharp.md) powiązania, który używa. Funkcja pobiera wiele dokumentów, określonych przez zapytanie SQL, przy użyciu wyzwalacza kolejki, aby dostosować parametry zapytania.

Wyzwalacz kolejki zawiera parametr `departmentId`. Komunikat kolejki `{ "departmentId" : "Finance" }` zwróci wszystkie rekordy dla działu finansowego.

Oto powiązanie danych w *function.json* pliku:

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

[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

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

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c-script"></a>Wyzwalacz HTTP, Sprawdź identyfikator z ciągu zapytania (skrypt języka C#)

W poniższym przykładzie przedstawiono [funkcji skryptu w języku C#](functions-reference-csharp.md) , który pobiera pojedynczego dokumentu. Funkcja jest wyzwalana przez żądanie HTTP, która wykorzystuje ciąg kwerendy do określenia Identyfikatora do wyszukania. Identyfikator służy do pobierania `ToDoItem` dokument z określonej bazy danych i kolekcji.

Oto *function.json* pliku:

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

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c-script"></a>Wyzwalacz HTTP, wyszukiwania Identyfikatora z danych trasy (skrypt języka C#)

W poniższym przykładzie przedstawiono [funkcji skryptu w języku C#](functions-reference-csharp.md) , który pobiera pojedynczego dokumentu. Funkcja jest wyzwalana przez żądanie HTTP, że używa kierować dane do określenia Identyfikatora do wyszukania. Identyfikator służy do pobierania `ToDoItem` dokument z określonej bazy danych i kolekcji.

Oto *function.json* pliku:

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

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>HTTP wyzwalacza, Uzyskaj dokumentację wielu, za pomocą SqlQuery (skrypt języka C#)

W poniższym przykładzie przedstawiono [funkcji skryptu w języku C#](functions-reference-csharp.md) , który pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Zapytanie jest określona w `SqlQuery` atrybutu właściwości.

Oto *function.json* pliku:

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

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>HTTP wyzwalacza, Uzyskaj dokumentację wielu, za pomocą DocumentClient (skrypt języka C#)

W poniższym przykładzie przedstawiono [funkcji skryptu w języku C#](functions-reference-csharp.md) , który pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Kod używa `DocumentClient` podanego przez powiązanie usługi Azure Cosmos DB można odczytać listy dokumentów wystąpienia. `DocumentClient` Wystąpienia może również służyć do operacji zapisu.

Oto *function.json* pliku:

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

[Pomiń przykłady danych wejściowych](#input---attributes)

### <a name="input---javascript-examples"></a>Dane wejściowe — przykłady kodu JavaScript

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz kolejki, odnośnika identyfikator z formatu JSON](#queue-trigger-look-up-id-from-json-javascript)
* [Wyzwalacz HTTP, Sprawdź identyfikator z ciągu zapytania](#http-trigger-look-up-id-from-query-string-javascript)
* [Wyzwalacz HTTP, wyszukiwania Identyfikatora z danych trasy](#http-trigger-look-up-id-from-route-data-javascript)
* [Wyzwalacz kolejki, Uzyskaj dokumentację wielu, za pomocą SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-javascript"></a>Wyzwalacz kolejki, odnośnika identyfikator z formatu JSON (JavaScript)

Poniższy przykład pokazuje usługi Cosmos DB, powiązania danych wejściowych w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja odczytuje pojedynczy dokument i aktualizuje wartość tekstu dokumentu.

Oto powiązanie danych w *function.json* pliku:

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
[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod JavaScript:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-javascript"></a>Wyzwalacz HTTP, Sprawdź identyfikator z ciągu zapytania (JavaScript)

W poniższym przykładzie przedstawiono [funkcji JavaScript](functions-reference-node.md) , który pobiera pojedynczego dokumentu. Funkcja jest wyzwalana przez żądanie HTTP, która wykorzystuje ciąg kwerendy do określenia Identyfikatora do wyszukania. Identyfikator służy do pobierania `ToDoItem` dokument z określonej bazy danych i kolekcji.

Oto *function.json* pliku:

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

[Pomiń przykłady danych wejściowych](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-javascript"></a>Wyzwalacz HTTP, wyszukiwania Identyfikatora z danych trasy (JavaScript)

W poniższym przykładzie przedstawiono [funkcji JavaScript](functions-reference-node.md) , który pobiera pojedynczego dokumentu. Funkcja jest wyzwalana przez żądanie HTTP, która wykorzystuje ciąg kwerendy do określenia Identyfikatora do wyszukania. Identyfikator służy do pobierania `ToDoItem` dokument z określonej bazy danych i kolekcji.

Oto *function.json* pliku:

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

[Pomiń przykłady danych wejściowych](#input---attributes)



#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>Wyzwalacz kolejki, Uzyskaj dokumentację wielu, za pomocą SqlQuery (JavaScript)

W poniższym przykładzie pokazano powiązania danych wejściowych usługi Azure Cosmos DB w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja pobiera wiele dokumentów, określonych przez zapytanie SQL, przy użyciu wyzwalacza kolejki, aby dostosować parametry zapytania.

Wyzwalacz kolejki zawiera parametr `departmentId`. Komunikat kolejki `{ "departmentId" : "Finance" }` zwróci wszystkie rekordy dla działu finansowego.

Oto powiązanie danych w *function.json* pliku:

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

[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

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

[Pomiń przykłady danych wejściowych](#input---attributes)

<a name="infsharp"></a>

### <a name="input---f-examples"></a>Dane wejściowe - F# przykłady

Poniższy przykład pokazuje usługi Cosmos DB, powiązania danych wejściowych w *function.json* pliku i [ F# funkcja](functions-reference-fsharp.md) powiązania, który używa. Funkcja odczytuje pojedynczy dokument i aktualizuje wartość tekstu dokumentu.

Oto powiązanie danych w *function.json* pliku:

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
}
```

[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Oto F# kodu:

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

Aby dodać `project.json` plików, zobacz [ F# Zarządzanie pakietami](functions-reference-fsharp.md#package).

## <a name="input---attributes"></a>Dane wejściowe — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) atrybutu.

Konstruktor atrybutu ma nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i inne właściwości, które można skonfigurować, zobacz [w poniższej sekcji konfiguracji](#input---configuration).

## <a name="input---configuration"></a>Dane wejściowe — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `DocumentDB` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type**     || Musi być równa `documentdb`.        |
|**direction**     || Musi być równa `in`.         |
|**Nazwa**     || Nazwa parametru powiązania, który reprezentuje dokument w funkcji.  |
|**databaseName** |**DatabaseName** |Baza danych zawiera dokument.        |
|**collectionName** |**collectionName** | Nazwa kolekcji, która zawiera dokument. |
|**id**    | **Identyfikator** | Identyfikator dokumentu do pobrania. Ta właściwość obsługuje [powiązania wyrażeń](./functions-bindings-expressions-patterns.md). Nie należy ustawiać zarówno **identyfikator** i **sqlQuery** właściwości. Jeśli nie ustawisz pojedynczo, zostanie pobrana całą kolekcję. |
|**sqlQuery**  |**SqlQuery**  | Zapytania SQL usługi Azure Cosmos DB używane do pobierania wiele dokumentów. Właściwość obsługuje powiązań środowiska uruchomieniowego, jak w poniższym przykładzie: `SELECT * FROM c where c.departmentId = {departmentId}`. Nie należy ustawiać zarówno **identyfikator** i **sqlQuery** właściwości. Jeśli nie ustawisz pojedynczo, zostanie pobrana całą kolekcję.|
|**połączenia**     |**ConnectionStringSetting**|Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi Azure Cosmos DB.        |
|**właściwości partitionKey**|**właściwości partitionKey**|Określa wartość klucza partycji do wyszukiwania. Może zawierać parametrów wiązania.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Dane wejściowe — użycie

W C# i F# funkcji, jeśli funkcja kończy działanie pomyślnie, wszelkie zmiany wprowadzone do dokument wejściowy za pośrednictwem danych wejściowych o nazwie parametry są automatycznie zachowywane.

W funkcji języka JavaScript nie zostało zaktualizowane automatycznie po wyjściu z funkcji. Zamiast tego należy użyć `context.bindings.<documentName>In` i `context.bindings.<documentName>Out` Aby wprowadzić aktualizacje. Zobacz [przykład JavaScript](#input---javascript-examples).

## <a name="output"></a>Dane wyjściowe

Dane wyjściowe usługi Azure Cosmos DB, powiązania pozwala zapisać nowy dokument z bazą danych Azure Cosmos DB przy użyciu interfejsu API SQL.

## <a name="output---examples"></a>Dane wyjściowe — przykłady

Zobacz przykłady specyficzny dla języka:

* [C#](#output---c-examples)
* [Skryptu C# (csx)](#output---c-script-examples)
* [JavaScript](#output---javascript-examples)
* [F#](#output---f-examples)

Zobacz też [wejściowych przykład](#input---c-examples) , który używa `DocumentClient`.

[Pomiń przykładowe dane wyjściowe](#output---attributes)

### <a name="output---c-examples"></a>Dane wyjściowe — C# przykłady

Ta sekcja zawiera następujące przykłady:

* Wyzwalacz kolejki, doc jeden zapisu
* Wyzwalacz kolejki, dokumentacja zapisu przy użyciu IAsyncCollector

Przykłady dotyczą prostego `ToDoItem` typu:

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

[Pomiń przykładowe dane wyjściowe](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c"></a>Wyzwalacz kolejki, doc jeden zapis (C#)

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , dodanie dokumentu do bazy danych przy użyciu danych podany w wiadomości z usługi Queue storage.

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

[Pomiń przykładowe dane wyjściowe](#output---attributes)

#### <a name="queue-trigger-write-docs-using-iasynccollector-c"></a>Wyzwalacz kolejki, dokumentacja zapisu przy użyciu IAsyncCollector (C#)

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) kolekcji dokumentów, który dodaje w bazie danych, przy użyciu danych w komunikatu w kolejce JSON.

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

[Pomiń przykładowe dane wyjściowe](#output---attributes)

### <a name="output---c-script-examples"></a>Dane wyjściowe — przykłady skryptów w języku C#

Ta sekcja zawiera następujące przykłady:

* Wyzwalacz kolejki, doc jeden zapisu
* Wyzwalacz kolejki, dokumentacja zapisu przy użyciu IAsyncCollector

[Pomiń przykładowe dane wyjściowe](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c-script"></a>Wyzwalacz kolejki, doc jeden zapisu (skrypt języka C#)

W poniższym przykładzie pokazano powiązania w danych wyjściowych usługi Azure Cosmos DB *function.json* pliku i [funkcji skryptu w języku C#](functions-reference-csharp.md) powiązania, który używa. Funkcja używa powiązania danych wejściowych w kolejce dla kolejki, która odbiera JSON w następującym formacie:

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

Oto powiązanie danych w *function.json* pliku:

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

[Konfiguracji](#output---configuration) sekcji opisano te właściwości.

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

#### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wyzwalacz kolejki, dokumentacja zapisu przy użyciu IAsyncCollector

Aby utworzyć wiele dokumentów, można powiązać `ICollector<T>` lub `IAsyncCollector<T>` gdzie `T` jest jednym z obsługiwanych typów.

Ten przykład dotyczy prostego `ToDoItem` typu:

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

[Pomiń przykładowe dane wyjściowe](#output---attributes)

### <a name="output---javascript-examples"></a>Dane wyjściowe — przykłady kodu JavaScript

W poniższym przykładzie pokazano powiązania w danych wyjściowych usługi Azure Cosmos DB *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja używa powiązania danych wejściowych w kolejce dla kolejki, która odbiera JSON w następującym formacie:

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

Oto powiązanie danych w *function.json* pliku:

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

[Konfiguracji](#output---configuration) sekcji opisano te właściwości.

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

[Pomiń przykładowe dane wyjściowe](#output---attributes)

### <a name="output---f-examples"></a>Dane wyjściowe — F# przykłady

W poniższym przykładzie pokazano powiązania w danych wyjściowych usługi Azure Cosmos DB *function.json* pliku i [ F# funkcja](functions-reference-fsharp.md) powiązania, który używa. Funkcja używa powiązania danych wejściowych w kolejce dla kolejki, która odbiera JSON w następującym formacie:

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

Oto powiązanie danych w *function.json* pliku:

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
[Konfiguracji](#output---configuration) sekcji opisano te właściwości.

Oto F# kodu:

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

Aby dodać `project.json` plików, zobacz [ F# Zarządzanie pakietami](functions-reference-fsharp.md#package).

## <a name="output---attributes"></a>Dane wyjściowe — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) atrybutu.

Konstruktor atrybutu ma nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i inne właściwości, które można skonfigurować, zobacz [dane wyjściowe — Konfiguracja](#output---configuration). Oto `DocumentDB` przykład atrybutu w podpisie metody:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Aby uzyskać kompletny przykład, zobacz [dane wyjściowe — przykład w języku C#](#output---c-examples).

## <a name="output---configuration"></a>Dane wyjściowe — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `DocumentDB` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type**     || Musi być równa `documentdb`.        |
|**direction**     || Musi być równa `out`.         |
|**Nazwa**     || Nazwa parametru powiązania, który reprezentuje dokument w funkcji.  |
|**databaseName** | **DatabaseName**|Baza danych, zawierający kolekcję, w którym zostanie utworzona dokumentu.     |
|**collectionName** |**collectionName**  | Nazwa kolekcji jest tworzona dokumentu. |
|**createIfNotExists**  |**createIfNotExists**    | Wartość logiczna, aby wskazać, czy kolekcja jest tworzona, gdy nie istnieje. Wartość domyślna to *false* ponieważ nowych kolekcji są tworzone z zarezerwowaną przepływnością, co ma koszt skutki. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**właściwości partitionKey**|**właściwości partitionKey** |Gdy `CreateIfNotExists` ma wartość true, określa ścieżkę klucza partycji dla utworzonej kolekcji.|
|**collectionThroughput**|**collectionThroughput**| Gdy `CreateIfNotExists` ma wartość true, określa [przepływności](../cosmos-db/set-throughput.md) utworzonej kolekcji.|
|**połączenia**    |**ConnectionStringSetting** |Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

Domyślnie podczas zapisu do parametru wyjściowego w funkcji, tworzony jest dokument w bazie danych. Ten dokument ma automatycznie generowanym identyfikatorze GUID jako identyfikatora dokumentu. Identyfikator dokumentu dokumentu wyjściowego można określić, podając `id` właściwości w obiekcie JSON przekazanego do parametru wyjściowego.

> [!Note]
> Po określeniu identyfikator istniejącego dokumentu, pobiera to zastąpione przez nowy dokument danych wyjściowych.

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie | Informacje ogólne |
|---|---|
| CosmosDB | [Kody błędów CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej o bazie danych bez użycia serwera, obliczeń w usłudze Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
