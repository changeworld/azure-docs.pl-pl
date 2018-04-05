---
title: Azure DB rozwiązania Cosmos powiązania dla funkcji
description: Zrozumienie sposobu korzystania z bazy danych Azure rozwiązania Cosmos wyzwalaczy i powiązań w funkcji platformy Azure.
services: functions
documentationcenter: na
author: ggailey777
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
ms.author: glenga
ms.openlocfilehash: ac869cc45d352bdeed16bb3ca926ec7a921d1f75
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="azure-cosmos-db-bindings-for-azure-functions"></a>Azure DB rozwiązania Cosmos powiązania dla usługi Azure Functions

W tym artykule opisano sposób pracy z [bazy danych Azure rozwiązania Cosmos](..\cosmos-db\serverless-computing-database.md) powiązania usługi Azure Functions. Usługi Azure Functions obsługuje wyzwolenia, danych wejściowych i wyjściowych powiązania dla bazy danych Azure rozwiązania Cosmos.

> [!NOTE]
> To powiązanie pierwotnie nosiła nazwę usługi DocumentDB. Funkcje wersji 1.x, tylko wyzwalacz została zmieniona DB rozwiązania Cosmos; powiązania wejściowego, powiązania wyjściowego i pakiet NuGet zachować nazwy usługi DocumentDB. W [funkcje wersji 2.x](functions-versions.md), powiązania i pakietu zostały również zmienionej nazwie DB rozwiązania Cosmos. W tym artykule używa nazwy 1.x.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Pakiety

Powiązania rozwiązania Cosmos bazy danych dla funkcji wersja 1.x znajdują się w [Microsoft.Azure.WebJobs.Extensions.DocumentDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) pakietu NuGet. Dla funkcji 2.x, że pakiet jest [Microsoft.Azure.WebJobs.Extensions.CosmosDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB). Kod źródłowy dla powiązania jest w [azure-zadań webjob sdk rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) repozytorium GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Wyzwalacz

Wyzwalacz DB rozwiązania Cosmos Azure wykorzystuje [Azure rozwiązania Cosmos DB zmiany źródła danych](../cosmos-db/change-feed.md) do nasłuchiwania zmian w partycji. Zmiana źródła strumieniowego publikuje wstawia i aktualizacji i usunięć nie. 

## <a name="trigger---example"></a>Wyzwalacz — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [C#](#trigger---c-example)
* [Skryptu C# (csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Wyzwalacz — przykład C#

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) która wyzwala z określonej bazy danych i kolekcji.

```cs
    using System.Collections.Generic;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
            log.Info("Documents modified " + documents.Count);
            log.Info("First document Id " + documents[0].Id);
    }
```

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
|**Typ** || należy wybrać opcję `cosmosDBTrigger`. |
|**Kierunek** || należy wybrać opcję `in`. Ten parametr jest ustawiany automatycznie, podczas tworzenia wyzwalacza w portalu Azure. |
|**Nazwa** || Nazwa zmiennej używany w funkcji kod, który reprezentuje listy dokumentów o zmiany. | 
|**connectionStringSetting**|**ConnectionStringSetting** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia używane do nawiązania połączenia konta bazy danych Azure rozwiązania Cosmos monitorowane. |
|**databaseName**|**DatabaseName**  | Nazwa bazy danych DB rozwiązania Cosmos Azure z tą kolekcją monitorowane. |
|**CollectionName** |**CollectionName** | Nazwa kolekcji monitorowane. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Opcjonalnie) Nazwa ustawienia aplikacji, które zawiera parametry połączenia do usługi, która posiada kolekcji dzierżawy. Gdy nie są ustawione, `connectionStringSetting` wartość jest używana. Ten parametr ma wartość automatycznie, podczas tworzenia powiązania w portalu. Parametry połączenia dla kolekcji dzierżawy musi mieć uprawnienia do zapisu.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Opcjonalnie) Nazwa bazy danych, która przechowuje kolekcji używany do przechowywania dzierżawy. Gdy nie są ustawione, wartość `databaseName` ustawienie jest używane. Ten parametr ma wartość automatycznie, podczas tworzenia powiązania w portalu. |
|**leaseCollectionName** | **LeaseCollectionName** | (Opcjonalnie) Nazwa kolekcji używany do przechowywania dzierżawy. Gdy nie są ustawione, wartość `leases` jest używany. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Opcjonalnie) Jeśli wartość `true`, kolekcji dzierżawy jest tworzony automatycznie podczas już nie istnieje. Wartość domyślna to `false`. |
|**LeasesCollectionThroughput**| **LeasesCollectionThroughput**| (Opcjonalnie) Definiuje liczbę jednostek żądań można przypisać podczas tworzenia kolekcji dzierżawy. To ustawienie jest tylko do użycia podczas `createLeaseCollectionIfNotExists` ma ustawioną wartość `true`. Ten parametr ma wartość automatycznie, podczas tworzenia powiązania za pomocą portalu.
| |**LeaseOptions** | Skonfiguruj opcje dzierżawy przez ustawienie właściwości w wystąpieniu [ChangeFeedHostOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.changefeedprocessor.changefeedhostoptions) klasy.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Wyzwalacz — użycie

Druga kolekcja, która jest używana do przechowywania wymaga wyzwalacza _dzierżawy_ na partycje. Zarówno kolekcji monitorowane i kolekcji, która zawiera dzierżawy musi być dostępny dla wyzwalacza do pracy.

 >[!IMPORTANT]
 > Jeśli wiele funkcji są skonfigurowane do używania wyzwalacz rozwiązania Cosmos bazy danych dla tej samej kolekcji, każdej funkcji należy używać kolekcji dedykowanym dzierżawy. W przeciwnym razie tylko jeden funkcji zostanie wyzwolone. 

Wyzwalacz nie wskazują, czy zaktualizowane lub wstawić dokumentu, tylko zawiera samego dokumentu. Jeśli wymagana jest obsługa aktualizacji i wstawia inaczej, możesz to zrobić z zastosowaniem pola sygnatury czasowej do wstawienia lub aktualizacji.

## <a name="input"></a>Dane wejściowe

Powiązania wejściowego bazy danych Azure rozwiązania Cosmos pobiera jeden lub więcej dokumentów bazy danych Azure rozwiązania Cosmos i przekazuje je do parametr wejściowy funkcji. Można określić parametry identyfikator lub kwerendę dokumentu oparte na wyzwalacz, który wywołuje funkcję. 

>[!NOTE]
> Nie używasz bazy danych Azure rozwiązania Cosmos w danych wejściowych lub wyjściowych powiązań, jeśli używasz bazy danych MongoDB interfejsu API na koncie DB rozwiązania Cosmos. Możliwe jest uszkodzenia danych.

## <a name="input---example-1"></a>Dane wejściowe — przykład 1

Zapoznaj się z przykładem specyficzny dla języka, która odczytuje pojedynczego dokumentu:

* [C#](#input---c-example)
* [Skryptu C# (csx)](#input---c-script-example)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example"></a>Dane wejściowe — przykład C#

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) który pobiera jeden dokument z określoną bazę danych i kolekcji. 

Najpierw `Id` i `Maker` wartości `CarReview` wystąpienia są przekazywane do kolejki. DB rozwiązania Cosmos wiązanie `Id` i `Maker` z kolejki komunikatów w celu pobrania dokumentu z bazy danych.

```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    namespace CosmosDB
    {
        public static class SingleEntry
        {
            [FunctionName("SingleEntry")]
            public static void Run(
                [QueueTrigger("car-reviews", Connection = "StorageConnectionString")] CarReview carReview,
                [DocumentDB("cars", "car-reviews", PartitionKey = "{maker}", Id= "{id}", ConnectionStringSetting = "CarReviewsConnectionString")] CarReview document,
                TraceWriter log)
            {
                log.Info( $"Selected Review - {document?.Review}"); 
            }
        }
    }
```

Oto `CarReview` POCO:

 ```cs
    public class CarReview
    {
        public string Id { get; set; }
        public string Maker { get; set; }
        public string Description { get; set; }
        public string Model { get; set; }
        public string Image { get; set; }
        public string Review { get; set; }
    }
 ```

### <a name="input---c-script-example"></a>Dane wejściowe — przykładowy skrypt w języku C#

W poniższym przykładzie przedstawiono powiązania wejściowego DB rozwiązania Cosmos w *function.json* pliku i [funkcji skryptu C#](functions-reference-csharp.md) używającą powiązania. Funkcja odczytuje pojedynczego dokumentu i aktualizuje wartości tekstowej dokumentu.

W tym miejscu jest powiązanie danych *function.json* pliku:

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

Oto kod skryptu C#:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding 
    public static void Run(string myQueueItem, dynamic inputDocument)
    {   
      inputDocument.text = "This has changed.";
    }
```

<a name="infsharp"></a>

### <a name="input---f-example"></a>Dane wejściowe — przykład F #

W poniższym przykładzie przedstawiono powiązania wejściowego DB rozwiązania Cosmos w *function.json* pliku i [F # funkcja](functions-reference-fsharp.md) używającą powiązania. Funkcja odczytuje pojedynczego dokumentu i aktualizuje wartości tekstowej dokumentu.

W tym miejscu jest powiązanie danych *function.json* pliku:

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

### <a name="input---javascript-example"></a>Dane wejściowe — przykład JavaScript

W poniższym przykładzie przedstawiono powiązania wejściowego DB rozwiązania Cosmos w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) używającą powiązania. Funkcja odczytuje pojedynczego dokumentu i aktualizuje wartości tekstowej dokumentu.

W tym miejscu jest powiązanie danych *function.json* pliku:

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

Oto kod JavaScript:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {   
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

## <a name="input---example-2"></a>Dane wejściowe — przykład 2

Zapoznaj się z przykładem specyficzny dla języka, które odczytuje wiele dokumentów:

* [C#](#input---c-example-2)
* [Skryptu C# (csx)](#input---c-script-example-2)
* [JavaScript](#input---javascript-example-2)

### <a name="input---c-example-2"></a>Dane wejściowe — przykład C# 2

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) który wykonuje zapytanie SQL. Aby użyć `SqlQuery` parametru, musisz zainstalować najnowszą wersję beta `Microsoft.Azure.WebJobs.Extensions.DocumentDB` pakietu NuGet.

```csharp
    using System.Net;
    using System.Net.Http;
    using System.Collections.Generic;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;

    [FunctionName("CosmosDBSample")]
    public static HttpResponseMessage Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get")] HttpRequestMessage req,
        [DocumentDB("test", "test", ConnectionStringSetting = "CosmosDB", SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")] IEnumerable<object> documents)
    {
        return req.CreateResponse(HttpStatusCode.OK, documents);
    }
```

### <a name="input---c-script-example-2"></a>Dane wejściowe - C# przykładowy skrypt 2

W poniższym przykładzie przedstawiono powiązania wejściowego bazy danych Azure rozwiązania Cosmos w *function.json* pliku i [funkcji skryptu C#](functions-reference-csharp.md) używającą powiązania. Funkcja pobiera wielu dokumentów określonych przez zapytanie SQL, aby dostosować parametry zapytania przy użyciu wyzwalacza kolejki.

Wyzwalacz kolejki zawiera parametr `departmentId`. Komunikat z kolejki z `{ "departmentId" : "Finance" }` zwróci wszystkie rekordy dotyczące działu finansowego. 

W tym miejscu jest powiązanie danych *function.json* pliku:

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

### <a name="input---javascript-example-2"></a>Dane wejściowe — przykład JavaScript 2

W poniższym przykładzie przedstawiono powiązania wejściowego bazy danych Azure rozwiązania Cosmos w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) używającą powiązania. Funkcja pobiera wielu dokumentów określonych przez zapytanie SQL, aby dostosować parametry zapytania przy użyciu wyzwalacza kolejki.

Wyzwalacz kolejki zawiera parametr `departmentId`. Komunikat z kolejki z `{ "departmentId" : "Finance" }` zwróci wszystkie rekordy dotyczące działu finansowego. 

W tym miejscu jest powiązanie danych *function.json* pliku:

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

## <a name="input---attributes"></a>Dane wejściowe — atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) atrybutu.

Konstruktor atrybutu ma nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i inne właściwości, które można skonfigurować, zobacz [następującą sekcję konfiguracji](#input---configuration). 

## <a name="input---configuration"></a>Dane wejściowe — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `DocumentDB` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typ**     || należy wybrać opcję `documentdb`.        |
|**Kierunek**     || należy wybrać opcję `in`.         |
|**Nazwa**     || Nazwa parametru powiązania, który reprezentuje dokument w funkcji.  |
|**databaseName** |**DatabaseName** |Baza danych zawierająca dokumentu.        |
|**CollectionName** |**CollectionName** | Nazwa kolekcji, która zawiera dokument. |
|**id**    | **Identyfikator** | Identyfikator dokumentu do pobrania. Ta właściwość obsługuje [wyrażenia powiązania](functions-triggers-bindings.md#binding-expressions-and-patterns). Nie należy ustawiać zarówno **identyfikator** i **sqlQuery** właściwości. Jeśli nie zostanie ustawiona jedną, są pobierane całą kolekcję. |
|**sqlQuery**  |**SqlQuery**  | Zapytanie Azure rozwiązania Cosmos bazy danych SQL, używane do pobierania wielu dokumentów. Właściwość obsługuje powiązań czasu wykonywania, jak w poniższym przykładzie: `SELECT * FROM c where c.departmentId = {departmentId}`. Nie należy ustawiać zarówno **identyfikator** i **sqlQuery** właściwości. Jeśli nie zostanie ustawiona jedną, są pobierane całą kolekcję.|
|**Połączenia**     |**ConnectionStringSetting**|Nazwa ustawienia aplikacji zawierający parametry połączenia bazy danych Azure rozwiązania Cosmos.        |
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

* [C#](#output---c-example)
* [Skryptu C# (csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Dane wyjściowe — przykład C#

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) dodaje dokumentu do bazy danych, przy użyciu danych zawartych w wiadomości z kolejki magazynu.

```cs
    using System;
    using Microsoft.Azure.WebJobs;

    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        document = new { Text = myQueueItem, id = Guid.NewGuid() };
    }
```

### <a name="output---c-script-example"></a>Dane wyjściowe — przykładowy skrypt w języku C#

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
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

### <a name="output---f-example"></a>Dane wyjściowe — przykład F #

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
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

### <a name="output---javascript-example"></a>Dane wyjściowe — przykład JavaScript

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
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

## <a name="output---attributes"></a>Dane wyjściowe — atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) atrybutu.

Konstruktor atrybutu ma nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i inne właściwości, które można skonfigurować, zobacz [wyjście - konfiguracji](#output---configuration). Oto `DocumentDB` przykład atrybutu w podpisie metody:

```csharp
    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Pełny przykład, zobacz [dane wyjściowe — przykład C#](#output---c-example).

## <a name="output---configuration"></a>OUTPUT — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `DocumentDB` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typ**     || należy wybrać opcję `documentdb`.        |
|**Kierunek**     || należy wybrać opcję `out`.         |
|**Nazwa**     || Nazwa parametru powiązania, który reprezentuje dokument w funkcji.  |
|**databaseName** | **DatabaseName**|Baza danych zawierający kolekcję, do której jest tworzony dokumentu.     |
|**CollectionName** |**CollectionName**  | Nazwa kolekcji, których tworzone jest dokumentu. |
|**CreateIfNotExists**  |**CreateIfNotExists**    | Wartość logiczna wskazująca, czy kolekcja jest tworzony, gdy nie istnieje. Wartość domyślna to *false* ponieważ nowe kolekcje są tworzone z zarezerwowaną przepływnością, co ma koszt skutki. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**PartitionKey**|**PartitionKey** |Gdy `CreateIfNotExists` ma wartość true, określa ścieżkę klucza partycji dla utworzonej kolekcji.|
|**CollectionThroughput**|**CollectionThroughput**| Gdy `CreateIfNotExists` ma wartość true, określa [przepływności](../cosmos-db/set-throughput.md) utworzonej kolekcji.|
|**Połączenia**    |**ConnectionStringSetting** |Nazwa ustawienia aplikacji zawierający parametry połączenia bazy danych Azure rozwiązania Cosmos.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

Domyślnie podczas zapisywania do parametru wyjściowego w funkcji, dokument jest tworzony w bazie danych. Ten dokument ma automatycznie generowanym identyfikatorze GUID jako identyfikatora dokumentu. Identyfikator dokumentu dokumentu wyjściowego można określić, podając `id` właściwości w obiekcie JSON przekazanego do parametru wyjściowego. 

> [!Note]  
> Po określeniu Identyfikatora istniejącego dokumentu pobiera zastąpione przez nowy dokument danych wyjściowych. 

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie | Informacje ogólne |
|---|---|
| CosmosDB | [Kody błędów CosmosDB](https://docs.microsoft.com/en-us/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przejdź do szybkiego startu, używającej wyzwalacz DB rozwiązania Cosmos](functions-create-cosmos-db-triggered-function.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o bez serwera bazy danych, przetwarzania danych z bazy danych rozwiązania Cosmos](..\cosmos-db\serverless-computing-database.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o usługę Azure functions wyzwalaczy i powiązań](functions-triggers-bindings.md)
