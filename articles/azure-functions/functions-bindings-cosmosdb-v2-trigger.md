---
title: Wyzwalacz usługi Azure Cosmos DB dla funkcji 2.x
description: Dowiedz się, jak używać wyzwalacza usługi Azure Cosmos DB w usłudze Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: de8ad39ef731af3dc272d700eeee346acda64b53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277572"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x"></a>Wyzwalacz usługi Azure Cosmos DB dla usługi Azure Functions 2.x

Wyzwalacz usługi Azure Cosmos DB używa [kanału informacyjnego usługi Azure Cosmos DB Change Feed](../cosmos-db/change-feed.md) do nasłuchiwać wstawia i aktualizacji między partycjami. Kanał zmian publikuje wstawia i aktualizuje, a nie usuwa.

Aby uzyskać informacje na temat szczegółów konfiguracji i konfiguracji, zobacz [omówienie](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

Poniższy przykład pokazuje [funkcję Języka C#,](functions-dotnet-class-library.md) która jest wywoływana, gdy istnieją wstawia lub aktualizacje w określonej bazie danych i kolekcji.

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

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

W poniższym przykładzie pokazano powiązanie wyzwalacza usługi Cosmos DB w pliku *function.json* i [funkcję skryptu Języka C#,](functions-reference-csharp.md) która używa powiązania. Funkcja zapisuje komunikaty dziennika, gdy rekordy usługi Cosmos DB są dodawane lub modyfikowane.

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza usługi Cosmos DB w pliku *function.json* i [funkcję JavaScript,](functions-reference-node.md) która używa powiązania. Funkcja zapisuje komunikaty dziennika, gdy rekordy usługi Cosmos DB są dodawane lub modyfikowane.

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

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano powiązanie wyzwalacza usługi Cosmos DB w pliku *function.json* i [funkcję Języka Python,](functions-reference-python.md) która używa powiązania. Funkcja zapisuje komunikaty dziennika, gdy rekordy usługi Cosmos DB są modyfikowane.

Oto dane powiązania w pliku *function.json:*

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

Oto kod Pythona:

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

# <a name="java"></a>[Java](#tab/java)

Ta funkcja jest wywoływana, gdy istnieją wstawia lub aktualizacje w określonej bazie danych i kolekcji.

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


W [bibliotece środowiska wykonawczego](/java/api/overview/azure/functions/runtime)funkcji `@CosmosDBTrigger` Java użyj adnotacji na temat parametrów, których wartość pochodziłaby z usługi Cosmos DB.  Ta adnotacja może być używana z natywnymi typami `Optional<T>`Java, POJO lub wartościami nullable przy użyciu .

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C #](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [CosmosDBTrigger.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs)

Konstruktor atrybutu przyjmuje nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i innych właściwościach, które można skonfigurować, zobacz [Wyzwalacz — konfiguracja](#configuration). Oto przykład `CosmosDBTrigger` atrybutu w podpisie metody:

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

Aby uzyskać pełny przykład, zobacz [Wyzwalacz](#example).

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez Pythona.

# <a name="java"></a>[Java](#tab/java)

Z [biblioteki środowiska wykonawczego](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)funkcji `@CosmosDBInput` Java użyj adnotacji na temat parametrów odczytu danych z usługi Cosmos DB.

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `CosmosDBTrigger` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typu** | Nie dotyczy | Musi być `cosmosDBTrigger`ustawiona na . |
|**Kierunku** | Nie dotyczy | Musi być `in`ustawiona na . Ten parametr jest ustawiany automatycznie podczas tworzenia wyzwalacza w witrynie Azure portal. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej używana w kodzie funkcji reprezentująca listę dokumentów ze zmianami. |
|**connectionStringSetting**|**ConnectionStringSetting (Ustawienia połączeń)** | Nazwa ustawienia aplikacji, który zawiera parametry połączenia używane do łączenia się z kontem usługi Azure Cosmos DB monitorowane. |
|**Databasename**|**DatabaseName**  | Nazwa bazy danych usługi Azure Cosmos DB z kolekcji są monitorowane. |
|**Collectionname** |**CollectionName** | Nazwa monitorowanej kolekcji. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Opcjonalnie) Nazwa ustawienia aplikacji, który zawiera parametry połączenia z kontem usługi Azure Cosmos DB, który posiada kolekcji dzierżawy. Gdy nie jest `connectionStringSetting` ustawiona, używana jest wartość. Ten parametr jest ustawiany automatycznie podczas tworzenia powiązania w portalu. Parametry połączenia dla kolekcji dzierżaw muszą mieć uprawnienia do zapisu.|
|**leaseDatabaseName** |**LeaseDatabaseName (Nazwa danych umowy dzierżawy)** | (Opcjonalnie) Nazwa bazy danych, która przechowuje kolekcję używaną do przechowywania dzierżaw. Gdy nie jest ustawiona, używana jest wartość `databaseName` ustawienia. Ten parametr jest ustawiany automatycznie podczas tworzenia powiązania w portalu. |
|**leaseCollectionName** | **LeaseCollectionName** | (Opcjonalnie) Nazwa kolekcji używanej do przechowywania dzierżaw. Gdy nie jest `leases` ustawiona, używana jest wartość. |
|**tworzenieLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Opcjonalnie) Po ustawieniu `true`kolekcji dzierżaw jest automatycznie tworzony, gdy jeszcze nie istnieje. Wartością domyślną jest `false`. |
|**leasesCollectionThroughput**| **DzierżawaCollectionThroughput**| (Opcjonalnie) Określa liczbę jednostek żądań do przypisania podczas tworzenia kolekcji dzierżaw. To ustawienie jest `createLeaseCollectionIfNotExists` używane tylko `true`wtedy, gdy jest ustawione na . Ten parametr jest ustawiany automatycznie podczas tworzenia powiązania za pomocą portalu.
|**leaseCollectionPrefix**| **Umowa najmuPrefix**| (Opcjonalnie) Po ustawieniu wartość jest dodawana jako prefiks do dzierżawy utworzone w kolekcji dzierżawy dla tej funkcji. Za pomocą prefiksu umożliwia dwa oddzielne usługi Azure usługi do udziału w tej samej kolekcji dzierżawy przy użyciu różnych prefiksów.
|**feedPollDelay**| **FeedPollDelay (Polski)**| (Opcjonalnie) Czas (w milisekundach) opóźnienia między sondowaniem partycji dla nowych zmian w kanale danych, po wyczerpaniu wszystkich bieżących zmian. Wartość domyślna to 5000 milisekund lub 5 sekund.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Opcjonalnie) Po ustawieniu definiuje w milisekundach interwał do rozpoczęcia zadania do obliczenia, jeśli partycje są rozłożone równomiernie między znane wystąpienia hosta. Wartość domyślna to 13000 (13 sekund).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Opcjonalnie) Po ustawieniu definiuje w milisekundach interwał, dla którego dzierżawa jest pobierana na dzierżawę reprezentującą partycję. Jeśli dzierżawa nie zostanie odnowiona w tym przedziale czasu, spowoduje to jej wygaśnięcie, a własność partycji zostanie przesunięta do innego wystąpienia. Wartość domyślna to 60000 (60 sekund).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Opcjonalnie) Po ustawieniu definiuje w milisekundach interwał odnawiania dla wszystkich dzierżaw dla partycji aktualnie posiadanych przez wystąpienie. Wartość domyślna to 17000 (17 sekund).
|**częstotliwość punktów kontrolnych**| **Częstotliwość punktów kontrolnych**| (Opcjonalnie) Po ustawieniu definiuje w milisekundach interwał między punktami kontrolnymi dzierżawy. Wartość domyślna jest zawsze po każdym wywołaniu funkcji.
|**maxItemsPerInvocation**| **Maksymalnezawodowanie Maksymalnewywodowanie**| (Opcjonalnie) Po ustawieniu ta właściwość ustawia maksymalną liczbę elementów odebranych na wywołanie funkcji. Jeśli operacje w monitorowałej kolekcji są wykonywane za pośrednictwem procedur przechowywanych, [zakres transakcji](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) jest zachowywany podczas odczytywania elementów z kanału informacyjnego zmiany. W rezultacie liczba odebranych towarów może być wyższa niż określona wartość, tak aby towary zmienione przez tę samą transakcję były zwracane jako część jednej partii atomowej.
|**startFromBeginning**| **StartFromBeginning**| (Opcjonalnie) Ta opcja informuje Wyzwalacz, aby odczytać zmiany od początku historii zmian kolekcji zamiast zaczynać się od bieżącej godziny. Odczyt od początku działa tylko przy pierwszym uruchomieniu wyzwalacza, ponieważ w kolejnych uruchomieniach punkty kontrolne są już przechowywane. Ustawienie tej `true` opcji na czas, gdy istnieją już utworzone dzierżawy, nie ma wpływu. |
|**preferowaneLokacje**| **PreferowaneLocations**| (Opcjonalnie) Definiuje preferowane lokalizacje (regiony) dla kont bazy danych replikowanych geograficznie w usłudze Usługi Azure Cosmos DB. Wartości powinny być oddzielone przecinkami. Na przykład "Wschodnie stany USA, południowo-środkowe stany USA,Europa Północna". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Sposób użycia

Wyzwalacz wymaga drugiej kolekcji, która używa do przechowywania _dzierżaw_ za pomocą partycji. Zarówno kolekcji monitorowane i kolekcji, która zawiera dzierżawy muszą być dostępne dla wyzwalacza do pracy.

>[!IMPORTANT]
> Jeśli wiele funkcji są skonfigurowane do używania wyzwalacza usługi Cosmos DB dla tej samej `LeaseCollectionPrefix` kolekcji, każda z funkcji należy użyć dedykowanej kolekcji dzierżawy lub określić inny dla każdej funkcji. W przeciwnym razie zostanie wyzwolona tylko jedna z funkcji. Aby uzyskać informacje o prefiksie, zobacz [sekcję Konfiguracja](#configuration).

Wyzwalacz nie wskazuje, czy dokument został zaktualizowany lub wstawiony, po prostu udostępnia sam dokument. Jeśli chcesz obsługiwać aktualizacje i wstawia inaczej, można to zrobić, implementując pola sygnatury czasowe do wstawiania lub aktualizacji.

## <a name="next-steps"></a>Następne kroki

- [Odczyt dokumentu usługi Azure Cosmos DB (powiązanie wejściowe)](./functions-bindings-cosmosdb-v2-input.md)
- [Zapisywanie zmian w dokumencie usługi Azure Cosmos DB (powiązanie danych wyjściowych)](./functions-bindings-cosmosdb-v2-output.md)