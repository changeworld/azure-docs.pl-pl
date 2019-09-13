---
title: Zarządzanie wystąpieniami w Durable Functions — Azure
description: Dowiedz się, jak zarządzać wystąpieniami w rozszerzeniu Durable Functions Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 0df6f5f9728a8e48a3257e56ddf8ad23906dc92c
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933317"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Zarządzanie wystąpieniami w Durable Functions na platformie Azure

Jeśli używasz rozszerzenia [Durable Functions](durable-functions-overview.md) Azure Functions lub chcesz rozpocząć wykonywanie tego działania, upewnij się, że najlepiej jest z niego korzystać. Aby zoptymalizować Durable Functions wystąpienia aranżacji, możesz dowiedzieć się więcej o sposobach zarządzania nimi. Ten artykuł zawiera szczegółowe informacje o każdej operacji zarządzania wystąpieniem.

Można na przykład uruchamiać i kończyć wystąpienia oraz wykonywać zapytania o wystąpienia, w tym możliwość wykonywania zapytań o wszystkie wystąpienia i wystąpienia zapytań za pomocą filtrów. Ponadto możesz wysyłać zdarzenia do wystąpień, poczekać na zakończenie aranżacji i pobrać adresy URL elementu webhook zarządzania HTTP. W tym artykule opisano również inne operacje zarządzania, w tym wystąpienia odwracania, przeczyszczania historii wystąpień i usuwania centrum zadań.

W Durable Functions są dostępne opcje implementacji każdej z tych operacji zarządzania. W tym artykule przedstawiono przykłady, które używają [Azure Functions Core Tools](../functions-run-local.md) zarówno dla programuC#.NET (), jak i języka JavaScript.

## <a name="start-instances"></a>Wystąpienia uruchamiania

Ważne jest, aby uruchomić wystąpienie aranżacji. Jest to często wykonywane, gdy używasz powiązania Durable Functions w wyzwalaczu innej funkcji.

Metoda [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) w [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) `startNew` `DurableOrchestrationClient` lub na (JavaScript) uruchamia nowe wystąpienie. Wystąpienia tej klasy uzyskuje się `orchestrationClient` za pomocą powiązania. Wewnętrznie metoda ta enqueues komunikat do kolejki sterującej, która następnie wyzwala początek funkcji o określonej nazwie, która używa `orchestrationTrigger` powiązania wyzwalacza.

Ta operacja asynchroniczna kończy się po pomyślnym zaplanowaniu procesu aranżacji. Proces aranżacji powinien rozpoczynać się w ciągu 30 sekund. Jeśli trwa dłużej, `TimeoutException`zobaczysz.

### <a name="net"></a>.NET

Parametry do [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) są następujące:

* **Nazwa**: Nazwa funkcji programu Orchestrator do zaplanowania.
* **Dane wejściowe**: Wszystkie dane, które można serializować w formacie JSON, które powinny być przesyłane jako dane wejściowe do funkcji programu Orchestrator.
* **Identyfikator wystąpienia**: Obowiązkowe Unikatowy identyfikator wystąpienia. Jeśli ten parametr nie zostanie określony, metoda używa identyfikatora losowego.

Oto kilka przykładów:

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

Parametry `startNew` są następujące:

* **Nazwa**: Nazwa funkcji programu Orchestrator do zaplanowania.
* **Identyfikator wystąpienia**: Obowiązkowe Unikatowy identyfikator wystąpienia. Jeśli ten parametr nie zostanie określony, metoda używa identyfikatora losowego.
* **Dane wejściowe**: Obowiązkowe Wszystkie dane, które można serializować w formacie JSON, które powinny być przesyłane jako dane wejściowe do funkcji programu Orchestrator.

Oto prosty przykład JavaScript:

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

> [!TIP]
> Użyj losowego identyfikatora wystąpienia. Zapewnia to równoważną dystrybucję obciążenia podczas skalowania funkcji programu Orchestrator na wielu maszynach wirtualnych. Prawidłowy czas użycia nielosowych identyfikatorów wystąpień to, gdy identyfikator musi pochodzić ze źródła zewnętrznego lub gdy wdrażany jest wzorzec [pojedynczego programu Orchestrator](durable-functions-singletons.md) .

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Wystąpienie można również uruchomić bezpośrednio przy użyciu polecenia [Azure Functions Core Tools](../functions-run-local.md) `durable start-new` . Przyjmuje następujące parametry:

* (wymagane): **`function-name`** Nazwa funkcji do uruchomienia.
* (opcjonalnie): **`input`** Dane wejściowe do funkcji, w postaci wbudowanej lub za pomocą pliku JSON. Dla plików Dodaj prefiks do ścieżki do pliku `@`, `@path/to/file.json`na przykład.
* (opcjonalnie): **`id`** Identyfikator wystąpienia aranżacji. Jeśli ten parametr nie jest określony, polecenie używa losowego identyfikatora GUID.
* (opcjonalnie): **`connection-string-setting`** Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to AzureWebJobsStorage.
* (opcjonalnie): **`task-hub-name`** Nazwa Durable Functionsego centrum zadań do użycia. Wartość domyślna to DurableFunctionsHub. Można to również skonfigurować w pliku [host. JSON](durable-functions-bindings.md#host-json) przy użyciu DurableTask: HubName.

> [!NOTE]
> Polecenia podstawowych narzędzi zakładają, że są one uruchamiane z katalogu głównego aplikacji funkcji. Jeśli jawnie podano `connection-string-setting` parametry i `task-hub-name` , można uruchomić polecenia z dowolnego katalogu. Chociaż można uruchamiać te polecenia bez uruchomienia hosta aplikacji funkcji, może się okazać, że nie można obserwować niektórych efektów, chyba że host jest uruchomiony. Na przykład `start-new` polecenie enqueues komunikat startowy w docelowym centrum zadań, ale aranżacja nie działa, chyba że jest uruchomiony proces hosta aplikacji funkcji, który może przetwarzać komunikat.

Następujące polecenie uruchamia funkcję o nazwie HelloWorld i przekazuje do niej zawartość pliku `counter-data.json` :

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Wystąpienia zapytań

W ramach wysiłków związanych z zarządzaniem aranżacjami najprawdopodobniej trzeba będzie zebrać informacje o stanie wystąpienia aranżacji (na przykład czy zostało zakończone normalnie lub zakończone niepowodzeniem).

Metoda [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) klasy [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET `getStatus` ) lub metoda w `DurableOrchestrationClient` klasie (JavaScript) wysyła zapytanie o stan wystąpienia aranżacji.

Przyjmuje `instanceId` (wymagane), `showHistory` (opcjonalnie `showHistoryOutput` ), (opcjonalnie) i `showInput` (opcjonalnie, tylko platforma .NET) jako parametry.

* **`showHistory`** : Jeśli jest ustawiona `true`na, odpowiedź zawiera historię wykonania.
* **`showHistoryOutput`** : W przypadku wybrania opcji `true`historia wykonywania zawiera dane wyjściowe działania.
* **`showInput`** : Jeśli jest ustawiona `false`na, odpowiedź nie będzie zawierać wejścia funkcji. Wartość domyślna to `true`. (Tylko platforma .NET)

Metoda zwraca obiekt JSON o następujących właściwościach:

* **Nazwa**: Nazwa funkcji programu Orchestrator.
* **Identyfikator wystąpienia**: Identyfikator wystąpienia aranżacji (powinien być taki sam jak `instanceId` dane wejściowe).
* **CreatedTime**: Godzina, o której uruchomiono funkcję programu Orchestrator.
* **LastUpdatedTime**: Godzina ostatniego przetworzenia punktu kontrolnego aranżacji.
* **Dane wejściowe**: Wejście funkcji jako wartość JSON. To pole nie jest wypełniane, jeśli `showInput` ma wartość false.
* **CustomStatus**: Niestandardowy stan aranżacji w formacie JSON.
* **Dane wyjściowe**: Dane wyjściowe funkcji jako wartość JSON (Jeśli funkcja została ukończona). Jeśli działanie programu Orchestrator nie powiodło się, ta właściwość zawiera szczegóły błędu. Jeśli funkcja programu Orchestrator została zakończona, ta właściwość zawiera przyczynę zakończenia (jeśli istnieje).
* **RuntimeStatus**: Jedna z następujących wartości:
  * **Oczekiwanie**: Wystąpienie zostało zaplanowane, ale nie zostało jeszcze uruchomione.
  * **Uruchamianie**: Wystąpienie zostało uruchomione.
  * **Ukończono**: Wystąpienie zostało normalnie ukończone.
  * **ContinuedAsNew**: Wystąpienie zostało uruchomione ponownie z nową historią. Jest to stan przejściowy.
  * **Niepowodzenie**: Wystąpienie nie powiodło się z powodu błędu.
  * **Przerwano**: Wystąpienie zostało zatrzymane w sposób nieoczekiwany.
* **Historia**: Historia wykonywania aranżacji. To pole jest wypełniane tylko `showHistory` wtedy, gdy `true`jest ustawione na.

Ta metoda zwraca `null` czy wystąpienie nie istnieje lub nie zostało jeszcze uruchomione.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Istnieje również możliwość bezpośredniego pobrania stanu wystąpienia aranżacji przy użyciu polecenia [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` . Przyjmuje następujące parametry:

* (wymagane): **`id`** Identyfikator wystąpienia aranżacji.
* (opcjonalnie): **`show-input`** Jeśli jest ustawiona `true`na, odpowiedź zawiera dane wejściowe funkcji. Wartość domyślna to `false`.
* (opcjonalnie): **`show-output`** Jeśli jest ustawiona `true`na, odpowiedź zawiera dane wyjściowe funkcji. Wartość domyślna to `false`.
* (opcjonalnie): **`connection-string-setting`** Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* (opcjonalnie): **`task-hub-name`** Nazwa Durable Functionsego centrum zadań do użycia. Wartość domyślna to `DurableFunctionsHub`. Można ją również ustawić w pliku [host. JSON](durable-functions-bindings.md#host-json)przy użyciu DurableTask: HubName.

Następujące polecenie pobiera stan (łącznie z danymi wejściowymi i wyjściowymi) wystąpienia z IDENTYFIKATORem wystąpienia aranżacji 0ab8c55a66644d68a3a8b220b12d209c. Przyjęto założenie, że uruchamiasz `func` polecenie z katalogu głównego aplikacji funkcji:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Możesz użyć polecenia, `durable get-history` aby pobrać historię wystąpienia aranżacji. Przyjmuje następujące parametry:

* (wymagane): **`id`** Identyfikator wystąpienia aranżacji.
* (opcjonalnie): **`connection-string-setting`** Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* (opcjonalnie): **`task-hub-name`** Nazwa Durable Functionsego centrum zadań do użycia. Wartość domyślna to `DurableFunctionsHub`. Można ją również ustawić w pliku host. JSON przy użyciu durableTask: HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Badaj wszystkie wystąpienia

Zamiast wysyłać zapytania do jednego wystąpienia w ramach aranżacji, może się okazać, że jest bardziej wydajna kwerenda wszystkich jednocześnie.

Można użyć `GetStatusAsync` metody (.NET) lub `getStatusAll` (JavaScript) do wykonywania zapytań o Stanów wszystkich wystąpień aranżacji. W programie .NET można przekazać `CancellationToken` obiekt w przypadku, gdy chcesz go anulować. Metoda zwraca obiekty z tymi samymi właściwościami co `GetStatusAsync` Metoda z parametrami.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

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

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Istnieje również możliwość bezpośredniego zapytania o wystąpienia przy użyciu polecenia [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` . Przyjmuje następujące parametry:

* (opcjonalnie): **`top`** To polecenie obsługuje stronicowanie. Ten parametr odnosi się do liczby wystąpień pobranych dla żądania. Wartość domyślna to 10.
* (opcjonalnie): **`continuation-token`** Token wskazujący stronę lub sekcję wystąpień do pobrania. Każde `get-instances` wykonanie zwraca token do następnego zestawu wystąpień.
* (opcjonalnie): **`connection-string-setting`** Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* (opcjonalnie): **`task-hub-name`** Nazwa Durable Functionsego centrum zadań do użycia. Wartość domyślna to `DurableFunctionsHub`. Można ją również ustawić w pliku [host. JSON](durable-functions-bindings.md#host-json)przy użyciu DurableTask: HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Zapytania o wystąpienia z filtrami

Co zrobić, jeśli nie potrzebujesz wszystkich informacji, które mogą być używane przez zapytanie wystąpienia standardowego? Na przykład co zrobić, jeśli szukasz tylko czasu utworzenia aranżacji lub stanu środowiska uruchomieniowego aranżacji? Możesz zawęzić zapytanie, stosując filtry.

`getStatusBy` Użyj metody `GetStatusAsync` (.NET) lub (JavaScript), aby uzyskać listę wystąpień aranżacji zgodnych z zestawem wstępnie zdefiniowanych filtrów.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

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

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

W Azure Functions Core Tools można również użyć `durable get-instances` polecenia z filtrami. Oprócz `top`powyższych `connection-string-setting``created-after` `runtime-status`parametrów, `created-before`, i `task-hub-name` , można użyć trzech parametrów filtru (, i). `continuation-token`

* (opcjonalnie): **`created-after`** Pobierz wystąpienia utworzone po tej dacie/godzinie (UTC). Zaakceptowane daty i godziny w formacie ISO 8601.
* (opcjonalnie): **`created-before`** Pobierz wystąpienia utworzone przed tą datą i godziną (UTC). Zaakceptowane daty i godziny w formacie ISO 8601.
* (opcjonalnie): **`runtime-status`** Pobierz wystąpienia z określonym stanem (na przykład uruchomioną lub ukończoną). Może zapewnić wiele stanów (rozdzielonych spacjami).
* (opcjonalnie): **`top`** Liczba pobranych wystąpień dla żądania. Wartość domyślna to 10.
* (opcjonalnie): **`continuation-token`** Token wskazujący stronę lub sekcję wystąpień do pobrania. Każde `get-instances` wykonanie zwraca token do następnego zestawu wystąpień.
* (opcjonalnie): **`connection-string-setting`** Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* (opcjonalnie): **`task-hub-name`** Nazwa Durable Functionsego centrum zadań do użycia. Wartość domyślna to `DurableFunctionsHub`. Można ją również ustawić w pliku [host. JSON](durable-functions-bindings.md#host-json)przy użyciu DurableTask: HubName.

Jeśli nie podano żadnych filtrów (`created-after`, `created-before`lub `runtime-status`), polecenie po prostu pobiera `top` wystąpienia bez względu na stan środowiska uruchomieniowego lub czas utworzenia.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Przerwij wystąpienia

Jeśli masz wystąpienie aranżacji, które trwa zbyt długo lub musisz zatrzymać je przed zakończeniem z dowolnego powodu, będziesz mieć możliwość jej zakończenia.

Można użyć metody [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) klasy [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) lub `terminate` metody `DurableOrchestrationClient` klasy (JavaScript). Dwa parametry są `instanceId` `reason` i ciągiem, które są zapisywane w dziennikach i w stanie wystąpienia. Przerwane wystąpienie zatrzymuje działanie zaraz `await` po osiągnięciu następnego (.NET) lub `yield` (JavaScript) punktu, lub kończy natychmiast, jeśli jest już w `await` lub `yield`.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> Przerwanie wystąpienia nie jest obecnie propagowane. Funkcje działania i podzbiory są wykonywane do zakończenia, bez względu na to, czy zostało przerwane wystąpienie aranżacji, które je wywołało.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Możesz również przerwać wystąpienie aranżacji bezpośrednio za pomocą polecenia [Azure Functions Core Tools](../functions-run-local.md) `durable terminate` . Przyjmuje następujące parametry:

* (wymagane): **`id`** Identyfikator wystąpienia aranżacji do przerwania.
* (opcjonalnie): **`reason`** Przyczyna zakończenia.
* (opcjonalnie): **`connection-string-setting`** Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* (opcjonalnie): **`task-hub-name`** Nazwa Durable Functionsego centrum zadań do użycia. Wartość domyślna to `DurableFunctionsHub`. Można ją również ustawić w pliku [host. JSON](durable-functions-bindings.md#host-json)przy użyciu DurableTask: HubName.

Następujące polecenie kończy wystąpienie aranżacji o IDENTYFIKATORze 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Wysyłanie zdarzeń do wystąpień

W niektórych scenariuszach ważne jest, aby funkcja programu Orchestrator mogła czekać i słuchać zdarzeń zewnętrznych. Obejmuje to [funkcje monitorowania](durable-functions-overview.md#monitoring) i funkcje, które oczekują na [interakcję przez człowieka](durable-functions-overview.md#human).

Wysyłać powiadomienia o zdarzeniach do uruchomionych wystąpień za pomocą metody [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) klasy [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET `raiseEvent` ) lub metody `DurableOrchestrationClient` klasy (JavaScript). Wystąpienia, które mogą obsługiwać te zdarzenia, są te, które oczekują na wywołanie [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) `waitForExternalEvent` lub (JavaScript).

Parametry do [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) i `raiseEvent` (JavaScript) są następujące:

* **Identyfikator wystąpienia**: Unikatowy identyfikator wystąpienia.
* **EventName**: Nazwa zdarzenia do wysłania.
* **EVENTDATA**: Ładunek do serializacji JSON do wysłania do wystąpienia.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!IMPORTANT]
> Jeśli nie istnieje wystąpienie aranżacji o określonym IDENTYFIKATORze wystąpienia lub jeśli wystąpienie nie oczekuje na określoną nazwę zdarzenia, komunikat o zdarzeniu zostanie odrzucony. Aby uzyskać więcej informacji na temat tego zachowania, zobacz problem z usługą [GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Możesz również podnieść zdarzenie do wystąpienia aranżacji bezpośrednio za pomocą polecenia [Azure Functions Core Tools](../functions-run-local.md) `durable raise-event` . Przyjmuje następujące parametry:

* (wymagane): **`id`** Identyfikator wystąpienia aranżacji.
* (opcjonalnie): **`event-name`** Nazwa zdarzenia do podniesienia. Wartość domyślna to `$"Event_{RandomGUID}"`.
* (opcjonalnie): **`event-data`** Dane do wysłania do wystąpienia aranżacji. Może to być ścieżka do pliku JSON lub można dostarczyć dane bezpośrednio w wierszu polecenia.
* (opcjonalnie): **`connection-string-setting`** Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* (opcjonalnie): **`task-hub-name`** Nazwa Durable Functionsego centrum zadań do użycia. Wartość domyślna to `DurableFunctionsHub`. Można ją również ustawić w pliku [host. JSON](durable-functions-bindings.md#host-json)przy użyciu DurableTask: HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Poczekaj na zakończenie aranżacji

W długotrwałych aranżacjach warto oczekiwać i uzyskać wyniki aranżacji. W takich przypadkach warto również zdefiniować przedział czasu dla aranżacji. W przypadku przekroczenia limitu czasu, zamiast wyników należy zwrócić stan aranżacji.

Klasa [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) uwidacznia interfejs API [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) na platformie .NET. Tego interfejsu API można użyć do uzyskania danych wyjściowych z wystąpienia aranżacji synchronicznie. W języku JavaScript `DurableOrchestrationClient` Klasa `waitForCompletionOrCreateCheckStatusResponse` udostępnia interfejs API do tego samego celu. Gdy nie są ustawione, metody używają wartości domyślnej 10 sekund dla `timeout`i 1 sekunda dla. `retryInterval`  

Oto przykład funkcji wyzwalacza HTTP, która pokazuje, jak używać tego interfejsu API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Wywołaj funkcję z następującym wierszem. Użyj 2 sekund dla limitu czasu i 0,5 sekund dla interwału ponawiania próby:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

W zależności od czasu wymaganego do uzyskania odpowiedzi z wystąpienia aranżacji istnieją dwa przypadki:

* Wystąpienia aranżacji zakończone w określonym limicie czasu (w tym przypadku 2 sekund), a odpowiedź to rzeczywiste dane wyjściowe wystąpienia aranżacji, dostarczone synchronicznie:

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

* Wystąpienia aranżacji nie mogą zakończyć się w określonym limicie czasu, a odpowiedź jest domyślnie opisana w temacie [odnajdowanie adresów URL interfejsu API protokołu HTTP](durable-functions-http-api.md):

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
> Format adresów URL elementu webhook może się różnić w zależności od używanej wersji hosta Azure Functions. Poprzedni przykład dotyczy hosta Azure Functions 2. x.

## <a name="retrieve-http-management-webhook-urls"></a>Pobierz adresy URL elementu webhook zarządzania HTTP

Możesz użyć systemu zewnętrznego do monitorowania lub podnoszenia zdarzeń do aranżacji. Systemy zewnętrzne mogą komunikować się z Durable Functions za pośrednictwem adresów URL elementu webhook, które są częścią domyślnej odpowiedzi opisanej w [odnajdowaniu adresów URL interfejsu API protokołu HTTP](durable-functions-http-api.md). Jednak adresy URL elementu webhook mogą być również programowo dostępne w kliencie aranżacji lub w funkcji działania. W tym celu należy użyć metody [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) klasy [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) lub `createHttpManagementPayload` metody `DurableOrchestrationClient` klasy (JavaScript).

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) i `createHttpManagementPayload` mają jeden parametr:

* **Identyfikator wystąpienia**: Unikatowy identyfikator wystąpienia.

Metody zwracają wystąpienie [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) lub obiekt (JavaScript) z następującymi właściwościami ciągu:

* **Identyfikator**: Identyfikator wystąpienia aranżacji (powinien być taki sam jak `InstanceId` dane wejściowe).
* **StatusQueryGetUri**: Adres URL stanu wystąpienia aranżacji.
* **SendEventPostUri**: Adres URL "zgłoś zdarzenie" wystąpienia aranżacji.
* **TerminatePostUri**: Adres URL "Przerwij" wystąpienia aranżacji.
* **RewindPostUri**: Adres URL "przewijania do tyłu" wystąpienia aranżacji.

Funkcje działania mogą wysyłać wystąpienia tych obiektów do systemów zewnętrznych w celu monitorowania lub podnoszenia zdarzeń do aranżacji:

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

### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

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

## <a name="rewind-instances-preview"></a>Przewiń do tyłu wystąpienia (wersja zapoznawcza)

Jeśli masz błąd aranżacji z nieoczekiwanej przyczyny, możesz przewinąć wystąpienie do poprzedniego stanu w dobrej kondycji, korzystając z interfejsu API skompilowanego do tego celu.

> [!NOTE]
> Ten interfejs API nie jest przeznaczony do zastępowania w celu zapewnienia prawidłowej obsługi błędów i zasad ponawiania. Zamiast tego jest przeznaczona do użycia tylko w przypadkach, w których wystąpienia aranżacji kończą się niepowodzeniem z nieoczekiwanych przyczyn. Aby uzyskać więcej informacji na temat obsługi błędów i zasad ponawiania, zobacz temat [Obsługa błędów](durable-functions-error-handling.md) .

Użyj interfejsu API [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) `rewindAsync` lub (JavaScript), aby przełączyć aranżację do stanu *uruchomienia* . Ponownie uruchom błędy wykonywania działania lub podaranżacji, które spowodowały błąd aranżacji.

Załóżmy na przykład, że masz przepływ pracy obejmujący serię [zatwierdzeń ludzkich](durable-functions-overview.md#human). Załóżmy, że istnieje szereg funkcji działania, które powiadamiają kogoś o konieczności zatwierdzenia i oczekują odpowiedzi w czasie rzeczywistym. Po odebraniu odpowiedzi przez wszystkie działania zatwierdzenia lub przekroczeniu limitu czasu inne działanie nie powiedzie się z powodu błędnej konfiguracji aplikacji, na przykład parametrów połączenia z bazą danych. Wynikiem jest niepowodzenie aranżacji w przepływie pracy. Za pomocą interfejsu API `rewindAsync` (.NET)lub(JavaScript),administratoraplikacjimożenaprawićbłądkonfiguracjiiprzewinąćniepowodzeniearanżacjidostanubezpośrednioprzedawarią.`RewindAsync` Żadna z kroków związanych z interakcją przez człowieka nie musi być ponownie zatwierdzana, a aranżacja może teraz zakończyć się pomyślnie.

> [!NOTE]
> Funkcja *przewijania do tyłu* nie obsługuje zawijania wystąpień aranżacji korzystających z trwałych czasomierzy.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Możesz również bezpośrednio przewinąć wystąpienie aranżacji przy użyciu polecenia [Azure Functions Core Tools](../functions-run-local.md) `durable rewind` . Przyjmuje następujące parametry:

* (wymagane): **`id`** Identyfikator wystąpienia aranżacji.
* (opcjonalnie): **`reason`** Przyczyna odwinięcia wystąpienia aranżacji.
* (opcjonalnie): **`connection-string-setting`** Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* (opcjonalnie): **`task-hub-name`** Nazwa Durable Functionsego centrum zadań do użycia. Wartość domyślna to `DurableFunctionsHub`. Można ją również ustawić w pliku [host. JSON](durable-functions-bindings.md#host-json)przy użyciu DurableTask: HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Przeczyść historię wystąpień

Aby usunąć wszystkie dane skojarzone z aranżacją, można przeczyścić historię wystąpień. Na przykład możesz chcieć pozbyć się z wierszy tabeli platformy Azure i dużych obiektów BLOB komunikatów, jeśli istnieją. Aby to zrobić, użyj interfejsu API [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) .

> [!NOTE]
> Interfejs API jest obecnie dostępny tylko dla programu C# `PurgeInstanceHistoryAsync`

 Metoda ma dwa przeciążenia. Pierwszy z nich Przeczyszcza historię według identyfikatora wystąpienia aranżacji:

### <a name="c"></a>C#

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

Drugi przykład pokazuje funkcję wyzwalaną przez czasomierz, która Przeczyszcza historię dla wszystkich wystąpień aranżacji, które zakończyły się po upływie określonego interwału czasu. W tym przypadku usuwa dane dla wszystkich wystąpień zakończonych 30 lub więcej dni temu. Zaplanowano uruchomienie raz dziennie, przy 12% AM:

### <a name="c"></a>C#

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
> Aby proces funkcji wyzwolonej pomyślnie zakończył się powodzeniem, stan środowiska uruchomieniowego musi być zakończony, **zakończony**lubniepowodzenie.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Historię wystąpienia aranżacji można przeczyścić, używając polecenia [Azure Functions Core Tools](../functions-run-local.md) `durable purge-history` . Podobnie jak w drugim C# przykładzie w poprzedniej sekcji, Przeczyszcza historię wszystkich wystąpień aranżacji utworzonych w określonym przedziale czasu. Możliwe jest dalsze filtrowanie przeczyszczonych wystąpień według stanu środowiska uruchomieniowego. Polecenie ma kilka parametrów:

* (opcjonalnie): **`created-after`** Przeczyść historię wystąpień utworzonych po tej dacie/godzinie (UTC). Zaakceptowane daty i godziny w formacie ISO 8601.
* (opcjonalnie): **`created-before`** Przeczyść historię wystąpień utworzonych przed tą datą i godziną (UTC). Zaakceptowane daty i godziny w formacie ISO 8601.
* (opcjonalnie): **`runtime-status`** Przeczyść historię wystąpień z określonym stanem (na przykład uruchomioną lub ukończoną). Może zapewnić wiele stanów (rozdzielonych spacjami).
* (opcjonalnie): **`connection-string-setting`** Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* (opcjonalnie): **`task-hub-name`** Nazwa Durable Functionsego centrum zadań do użycia. Wartość domyślna to `DurableFunctionsHub`. Można ją również ustawić w pliku [host. JSON](durable-functions-bindings.md#host-json)przy użyciu DurableTask: HubName.

Następujące polecenie usuwa historię wszystkich wystąpień zakończonych niepowodzeniem, które zostały utworzone przed 14 listopada 2018 o 7:35 PM (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Usuwanie centrum zadań

Za pomocą polecenia [Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` można usunąć wszystkie artefakty magazynu skojarzone z konkretnym centrum zadań. Obejmuje to tabele, kolejki i obiekty blob usługi Azure Storage. Polecenie ma dwa parametry:

* (opcjonalnie): **`connection-string-setting`** Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* (opcjonalnie): **`task-hub-name`** Nazwa Durable Functionsego centrum zadań do użycia. Wartość domyślna to `DurableFunctionsHub`. Można ją również ustawić w pliku [host. JSON](durable-functions-bindings.md#host-json)przy użyciu DurableTask: HubName.

Następujące polecenie usuwa wszystkie dane usługi Azure Storage skojarzone z `UserTest` centrum zadań.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak obsługiwać przechowywanie wersji](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Wbudowana dokumentacja interfejsu API protokołu HTTP do zarządzania wystąpieniami](durable-functions-http-api.md)