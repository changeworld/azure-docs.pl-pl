---
title: Zarządzanie wystąpieniami w funkcje trwałe - Azure
description: Informacje o sposobie zarządzania jego wystąpieniami w rozszerzenia funkcji trwałych dla usługi Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 1ab2e35c916c6bd6f2d73a328f71710378fac890
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53343942"
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Zarządzanie wystąpieniami w funkcje trwałe (usługi Azure Functions)

[Trwałe funkcje](durable-functions-overview.md) aranżacji wystąpień może być pracę, zakończone, zapytań i wysyłane zdarzenia powiadomień. Wszystkie wystąpienia Zarządzanie odbywa się przy użyciu [klient orkiestracji powiązanie](durable-functions-bindings.md). W tym artykule przechodzi do szczegółów każdej operacji zarządzania wystąpienia.

## <a name="starting-instances"></a>Uruchamianie wystąpienia

[StartNewAsync] (https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) metody [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) lub `startNew` na `DurableOrchestrationClient` (JavaScript) uruchamia nowe wystąpienie funkcji programu orchestrator. Wystąpienia tej klasy można kupić przy użyciu `orchestrationClient` powiązania. Wewnętrznie ta metoda umieszczeniu komunikat do kolejki kontroli, a następnie uruchamiają początku funkcji o podanej nazwie, która używa `orchestrationTrigger` wyzwolić powiązania.

Ta operacja async uzupełnia, gdy pomyślnie zaplanowano procesu aranżacji. Na początek procesu aranżacji w ciągu 30 sekund. Jeśli zajmuje więcej czasu, `TimeoutException` zgłaszany.

> [!WARNING]
> Wdrażając aplikacje lokalnie w języku JavaScript, musisz ustawić zmienną środowiskową `WEBSITE_HOSTNAME` do `localhost:<port>`, np. `localhost:7071` na korzystanie z metod `DurableOrchestrationClient`. Aby uzyskać więcej informacji na temat tego wymagania, zobacz [problem w usłudze GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="net"></a>.NET

Parametry [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) są następujące:

* **Nazwa**: Nazwa funkcji programu orchestrator do zaplanowania.
* **Dane wejściowe**: Dowolne dane serializacji JSON, który powinien zostać przekazany jako dane wejściowe do funkcji programu orchestrator.
* **Identyfikator instanceId**: (Opcjonalnie) Unikatowy identyfikator wystąpienia. Jeśli nie zostanie określony, zostanie wygenerowany identyfikator wystąpienia losowej.

Poniżej przedstawiono prosty przykład C#:

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

Parametry `startNew` są następujące:

* **Nazwa**: Nazwa funkcji programu orchestrator do zaplanowania.
* **Identyfikator instanceId**: (Opcjonalnie) Unikatowy identyfikator wystąpienia. Jeśli nie zostanie określony, zostanie wygenerowany identyfikator wystąpienia losowej.
* **Dane wejściowe**: (Opcjonalnie) Dowolne dane serializacji JSON, który powinien zostać przekazany jako dane wejściowe do funkcji programu orchestrator.

Poniżej przedstawiono prosty przykład JavaScript:

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

> [!NOTE]
> Losowy identyfikator na użytek identyfikatora wystąpienia. Ułatwi to zapewnienie dystrybucji obciążenia z działaniem równe, podczas skalowania funkcje programu orchestrator na wielu maszynach wirtualnych. Prawidłowego czasu, aby korzystać identyfikatorów wystąpień nielosową jest identyfikator musi pochodzić ze źródła zewnętrznego lub podczas implementowania [programu orchestrator z pojedynczego wystąpienia](durable-functions-singletons.md) wzorca.

### <a name="using-core-tools"></a>Przy użyciu podstawowych narzędzi

Istnieje również możliwość uruchomić wystąpienie bezpośrednio za pośrednictwem [podstawowych narzędzi usługi Azure Functions](../functions-run-local.md) `durable start-new` polecenia. Jego przyjmuje następujące parametry:

* **`function-name` (wymagane)** : Nazwa funkcji, które można uruchomić
* **`input` (opcjonalnie)** : Dane wejściowe do funkcji, albo w tekście lub za pomocą pliku JSON. W przypadku plików prefiksu ścieżki do pliku za pomocą `@`, takich jak `@path/to/file.json`.
* **`id` (opcjonalnie)** : Identyfikator wystąpienia aranżacji. Jeśli nie zostanie podana, zostanie wygenerowany losowy identyfikator GUID.
* **`connection-string-setting` (opcjonalnie)** : Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to AzureWebJobsStorage.
* **`task-hub-name` (opcjonalnie)** : Nazwa koncentratora trwałe zadanie do użycia. Wartość domyślna to DurableFunctionsHub. Ponadto można ustawić w [host.json](durable-functions-bindings.md#host-json) za pośrednictwem durableTask:HubName.

> [!NOTE]
> Podstawowe narzędzia polecenia przyjęto założenie, że są one wykonywane z katalogu głównego aplikacji funkcji. Jeśli `connection-string-setting` i `task-hub-name` są jawnie podana polecenia można uruchomić z dowolnego katalogu. Natomiast poleceń mogą być wykonywane bez uruchamiania hosta aplikacji funkcji, niektóre efekty mogą nie być obserwowane, chyba że na hoście jest uruchomiony. Na przykład `start-new` polecenia będzie umieścić komunikat uruchomienia do koncentratora docelowy zadania, ale orchestration nie faktycznie uruchomi chyba, że funkcja aplikacji hosta proces uruchomiony, może przetworzyć komunikatu.

Następujące polecenie spowoduje Uruchom funkcję o nazwie HelloWorld i przekaż zawartość pliku "licznik-data.json" do niego:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="querying-instances"></a>Tworzenie zapytań wystąpień

[GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) metody [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klas (.NET) lub `getStatus` metody `DurableOrchestrationClient` klasy (JavaScript) zapytanie o stan aranżacji wystąpienie.

Trwa `instanceId` (wymagane), `showHistory` (opcjonalnie) `showHistoryOutput` (opcjonalnie) i `showInput` (opcjonalnie, tylko .NET) jako parametry.

* **`showHistory`**: Jeśli ustawiono `true`, odpowiedź będzie zawierać historii wykonywania.
* **`showHistoryOutput`**: Jeśli ustawiono `true`, historię wykonywania będzie zawierać danych wyjściowych działania.
* **`showInput`**: Jeśli ustawiono `false`, odpowiedź nie zawiera dane wejściowe funkcji. Wartość domyślna to `true`. (Tylko platforma .NET)

Metoda ta zwraca obiekt JSON z następującymi właściwościami:

* **Nazwa**: Nazwa funkcji programu orchestrator.
* **Identyfikator instanceId**: Identyfikator wystąpienia aranżacji (powinna być taka sama jak `instanceId` wejściowego).
* **Wartością CreatedTime**: Czas, w którym funkcja orkiestratora został uruchomiony.
* **LastUpdatedTime**: Czas, w którym aranżacji ostatni utworzono punkt kontrolny.
* **Dane wejściowe**: Dane wejściowe funkcji jako wartości JSON. To pole nie zostanie wypełnione, jeśli `showInput` ma wartość false.
* **CustomStatus**: Stan niestandardowej aranżacji w formacie JSON.
* **Dane wyjściowe**: Dane wyjściowe funkcji jako wartości JSON (Jeśli funkcja została ukończona). Funkcja programu orchestrator nie powiodło się, ta właściwość będzie zawierać szczegóły błędu. Jeśli funkcja orkiestratora został zakończony, ta właściwość będzie zawierać podana Przyczyna zakończenia (jeśli istnieje).
* **RuntimeStatus**: Jeden z następujących wartości:
  * **Oczekujące**: Wystąpienie zostało zaplanowane, ale nie zostało jeszcze uruchomione uruchomiona.
  * **Uruchamianie**: Wystąpienie rozpoczęło działanie.
  * **Ukończono**: Wystąpienie zostało zakończone normalnie.
  * **ContinuedAsNew**: Wystąpienie uruchomieniu się o nowych historii. Jest to stan przejściowy.
  * **Nie powiodło się**: Wystąpienie nie powiodło się z powodu błędu.
  * **Zakończony**: Wystąpienie zostało nagle zatrzymane.
* **Historia**: Historia wykonywania aranżacji. To pole jest wypełniane tylko, jeśli `showHistory` ustawiono `true`.

Ta metoda zwraca `null` Jeśli wystąpienie nie istnieje lub nie zostało jeszcze uruchomione uruchomiona.

### <a name="c"></a>C#

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="using-core-tools"></a>Przy użyciu podstawowych narzędzi

Istnieje również możliwość wyświetlić stan wystąpienia aranżacji bezpośrednio za pośrednictwem [podstawowych narzędzi usługi Azure Functions](../functions-run-local.md) `durable get-runtime-status` polecenia. Jego przyjmuje następujące parametry:

* **`id` (wymagane)** : Identyfikator wystąpienia aranżacji
* **`show-input` (opcjonalnie)** : Jeśli ustawiono `true`, odpowiedź będzie zawierać dane wejściowe funkcji. Wartość domyślna to `false`.
* **`show-output` (opcjonalnie)** : Jeśli ustawiono `true`, odpowiedź będzie zawierać dane wyjściowe funkcji. Wartość domyślna to `false`.
* **`connection-string-setting` (opcjonalnie)** : Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to AzureWebJobsStorage.
* **`task-hub-name` (opcjonalnie)** : Nazwa koncentratora trwałe zadanie do użycia. Wartość domyślna to DurableFunctionsHub. Ponadto można ustawić w [host.json](durable-functions-bindings.md#host-json) za pośrednictwem durableTask:HubName.

Następujące polecenie pobiera stan wystąpienia przy użyciu Identyfikatora wystąpienia aranżacji 0ab8c55a66644d68a3a8b220b12d209c (w tym dane wejściowe i wyjściowe). Zakłada `func` polecenia jest uruchamiany z katalogu głównego aplikacji funkcji:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

`durable get-history` Polecenia można pobrać historii wystąpienie aranżacji. Jego przyjmuje następujące parametry:

* **`id` (wymagane)** : Identyfikator wystąpienia aranżacji
* **`connection-string-setting` (opcjonalnie)** : Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to AzureWebJobsStorage.
* **`task-hub-name` (opcjonalnie)** : Nazwa koncentratora trwałe zadanie do użycia. Wartość domyślna to DurableFunctionsHub. Można ją również ustawić w host.json za pośrednictwem durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="querying-all-instances"></a>Wykonywanie zapytań, wszystkie wystąpienia

Możesz użyć `GetStatusAsync` (.NET) lub `getStatusAll` metoda (JavaScript) do wykonywania zapytań stanami wszystkich wystąpień aranżacji. Na platformie .NET można przekazać `CancellationToken` obiektu w przypadku, gdy chcesz anulować. Metoda ta zwraca obiekty z tymi samymi właściwościami co `GetStatusAsync` metody z parametrami.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="using-core-tools"></a>Przy użyciu podstawowych narzędzi

Istnieje również możliwość wystąpienia zapytania bezpośrednio za pośrednictwem [podstawowych narzędzi usługi Azure Functions](../functions-run-local.md) `durable get-instances` polecenia. Jego przyjmuje następujące parametry:

* **`top` (opcjonalnie)** : To polecenie obsługuje stronicowanie. Ten parametr odnosi się do liczby wystąpień pobrane na żądanie. Wartość domyślna wynosi 10.
* **`continuation-token` (opcjonalnie)** : Token, aby wskazać, które strony/section wystąpień do pobrania. Każdy `get-instances` wykonanie zwraca token do następnego zestawu wystąpień.
* **`connection-string-setting` (opcjonalnie)** : Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to AzureWebJobsStorage.
* **`task-hub-name` (opcjonalnie)** : Nazwa koncentratora trwałe zadanie do użycia. Wartość domyślna to DurableFunctionsHub. Ponadto można ustawić w [host.json](durable-functions-bindings.md#host-json) za pośrednictwem durableTask:HubName.

```bash
func durable get-instances
```

## <a name="querying-instances-with-filters"></a>Wystąpienia zapytań z filtrami

Można również użyć `GetStatusAsync` (.NET) lub `getStatusBy` metoda (JavaScript) w celu uzyskania listy wystąpień aranżacji, które pasują zestaw wstępnie zdefiniowanych filtrów. Opcje filtru można zawierają czas tworzenia aranżacji i stan czasu wykonywania aranżacji.

### <a name="c"></a>C#

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IEnumerable<OrchestrationRuntimeStatus> runtimeStatus = new List<OrchestrationRuntimeStatus> {
        OrchestrationRuntimeStatus.Completed,
        OrchestrationRuntimeStatus.Running
    };
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(
        new DateTime(2018, 3, 10, 10, 1, 0),
        new DateTime(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    ); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="using-the-functions-core-tools"></a>Przy użyciu funkcji podstawowych narzędzi

`durable get-instances` Polecenia można również za pomocą filtrów. Oprócz wyżej wymienionych `top`, `continuation-token`, `connection-string-setting`, i `task-hub-name` parametrów, trzech parametrów filtru (`created-after`, `created-before`, i `runtime-status`), mogą być używane.

* **`created-after` (opcjonalnie)** : Pobieranie wystąpień utworzonych po tej daty/godziny (UTC). ISO 8601 formacie Data/Godzina zaakceptowane.
* **`created-before` (opcjonalnie)** : Pobieranie wystąpienia utworzone przed tym data/godzina (UTC). ISO 8601 formacie Data/Godzina zaakceptowane.
* **`runtime-status` (opcjonalnie)** : Pobieranie wystąpienia, którego stan dopasowania tych ("uruchomiona", "ukończone" itp.). Można podać wiele stanów (oddzielone spacjami).
* **`top` (opcjonalnie)** : Pobrać liczby wystąpień na żądanie. Wartość domyślna wynosi 10.
* **`continuation-token` (opcjonalnie)** : Token, aby wskazać, które strony/section wystąpień do pobrania. Każdy `get-instances` wykonanie zwraca token do następnego zestawu wystąpień.
* **`connection-string-setting` (opcjonalnie)** : Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to AzureWebJobsStorage.
* **`task-hub-name` (opcjonalnie)** : Nazwa koncentratora trwałe zadanie do użycia. Wartość domyślna to DurableFunctionsHub. Ponadto można ustawić w [host.json](durable-functions-bindings.md#host-json) za pośrednictwem durableTask:HubName.

Jeśli nie filtrów (`created-after`, `created-before`, lub `runtime-status`) są udostępniane, następnie `top` wystąpienia zostaną pobrane nie w odniesieniu do czasu utworzenia lub stan środowiska uruchomieniowego.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminating-instances"></a>Trwa przerywanie działania wystąpień

Uruchomionego wystąpienia orchestration można przerywać działanie przy użyciu [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) metody [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klas (.NET) lub `terminate` metody `DurableOrchestrationClient` klasy () JavaScript). Są dwa parametry `instanceId` i `reason` parametry, które będą zapisywane w dziennikach i stan wystąpienia. Wystąpienie zakończone zatrzyma się zaraz po osiągnięciu następnego `await` (.NET) lub `yield` punkt (JavaScript) lub jest on utracą ważność natychmiast, jeśli jest już włączone `await` (.NET) lub `yield` (JavaScript).

### <a name="c"></a>C#

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> Zakończenie wystąpienia nie obecnie propaguje. Działanie funkcji i mechanizmów podrzędne będą uruchamiane do zakończenia, niezależnie od tego, czy wystąpienie aranżacji, które je wywołuje zostało zakończone.

### <a name="using-core-tools"></a>Przy użyciu podstawowych narzędzi

Użytkownik może również zakończyć wystąpienia aranżacji bezpośrednio za pośrednictwem [podstawowe narzędzia](../functions-run-local.md) `durable terminate` polecenia. Jego przyjmuje następujące parametry:

* **`id` (wymagane)** : Identyfikator wystąpienia aranżacji, aby zakończyć
* **`reason` (opcjonalnie)** : Przyczyna zakończenia
* **`connection-string-setting` (opcjonalnie)** : Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to AzureWebJobsStorage.
* **`task-hub-name` (opcjonalnie)** : Nazwa koncentratora trwałe zadanie do użycia. Wartość domyślna to DurableFunctionsHub. Ponadto można ustawić w [host.json](durable-functions-bindings.md#host-json) za pośrednictwem durableTask:HubName.

Następujące polecenie zakończy się aranżacji wystąpienia o identyfikatorze 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="sending-events-to-instances"></a>Wysyłanie zdarzeń do wystąpień

Powiadomienia dotyczące zdarzenia mogą być wysyłane do uruchamiania wystąpień przy użyciu [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) metody [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klas (.NET) lub `raiseEvent` metody `DurableOrchestrationClient` klasy () JavaScript). Wystąpienia, obsługujące te zdarzenia są te, które oczekują na wywołanie [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) lub `waitForExternalEvent` (JavaScript).

Parametry [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) i `raiseEvent` (JavaScript) są następujące:

* **Identyfikator instanceId**: Unikatowy identyfikator wystąpienia.
* **EventName**: Nazwa zdarzenia do wysłania.
* **EventData**: Ładunek do serializacji JSON do wysłania do wystąpienia.

### <a name="c"></a>C#

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!WARNING]
> Jeśli żadne wystąpienie aranżacji z określonym *identyfikator wystąpienia* lub jeśli wystąpienie nie oczekuje na określonym *Nazwa zdarzenia*, komunikat o zdarzeniu zostanie odrzucony. Aby uzyskać więcej informacji na temat tego zachowania, zobacz [problem w usłudze GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="using-core-tools"></a>Przy użyciu podstawowych narzędzi

Istnieje również możliwość wywołać zdarzenie do wystąpienia aranżacji bezpośrednio za pośrednictwem [podstawowe narzędzia](../functions-run-local.md) `durable raise-event` polecenia. Jego przyjmuje następujące parametry:

* **`id` (wymagane)** : Identyfikator wystąpienia aranżacji
* **`event-name` (opcjonalnie)** : Nazwa zdarzenia, aby zgłosić. Wartość domyślna to `$"Event_{RandomGUID}"`
* **`event-data` (opcjonalnie)** : Dane do wysłania do wystąpienia aranżacji. Może to być ścieżka do pliku JSON lub danych można podać bezpośrednio w wierszu polecenia
* **`connection-string-setting` (opcjonalnie)** : Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to AzureWebJobsStorage.
* **`task-hub-name` (opcjonalnie)** : Nazwa koncentratora trwałe zadanie do użycia. Wartość domyślna to DurableFunctionsHub. Ponadto można ustawić w [host.json](durable-functions-bindings.md#host-json) za pośrednictwem durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Czekaj na ukończenie aranżacji

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klasy ujawnia [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) interfejsu API na platformie .NET, który może służyć do uzyskiwanie synchronicznie rzeczywiste wyniki aranżacji wystąpienie. W języku JavaScript `DurableOrchestrationClient` klasy ujawnia `waitForCompletionOrCreateCheckStatusResponse` interfejsu API, w tym samym celu. Metody, użyj wartości domyślnej 10 sekund na `timeout` i 1 sekundę `retryInterval` gdy nie są ustawione.  

Oto przykład funkcji wyzwalacza HTTP, który pokazuje, jak używać tego interfejsu API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Funkcja może zostać wywołana w następującym wierszem przy użyciu 2 sekund limitu czasu i interwału ponawiania 0,5 sekundy:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

W zależności od czas potrzebny na odpowiedź z wystąpienia aranżacji istnieją dwa przypadki:

* Wystąpienia aranżacji zakończyć w ciągu zdefiniowanego limitu czasu (w tym przypadku 2 sekundy), odpowiedź aranżacji rzeczywistego wystąpienia przekazywane są dostarczane synchronicznie:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* Wystąpienia orchestration nie może zakończyć w ciągu zdefiniowanego limitu czasu (w tym przypadku 2 sekund), odpowiedź jest domyślna jednego opisanego w **Odnajdywanie adresu URL interfejsu API HTTP**:

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}",
            "rewindPostUri": "https://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/rewind?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Format adresu URL elementu webhook mogą się różnić w zależności od posiadanej wersji programu host usługi Azure Functions. Powyższy przykład dotyczy hosta 2.x usługi Azure Functions.

## <a name="retrieving-http-management-webhook-urls"></a>Trwa pobieranie adresów URL elementu Webhook zarządzania HTTP

Systemy zewnętrzne mogą się komunikować trwałe funkcje za pomocą adresów URL elementu webhook, będące częścią odpowiedzi domyślnej opisanego w [Odnajdywanie adresu URL interfejsu API HTTP](durable-functions-http-api.md). Jednak adresy URL elementu webhook również można uzyskać programistycznie w klient orkiestracji lub w funkcji działania za pośrednictwem [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) metody [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)klas (.NET) lub `createHttpManagementPayload` metody `DurableOrchestrationClient` klasy (JavaScript).

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) i `createHttpManagementPayload` mieć jeden parametr:

* **Identyfikator instanceId**: Unikatowy identyfikator wystąpienia.

Metody zwracają wystąpienie [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) lub obiekt (JavaScript) z następującymi właściwościami ciąg:

* **Identyfikator**: Identyfikator wystąpienia aranżacji (powinna być taka sama jak `InstanceId` wejściowego).
* **StatusQueryGetUri**: Adres URL stanu wystąpienia aranżacji.
* **SendEventPostUri**: "Zgłoś zdarzenie" adres URL wystąpienia aranżacji.
* **TerminatePostUri**: "Terminate" adres URL wystąpienia aranżacji.
* **RewindPostUri**: "Przewijanie" adres URL wystąpienia aranżacji.

Działanie funkcji można wysłać wystąpienia tych obiektów z systemami zewnętrznymi, do monitorowania lub wywoływać zdarzenia do organizowania:

### <a name="c"></a>C#

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] DurableActivityContext ctx,
    [OrchestrationClient] DurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

## <a name="rewinding-instances-preview"></a>Przewijanie instances (wersja zapoznawcza)

Wystąpienie aranżacji zakończonych niepowodzeniem może być *przewinięta* w uprzednio dobrej kondycji, używając [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) lub `rewindAsync` (JavaScript) interfejsu API. To działa, umieszczając aranżacji do *systemem* stanu i ponowne uruchomienie działania i/lub suborchestration błędy wykonania, które spowodowały błąd aranżacji.

> [!NOTE]
> Ten interfejs API nie jest przeznaczony do zastąpienia obsługi błędów właściwe i zasad ponawiania. Przeciwnie jest przeznaczona do użycia tylko w przypadkach, gdzie wystąpień aranżacji się nie powieść nieoczekiwany powodów. Aby uzyskać szczegółowe informacje na temat zasad ponawiania prób i obsługi błędów, zobacz [obsługi błędów](durable-functions-error-handling.md) tematu.

Przykładem użycia w przypadku *rewind* jest przepływ pracy obejmujące serię [ludzi zatwierdzenia](durable-functions-overview.md#pattern-5-human-interaction). Załóżmy, że istnieje szereg funkcji działań, które powiadamiają o kogoś, że zatwierdzenie jest wymagane i zaczekaj na poziomie odpowiedzi w czasie rzeczywistym. Gdy wszystkie zatwierdzenia działań otrzymane odpowiedzi lub przekroczyło limit czasu, innego działania zakończy się niepowodzeniem z powodu błędnej konfiguracji aplikacji, takich jak nieprawidłowe parametry połączenia. Wynikiem jest błąd aranżacji szczegółowo przepływ pracy. Za pomocą `RewindAsync` (.NET) lub `rewindAsync` (JavaScript) interfejsu API, administrator aplikacji może naprawić ten błąd konfiguracji i *rewind* orchestration nie powiodło się z powrotem do stanu bezpośrednio przed awarią. Żaden z kroków interakcji człowieka muszą zostać ponownie uznany i organizowania może teraz zostać pomyślnie ukończony.

> [!NOTE]
> *Rewind* funkcja nie obsługuje przetwarzania aranżacji wystąpieniom używaj czasomierzy trwałe.

### <a name="c"></a>C#

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="using-core-tools"></a>Przy użyciu podstawowych narzędzi

Istnieje również możliwość do tyłu wystąpienia aranżacji bezpośrednio za pośrednictwem [podstawowe narzędzia](../functions-run-local.md) `durable rewind` polecenia. Jego przyjmuje następujące parametry:

* **`id` (wymagane)** : Identyfikator wystąpienia aranżacji
* **`reason` (opcjonalnie)** : Przyczyna przewijanie wystąpienia aranżacji
* **`connection-string-setting` (opcjonalnie)** : Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to AzureWebJobsStorage.
* **`task-hub-name` (opcjonalnie)** : Nazwa koncentratora trwałe zadanie do użycia. Wartość domyślna to DurableFunctionsHub. Ponadto można ustawić w [host.json](durable-functions-bindings.md#host-json) za pośrednictwem durableTask:HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Wyczyść historię wystąpienia

> [!NOTE]
> `PurgeInstanceHistoryAsync` Interfejs API jest obecnie dostępna tylko w przypadku C#. Będzie można dodać do języka JavaScript w kolejnej wersji.

Historia Orchestration można czyścić przy użyciu [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_). Funkcje spowoduje usunięcie wszystkich danych skojarzonych z aranżacji — wiersze tabeli platformy Azure i obiektów blob dużych bloków komunikatów, jeśli takie istnieją. Metoda ma dwa przeciążenia. Pierwsza z nich powoduje wyczyszczenie historii za pomocą Identyfikatora wystąpienia orchestration:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

Drugi przykład przedstawia funkcji wyzwalanej przez czasomierz, który powoduje wyczyszczenie historii dla wszystkich wystąpień aranżacji zakończone po określonym interwale. W tym przypadku usunie dane dla wszystkich wystąpień ukończone co najmniej 30 dni temu. Zostaje zaplanowane do uruchomienia raz dziennie o 12: 00:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
                    DateTime.MinValue,
                    DateTime.UtcNow.AddDays(-30),  
                    new List<OrchestrationStatus>
                    {
                        OrchestrationStatus.Completed
                    });
}
```

> [!NOTE]
> *PurgeInstanceHistory* przeciążenia akceptować przedziale czasu, jako parametr będzie przetwarzać tylko aranżacji wystąpienia w jeden stan środowiska uruchomieniowego — zakończone, zwolniony lub nie powiodło się.

### <a name="using-core-tools"></a>Przy użyciu podstawowych narzędzi

Istnieje możliwość przeczyścić historię wystąpienie aranżacji przy użyciu [podstawowe narzędzia](../functions-run-local.md) `durable purge-history` polecenia. Podobnie jak drugi C# powyższego przykładu Przeczyszcza historii dla wszystkich wystąpień aranżacji utworzone w określonym interwale. Wystąpienia przeczyścić może być dalej filtrowana według stanu środowiska uruchomieniowego. Polecenie ma kilka parametrów:

* **`created-after` (opcjonalnie)** : Wyczyść historię wystąpień utworzonych po tej daty/godziny (UTC). ISO 8601 formacie Data/Godzina zaakceptowane.
* **`created-before` (opcjonalnie)** : Wyczyść historię wystąpienia utworzone przed tym data/godzina (UTC). ISO 8601 formacie Data/Godzina zaakceptowane.
* **`runtime-status` (opcjonalnie)** : Wyczyść historię wystąpień, których stan dopasowania tych ("uruchomiona", "ukończone" itp.). Można podać wiele stanów (oddzielone spacjami).
* **`connection-string-setting` (opcjonalnie)** : Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to AzureWebJobsStorage.
* **`task-hub-name` (opcjonalnie)** : Nazwa koncentratora trwałe zadanie do użycia. Wartość domyślna to DurableFunctionsHub. Ponadto można ustawić w [host.json](durable-functions-bindings.md#host-json) za pośrednictwem durableTask:HubName.

Następujące polecenie spowoduje Usuń historię wszystkich wystąpień zakończonych niepowodzeniem przed 14 listopada 2018 r. utworzono 7:35 PM (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="deleting-a-task-hub"></a>Usuwanie Centrum zadań

Za pomocą [podstawowe narzędzia](../functions-run-local.md) `durable delete-task-hub` polecenia, istnieje możliwość usunięcia wszystkich artefaktów magazynu skojarzonego z rozwiązaniem hub danego zadania. W tym usługi Azure storage tabel, kolejek i obiektów blob. Polecenie ma dwa parametry:

* **`connection-string-setting` (opcjonalnie)** : Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to AzureWebJobsStorage.
* **`task-hub-name` (opcjonalnie)** : Nazwa koncentratora trwałe zadanie do użycia. Wartość domyślna to DurableFunctionsHub. Ponadto można ustawić w [host.json](durable-functions-bindings.md#host-json) za pośrednictwem durableTask:HubName.

Następujące polecenie spowoduje usunięcie wszystkich danych usługi Azure storage, które skojarzone z Centrum zadanie "UserTest".

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak używać interfejsów API protokołu HTTP dla wystąpienia zarządzania](durable-functions-http-api.md)
