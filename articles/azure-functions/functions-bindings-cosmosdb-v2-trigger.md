---
title: Wyzwalacz Azure Cosmos DB dla funkcji 2. x
description: Dowiedz się, jak używać wyzwalacza Azure Cosmos DB w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: de8ad39ef731af3dc272d700eeee346acda64b53
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277572"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x"></a>Wyzwalacz Azure Cosmos DB dla Azure Functions 2. x

Wyzwalacz Azure Cosmos DB używa [źródła zmian Azure Cosmos DB](../cosmos-db/change-feed.md) do nasłuchiwania operacji wstawiania i aktualizacji między partycjami. Kanał informacyjny zmian publikuje wstawienia i aktualizacje, nie do usunięcia.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C#Napisy](#tab/csharp-script)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="java"></a>[Java](#tab/java)

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

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj atrybutu [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) .

Konstruktor atrybutu ma nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i innych właściwościach, które można skonfigurować, zobacz [wyzwalacz-konfiguracja](#configuration). Oto przykład atrybutu `CosmosDBTrigger` w sygnaturze metody:

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

Pełny przykład można znaleźć w temacie [Trigger](#example).

# <a name="c-script"></a>[C#Napisy](#tab/csharp-script)

Atrybuty nie są obsługiwane przez C# skrypt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="java"></a>[Java](#tab/java)

W [bibliotece środowiska uruchomieniowego usługi Java Functions](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)Użyj adnotacji `@CosmosDBInput` dla parametrów, które odczytują dane z Cosmos DB.

---

## <a name="configuration"></a>Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* i `CosmosDBTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | Musi być ustawiony na `cosmosDBTrigger`. |
|**direction** | Nie dotyczy | Musi być ustawiony na `in`. Ten parametr jest ustawiany automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. |
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
|**feedPollDelay**| **FeedPollDelay**| Obowiązkowe Czas (w milisekundach) oczekiwania między sondowaniem partycji o nowe zmiany w strumieniu, po opróżnieniu wszystkich bieżących zmian. Wartość domyślna to 5 000 milisekund lub 5 sekund.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał można uruchamiać zadania obliczeniowe, jeśli partycje są dystrybuowane równomiernie między wystąpieniami znanych hostów. Wartość domyślna to 13000 (w sekundach 13).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał, dla której dzierżawy jest pobierany podczas dzierżawy, reprezentujący partycji. Jeśli dzierżawa nie zostanie odnowiony w tym przedziale czasu, spowoduje jego wygaśnięcia i własność partycji przejdzie do innego wystąpienia. Domyślna to 60 000 (60 sekund).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał odnawiania dla wszystkich dzierżaw dla partycji aktualnie trzymana przez wystąpienie. Wartość domyślna to 17000 (17 w sekundach).
|**checkpointFrequency**| **CheckpointFrequency**| (Opcjonalnie) Po ustawieniu definiuje, w milisekundach, interwał między punktami kontrolnymi dzierżawy. Wartość domyślna to zawsze po każdym wywołaniu funkcji.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Obowiązkowe Po ustawieniu ta właściwość ustawia maksymalną liczbę elementów odebranych na wywołanie funkcji. Jeśli operacje w monitorowanej kolekcji są wykonywane za pomocą procedur składowanych, [zakres transakcji](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) jest zachowywany podczas odczytywania elementów ze źródła zmian. W związku z tym liczba odebranych elementów może być wyższa niż określona wartość, dzięki czemu elementy zmienione przez tę samą transakcję są zwracane jako część jednej niepodzielnej partii.
|**startFromBeginning**| **StartFromBeginning**| Obowiązkowe Ta opcja informuje wyzwalacz, aby odczytał zmiany od początku historii zmian kolekcji zamiast od bieżącego czasu. Odczyt od początku działa tylko podczas pierwszego uruchomienia wyzwalacza, jak w kolejnych uruchomieniach, punkty kontrolne są już przechowywane. Ustawienie tej opcji na `true`, gdy istnieją już utworzone dzierżawy, nie mają żadnego efektu. |
|**preferredLocations**| **PreferredLocations**| Obowiązkowe Definiuje preferowane lokalizacje (regiony) dla kont bazy danych replikowanych geograficznie w usłudze Azure Cosmos DB. Wartości powinny być rozdzielane przecinkami. Na przykład "Wschodnie stany USA, Południowo-środkowe stany USA, Europa Północna". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Sposób użycia

Wyzwalacz wymaga drugiej kolekcji, która używa do przechowywania _dzierżaw_ w ramach partycji. Kolekcja monitorowanych i kolekcji, która zawiera dzierżaw musi być dostępny dla wyzwalacza do pracy.

>[!IMPORTANT]
> Jeśli wiele funkcji jest skonfigurowanych do korzystania z wyzwalacza Cosmos DB dla tej samej kolekcji, każda z tych funkcji powinna używać dedykowanej kolekcji dzierżaw lub określić inną `LeaseCollectionPrefix` dla każdej funkcji. W przeciwnym razie tylko jeden funkcje zostaną wyzwolone. Informacje na temat prefiksu znajdują się w [sekcji Konfiguracja](#configuration).

Wyzwalacz nie wskazuje, czy zaktualizowane lub wstawić dokumentu, po prostu zapewnia samego dokumentu. Jeśli wymagana jest obsługa aktualizacje i wstawienia w różny sposób, możesz można zrobić, implementując pola sygnatury czasowej dla wstawiania lub aktualizacji.

## <a name="next-steps"></a>Następne kroki

- [Odczytaj dokument Azure Cosmos DB (powiązanie danych wejściowych)](./functions-bindings-cosmosdb-v2-input.md)
- [Zapisz zmiany w dokumencie Azure Cosmos DB (powiązanie danych wyjściowych)](./functions-bindings-cosmosdb-v2-output.md)