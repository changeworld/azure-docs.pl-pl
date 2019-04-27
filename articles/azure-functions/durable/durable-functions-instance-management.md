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
origin.date: 12/07/2018
ms.date: 03/19/2019
ms.author: v-junlch
ms.openlocfilehash: ee96bc5e17051ab37be34eecbb8e4fe35599cd5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730773"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Zarządzanie wystąpieniami w funkcje trwałe na platformie Azure

Jeśli używasz [funkcje trwałe](durable-functions-overview.md) rozszerzenie dla usługi Azure Functions lub chcesz uruchomić w ten sposób, upewnij się, otrzymujesz najlepszego wykorzystania z niej. Aby zoptymalizować wystąpień aranżacji funkcje trwałe, więcej informacji na temat sposobu zarządzania nimi. W tym artykule przechodzi do szczegółów każdej operacji zarządzania wystąpienia.

Możesz zacząć i zakończyć wystąpienia, na przykład, i można tworzyć zapytania wystąpienia, w tym możliwość kwerendy wszystkie wystąpienia i zapytania wystąpienia za pomocą filtrów. Ponadto można wysyła zdarzenia do wystąpienia, Czekaj na ukończenie aranżacji i pobrać adresy URL elementu webhook zarządzania HTTP. W tym artykule opisano innych operacji zarządzania, zbyt, tym przewijanie wystąpień, czyszczenie historii wystąpienia i usuwanie Centrum zadania.

Funkcje trwałe masz opcje dotyczące sposobu wdrażania każdego z tych operacji zarządzania. Ten artykuł zawiera przykłady z zastosowaniem [podstawowych narzędzi usługi Azure Functions](../functions-run-local.md) dla obu platformy .NET (C#) i języka JavaScript.

## <a name="start-instances"></a>Uruchomić wystąpienia

Jest ważne można było uruchomić wystąpienie aranżacji. Często można to zrobić podczas korzystania z wiązania funkcje trwałe w wyzwalaczu innej funkcji.

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) metody [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) lub `startNew` na `DurableOrchestrationClient` (JavaScript) uruchamia nowe wystąpienie. Uzyskiwanie wystąpienia tej klasy przy użyciu `orchestrationClient` powiązania. Wewnętrznie ta metoda umieszczeniu komunikat do kolejki kontroli, a następnie uruchamiają początku funkcji o podanej nazwie, która używa `orchestrationTrigger` wyzwolić powiązania.

Ta operacja async uzupełnia, gdy pomyślnie zaplanowano procesu aranżacji. Na początek procesu aranżacji w ciągu 30 sekund. Jeśli zajmuje więcej czasu, zostanie wyświetlony `TimeoutException`.

> [!WARNING]
> Wdrażając aplikacje lokalnie w języku JavaScript, ustaw zmienną środowiskową `WEBSITE_HOSTNAME` do `localhost:<port>` (na przykład `localhost:7071`) na korzystanie z metod `DurableOrchestrationClient`. Aby uzyskać więcej informacji na temat tego wymagania, zobacz [problem w usłudze GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="net"></a>.NET

Parametry [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) są następujące:

* **Nazwa**: Nazwa funkcji programu orchestrator do zaplanowania.
* **Dane wejściowe**: Dowolne dane serializacji JSON, który powinien zostać przekazany jako dane wejściowe do funkcji programu orchestrator.
* **Identyfikator instanceId**: (Opcjonalnie) Unikatowy identyfikator wystąpienia. Jeśli ten parametr nie jest określony, metoda używa losowy identyfikator.

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
* **Identyfikator instanceId**: (Opcjonalnie) Unikatowy identyfikator wystąpienia. Jeśli ten parametr nie jest określony, metoda używa losowy identyfikator.
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

> [!TIP]
> Losowy identyfikator na użytek identyfikatora wystąpienia. Pozwala to zagwarantować równy rozłożenia podczas skalowania funkcje programu orchestrator na wielu maszynach wirtualnych. Prawidłowego czasu, aby korzystać identyfikatorów wystąpień nielosową gdy jest identyfikator muszą pochodzić z zewnętrznego źródła, lub gdy w przypadku wdrażania [programu orchestrator z pojedynczego wystąpienia](durable-functions-singletons.md) wzorca.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Możesz także uruchomić bezpośrednio przy użyciu wystąpienia [podstawowych narzędzi usługi Azure Functions](../functions-run-local.md) `durable start-new` polecenia. Jego przyjmuje następujące parametry:

* **`function-name` (wymagane)** : Nazwa funkcji, które można uruchomić.
* **`input` (opcjonalnie)** : Dane wejściowe do funkcji jako wbudowane lub za pomocą pliku JSON. W przypadku plików, Dodaj prefiks do ścieżki do pliku za pomocą `@`, takich jak `@path/to/file.json`.
* **`id` (opcjonalnie)** : Identyfikator wystąpienia aranżacji. Jeśli ten parametr nie jest określony, polecenie używa losowy identyfikator GUID.
* **`connection-string-setting` (opcjonalnie)** : Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to AzureWebJobsStorage.
* **`task-hub-name` (opcjonalnie)** : Nazwa koncentratora zadań funkcje trwałe do użycia. Wartość domyślna to DurableFunctionsHub. Możesz również ustawić [host.json](durable-functions-bindings.md#host-json) przy użyciu durableTask:HubName.

> [!NOTE]
> Podstawowe narzędzia polecenia przyjęto założenie, że uruchamiasz je w katalogu głównym aplikacji funkcji. Jeśli jawnie określić `connection-string-setting` i `task-hub-name` parametrów polecenia można uruchomić z dowolnego katalogu. Mimo że można uruchomić te polecenia, bez uruchamiania hosta aplikacji funkcji, może się okazać, nie może obserwować niektóre efekty hosta nie jest uruchomiona. Na przykład `start-new` polecenia umieszczeniu komunikat rozpoczęcia do koncentratora docelowy zadania, ale aranżacji faktycznie nie działa, chyba, że proces hosta aplikacji funkcji uruchamiania, który może przetworzyć komunikatu.

Następujące polecenie uruchamia funkcji o nazwie HelloWorld i przekazuje zawartość pliku `counter-data.json` do niego:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Wystąpienia zapytania

W ramach nakładów pracy do zarządzania usługi aranżacji prawdopodobnie należy do zbierania informacji o stanie wystąpienie orchestration (na przykład, czy jego zwykle ukończone lub nie powiodło się).

[GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) metody [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klas (.NET) lub `getStatus` metody `DurableOrchestrationClient` klasy (JavaScript) zapytanie o stan aranżacji wystąpienie.

Trwa `instanceId` (wymagane), `showHistory` (opcjonalnie) `showHistoryOutput` (opcjonalnie) i `showInput` (opcjonalnie, tylko .NET) jako parametry.

* **`showHistory`**: Jeśli ustawiono `true`, odpowiedź zawiera historię wykonywania.
* **`showHistoryOutput`**: Jeśli ustawiono `true`, historię wykonywania zawiera danych wyjściowych działania.
* **`showInput`**: Jeśli ustawiono `false`, odpowiedzi nie będzie zawierał dane wejściowe funkcji. Wartość domyślna to `true`. (Tylko platforma .NET)

Metoda ta zwraca obiekt JSON z następującymi właściwościami:

* **Nazwa**: Nazwa funkcji programu orchestrator.
* **Identyfikator instanceId**: Identyfikator wystąpienia aranżacji (powinna być taka sama jak `instanceId` wejściowego).
* **Wartością CreatedTime**: Czas, w którym funkcja orkiestratora został uruchomiony.
* **LastUpdatedTime**: Czas, w którym aranżacji ostatni utworzono punkt kontrolny.
* **Dane wejściowe**: Dane wejściowe funkcji jako wartości JSON. To pole nie jest wypełniana w przypadku `showInput` ma wartość false.
* **CustomStatus**: Stan niestandardowej aranżacji w formacie JSON.
* **Dane wyjściowe**: Dane wyjściowe funkcji jako wartości JSON (Jeśli funkcja została ukończona). Jeśli funkcja programu orchestrator nie powiodła się, ta właściwość zawiera szczegóły błędu. Jeśli funkcja orkiestratora został zakończony, ta właściwość zawiera powód zakończenia (jeśli istnieje).
* **RuntimeStatus**: Jeden z następujących wartości:
  * **Oczekujące**: Wystąpienie zostało zaplanowane, ale nie zostało jeszcze uruchomione uruchomiona.
  * **Uruchamianie**: Wystąpienie rozpoczęło działanie.
  * **Ukończono**: Wystąpienie zostało zakończone normalnie.
  * **ContinuedAsNew**: Wystąpienie uruchomieniu się o nowych historii. Jest to stan przejściowy.
  * **Niepowodzenie**: Wystąpienie nie powiodło się z powodu błędu.
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

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Istnieje również możliwość uzyskać stan wystąpienia aranżacji bezpośrednio przy użyciu [podstawowych narzędzi usługi Azure Functions](../functions-run-local.md) `durable get-runtime-status` polecenia. Jego przyjmuje następujące parametry:

* **`id` (wymagane)** : Identyfikator wystąpienia aranżacji.
* **`show-input` (opcjonalnie)** : Jeśli ustawiono `true`, odpowiedź zawiera dane wejściowe funkcji. Wartość domyślna to `false`.
* **`show-output` (opcjonalnie)** : Jeśli ustawiono `true`, odpowiedź zawiera dane wyjściowe funkcji. Wartość domyślna to `false`.
* **`connection-string-setting` (opcjonalnie)** : Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* **`task-hub-name` (opcjonalnie)** : Nazwa koncentratora zadań funkcje trwałe do użycia. Wartość domyślna to `DurableFunctionsHub`. Ponadto można ustawić w [host.json](durable-functions-bindings.md#host-json), za pomocą durableTask:HubName.

Następujące polecenie pobiera stan wystąpienia przy użyciu Identyfikatora wystąpienia aranżacji 0ab8c55a66644d68a3a8b220b12d209c (w tym dane wejściowe i wyjściowe). Przyjęto założenie, że używasz `func` polecenie z katalogu głównego aplikacji funkcji:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Możesz użyć `durable get-history` polecenie, aby pobrać historii wystąpienie aranżacji. Jego przyjmuje następujące parametry:

* **`id` (wymagane)** : Identyfikator wystąpienia aranżacji.
* **`connection-string-setting` (opcjonalnie)** : Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* **`task-hub-name` (opcjonalnie)** : Nazwa koncentratora zadań funkcje trwałe do użycia. Wartość domyślna to `DurableFunctionsHub`. Może także być ustawiona w host.json, za pomocą durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Wszystkie wystąpienia zapytania

Zamiast jedno wystąpienie zapytania w Twojej orkiestracji w czasie może okazać się bardziej efektywne kwerendy wszystkie z nich jednocześnie.

Możesz użyć `GetStatusAsync` (.NET) lub `getStatusAll` metoda (JavaScript) do wykonywania zapytań stanami wszystkich wystąpień aranżacji. Na platformie .NET, możesz przekazać `CancellationToken` obiektu w przypadku, gdy chcesz anulować. Metoda ta zwraca obiekty z tymi samymi właściwościami co `GetStatusAsync` metody z parametrami.

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

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Istnieje również możliwość wystąpień zapytania, używając [podstawowych narzędzi usługi Azure Functions](../functions-run-local.md) `durable get-instances` polecenia. Jego przyjmuje następujące parametry:

* **`top` (opcjonalnie)** : To polecenie obsługuje stronicowanie. Ten parametr odnosi się do liczby wystąpień pobrane na żądanie. Wartość domyślna wynosi 10.
* **`continuation-token` (opcjonalnie)** : Token, aby wskazać stronę lub części wystąpień do pobrania. Każdy `get-instances` wykonanie zwraca token do następnego zestawu wystąpień.
* **`connection-string-setting` (opcjonalnie)** : Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* **`task-hub-name` (opcjonalnie)** : Nazwa koncentratora zadań funkcje trwałe do użycia. Wartość domyślna to `DurableFunctionsHub`. Ponadto można ustawić w [host.json](durable-functions-bindings.md#host-json), za pomocą durableTask:HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Wystąpienia zapytań z filtrami

Co zrobić, jeśli nie potrzebujesz wszystkie informacje, które zapewniają zapytania wystąpienia standard? Na przykład co się stanie, jeśli po prostu szukasz podczas tworzenia aranżacji lub stan czasu wykonywania aranżacji? Zapytania można ograniczyć, stosując filtry.

Użyj `GetStatusAsync` (.NET) lub `getStatusBy` metoda (JavaScript) w celu uzyskania listy wystąpień aranżacji, które pasują zestaw wstępnie zdefiniowanych filtrów.

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

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

W funkcji podstawowych narzędzi usługi Azure, można również użyć `durable get-instances` polecenia za pomocą filtrów. Oprócz wyżej wymienionych `top`, `continuation-token`, `connection-string-setting`, i `task-hub-name` parametry, można użyć trzy parametry filtru (`created-after`, `created-before`, i `runtime-status`).

* **`created-after` (opcjonalnie)** : Pobieranie wystąpień utworzonych po tej daty/godziny (UTC). ISO 8601 formacie Data/Godzina zaakceptowane.
* **`created-before` (opcjonalnie)** : Pobieranie wystąpienia utworzone przed tym data/godzina (UTC). ISO 8601 formacie Data/Godzina zaakceptowane.
* **`runtime-status` (opcjonalnie)** : Pobieranie wystąpień z określonym statusem (na przykład uruchomiona lub ukończone). Można podać wiele stanów (oddzielone spacjami).
* **`top` (opcjonalnie)** : Pobrać liczby wystąpień na żądanie. Wartość domyślna wynosi 10.
* **`continuation-token` (opcjonalnie)** : Token, aby wskazać stronę lub części wystąpień do pobrania. Każdy `get-instances` wykonanie zwraca token do następnego zestawu wystąpień.
* **`connection-string-setting` (opcjonalnie)** : Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* **`task-hub-name` (opcjonalnie)** : Nazwa koncentratora zadań funkcje trwałe do użycia. Wartość domyślna to `DurableFunctionsHub`. Ponadto można ustawić w [host.json](durable-functions-bindings.md#host-json), za pomocą durableTask:HubName.

Jeśli nie podasz żadnych filtrów (`created-after`, `created-before`, lub `runtime-status`), po prostu pobiera polecenie `top` wystąpień, a nie w odniesieniu do czasu utworzenia lub stan środowiska uruchomieniowego.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Zakończenie wystąpień

Jeśli masz wystąpienie aranżacji, który trwa zbyt długo, aby uruchomić lub wystarczy ją zatrzymać przed ukończeniem jakiegokolwiek powodu, istnieje możliwość do jej zakończenia.

Możesz użyć [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) metody [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klas (.NET) lub `terminate` metody `DurableOrchestrationClient` klasy (JavaScript). Są dwa parametry `instanceId` i `reason` parametry, które są zapisywane do dzienników i stan wystąpienia. Wystąpienie zakończone zatrzymane, zaraz po osiągnięciu następnego `await` (.NET) lub `yield` punkt (JavaScript) lub jest on kończy działanie natychmiast, jeśli jest już na `await` lub `yield`.

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
> Obecnie nie propagować zakończenia wystąpienia. Działanie funkcji i aranżacji podrzędnych zostało ukończone, niezależnie od tego, czy został zakończony wystąpienie aranżacji, które je wywołuje.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Można także zakończyć wystąpienie aranżacji bezpośrednio, za pomocą [podstawowych narzędzi usługi Azure Functions](../functions-run-local.md) `durable terminate` polecenia. Jego przyjmuje następujące parametry:

* **`id` (wymagane)** : Identyfikator wystąpienia aranżacji, aby zakończyć.
* **`reason` (opcjonalnie)** : Powód zakończenia.
* **`connection-string-setting` (opcjonalnie)** : Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* **`task-hub-name` (opcjonalnie)** : Nazwa koncentratora zadań funkcje trwałe do użycia. Wartość domyślna to `DurableFunctionsHub`. Ponadto można ustawić w [host.json](durable-functions-bindings.md#host-json), za pomocą durableTask:HubName.

Następujące polecenie kończy się aranżacji wystąpienia o identyfikatorze 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Wysyłanie zdarzeń do wystąpień

W niektórych scenariuszach ważne jest dla funkcji programu orchestrator być w stanie oczekiwania oraz nasłuchiwać zdarzeń zewnętrznych. Obejmuje to [monitorowania funkcji](durable-functions-concepts.md#monitoring) i funkcje, które oczekują na [z reakcji człowieka](durable-functions-concepts.md#human).

Wysyłaj powiadomienia o zdarzeniach do uruchamiania wystąpień przy użyciu [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) metody [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klas (.NET) lub `raiseEvent` metody `DurableOrchestrationClient` klasy () JavaScript). Wystąpienia, obsługujące te zdarzenia są te, które oczekują na wywołanie [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) lub `waitForExternalEvent` (JavaScript).

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

> [!IMPORTANT]
> Jeśli żadne wystąpienie aranżacji o identyfikatorze określone wystąpienie lub wystąpienia nie oczekuje się od nazwy określonego zdarzenia, komunikat o zdarzeniu zostanie odrzucony. Aby uzyskać więcej informacji na temat tego zachowania, zobacz [problem w usłudze GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Można także podnieść zdarzenie do wystąpienia aranżacji bezpośrednio, za pomocą [podstawowych narzędzi usługi Azure Functions](../functions-run-local.md) `durable raise-event` polecenia. Jego przyjmuje następujące parametry:

* **`id` (wymagane)** : Identyfikator wystąpienia aranżacji.
* **`event-name` (opcjonalnie)** : Nazwa zdarzenia, aby zgłosić. Wartość domyślna to `$"Event_{RandomGUID}"`.
* **`event-data` (opcjonalnie)** : Dane do wysłania do wystąpienia aranżacji. Może to być ścieżka do pliku JSON lub danych może zapewnić bezpośrednio w wierszu polecenia.
* **`connection-string-setting` (opcjonalnie)** : Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* **`task-hub-name` (opcjonalnie)** : Nazwa koncentratora zadań funkcje trwałe do użycia. Wartość domyślna to `DurableFunctionsHub`. Ponadto można ustawić w [host.json](durable-functions-bindings.md#host-json), za pomocą durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Czekaj na ukończenie aranżacji

W aranżacjach długotrwałych można poczekać i Pobierz wyniki aranżacji. W takich przypadkach warto także może zdefiniować okres limitu czasu na aranżację. Jeśli zostanie przekroczony limit czasu, stan aranżacji powinna być zwrócona zamiast wyniki.

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klasy ujawnia [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) interfejsu API na platformie .NET. Aby uzyskać rzeczywiste dane wyjściowe z wystąpienia aranżacji synchronicznie, można użyć tego interfejsu API. W języku JavaScript `DurableOrchestrationClient` klasy ujawnia `waitForCompletionOrCreateCheckStatusResponse` interfejsu API, w tym samym celu. Gdy nie są ustawione, metody, użyj wartości domyślnej 10 sekund na `timeout`i 1 sekundę `retryInterval`.  

Oto przykład funkcji wyzwalacza HTTP, który pokazuje, jak używać tego interfejsu API:

```C#
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace VSSample
{
    public static class HttpSyncStart
    {
        private const string Timeout = "timeout";
        private const string RetryInterval = "retryInterval";

        [FunctionName("HttpSyncStart")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/wait")]
            HttpRequestMessage req,
            [OrchestrationClient] DurableOrchestrationClientBase starter,
            string functionName,
            ILogger log)
        {
            // Function input comes from the request content.
            dynamic eventData = await req.Content.ReadAsAsync<object>();
            string instanceId = await starter.StartNewAsync(functionName, eventData);

            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

            TimeSpan timeout = GetTimeSpan(req, Timeout) ?? TimeSpan.FromSeconds(30);
            TimeSpan retryInterval = GetTimeSpan(req, RetryInterval) ?? TimeSpan.FromSeconds(1);
            
            return await starter.WaitForCompletionOrCreateCheckStatusResponseAsync(
                req,
                instanceId,
                timeout,
                retryInterval);
        }

        private static TimeSpan? GetTimeSpan(HttpRequestMessage request, string queryParameterName)
        {
            string queryParameterStringValue = request.RequestUri.ParseQueryString()[queryParameterName];
            if (string.IsNullOrEmpty(queryParameterStringValue))
            {
                return null;
            }

            return TimeSpan.FromSeconds(double.Parse(queryParameterStringValue));
        }
    }
}
```

```Javascript
const df = require("durable-functions");

const timeout = "timeout";
const retryInterval = "retryInterval";

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = await client.startNew(req.params.functionName, undefined, req.body);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    const timeoutInMilliseconds = getTimeInSeconds(req, timeout) || 30000;
    const retryIntervalInMilliseconds = getTimeInSeconds(req, retryInterval) || 1000;

    return client.waitForCompletionOrCreateCheckStatusResponse(
        context.bindingData.req,
        instanceId,
        timeoutInMilliseconds,
        retryIntervalInMilliseconds);
};

function getTimeInSeconds (req, queryParameterName) {
    const queryValue = req.query[queryParameterName];
    return queryValue
        ? queryValue // expected to be in seconds
        * 1000 : undefined;
}
```

Wywołaj funkcję o następujący wiersz. Użyj 2 sekundy na wartość limitu czasu i 0,5 sekund dla interwału ponawiania prób:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

W zależności od czas potrzebny na odpowiedź z wystąpienia aranżacji istnieją dwa przypadki:

* Wystąpienia aranżacji zakończyć w ciągu zdefiniowanego limitu czasu (w tym przypadku 2 sekundy), a odpowiedź jest rzeczywista aranżacji wystąpienie danych wyjściowych synchronicznie dostarczane:

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

* Wystąpienia orchestration nie może ukończyć przed upływem limitu czasu zdefiniowanego i odpowiedź jest domyślna jednego opisanego w [Odnajdywanie adresu URL interfejsu API HTTP](durable-functions-http-api.md):

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
> Format adresu URL elementu webhook, mogą się różnić w zależności od posiadanej wersji programu host usługi Azure Functions. Powyższy przykład dotyczy hosta 2.x usługi Azure Functions.

## <a name="retrieve-http-management-webhook-urls"></a>Pobieranie adresów URL elementu webhook zarządzania HTTP

Za pomocą zewnętrznego systemu do monitorowania lub wywoływać zdarzenia do aranżacji. Systemy zewnętrzne mogą się komunikować trwałe funkcje za pomocą adresów URL elementu webhook, będące częścią odpowiedzi domyślnej opisanego w [Odnajdywanie adresu URL interfejsu API HTTP](durable-functions-http-api.md). Jednak adresy URL elementu webhook również można uzyskać programistycznie w klient orkiestracji lub w funkcji działania. To zrobić za pomocą [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) metody [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klas (.NET) lub `createHttpManagementPayload` metody `DurableOrchestrationClient` klasy (JavaScript).

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) i `createHttpManagementPayload` mieć jeden parametr:

* **instanceId**: Unikatowy identyfikator wystąpienia.

Metody zwracają wystąpienie [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) lub obiekt (JavaScript), z następującymi właściwościami ciąg:

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

## <a name="rewind-instances-preview"></a>Przewiń do tyłu instances (wersja zapoznawcza)

Jeśli z powodu nieoczekiwanego błędu aranżacji, możesz *rewind* wystąpienia do stanu prawidłowego wcześniej przy użyciu interfejsu API utworzone w tym celu.

> [!NOTE]
> Ten interfejs API nie jest przeznaczony do zastąpienia obsługi błędów właściwe i zasad ponawiania. Przeciwnie jest przeznaczona do użycia tylko w przypadkach, gdzie wystąpień aranżacji się nie powieść nieoczekiwany powodów. Aby uzyskać szczegółowe informacje na temat zasad ponawiania prób i obsługi błędów, zobacz [obsługi błędów](durable-functions-error-handling.md) tematu.

Użyj [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) lub `rewindAsync` z powrotem do interfejsu API (JavaScript), aby umieścić aranżacji *systemem* stanu. Ponowne uruchomienie działania lub suborchestration błędów wykonywania, które spowodowały błąd aranżacji.

Na przykład, załóżmy, że masz przepływu pracy obejmujące serię [ludzi zatwierdzenia](durable-functions-concepts.md#human). Załóżmy, że istnieje szereg funkcji działań, które powiadamiają o kogoś zatwierdzenia jest wymagana, a następnie poczekaj się odpowiedzi w czasie rzeczywistym. Gdy wszystkie zatwierdzenia działań otrzymane odpowiedzi lub upłynął limit czasu, załóżmy, że działanie innego zakończy się niepowodzeniem z powodu błędnej konfiguracji aplikacji, takich jak nieprawidłowe parametry połączenia. Wynikiem jest błąd aranżacji szczegółowo przepływ pracy. Za pomocą `RewindAsync` (.NET) lub `rewindAsync` administratora (JavaScript) interfejsu API, aplikacji może naprawić ten błąd konfiguracji i przewijanie orchestration nie powiodło się, stan natychmiast przed awarią. Żaden z kroków interakcji człowieka muszą zostać ponownie uznany i organizowania może teraz zostać pomyślnie ukończony.

> [!NOTE]
> *Rewind* funkcji nie obsługuje przetwarzania aranżacji wystąpieniom używaj czasomierzy trwałe.

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

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Możesz również przewinąć wystąpienie aranżacji bezpośrednio przy użyciu [podstawowych narzędzi usługi Azure Functions](../functions-run-local.md) `durable rewind` polecenia. Jego przyjmuje następujące parametry:

* **`id` (wymagane)** : Identyfikator wystąpienia aranżacji.
* **`reason` (opcjonalnie)** : Przyczyna przewijanie wystąpienia aranżacji.
* **`connection-string-setting` (opcjonalnie)** : Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* **`task-hub-name` (opcjonalnie)** : Nazwa koncentratora zadań funkcje trwałe do użycia. Wartość domyślna to `DurableFunctionsHub`. Ponadto można ustawić w [host.json](durable-functions-bindings.md#host-json), za pomocą durableTask:HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Wyczyść historię wystąpienia

Aby usunąć wszystkie dane, które są skojarzone z aranżacji, można przeczyścić historię wystąpienia. Na przykład możesz chcieć usunąć wiersze tabeli platformy Azure i obiektów blob w dużych bloków komunikatów, jeśli takie istnieją. Aby to zrobić, należy użyć [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) interfejsu API.

> [!NOTE]
> `PurgeInstanceHistoryAsync` Interfejs API jest obecnie dostępna tylko w przypadku C#.

 Metoda ma dwa przeciążenia. Pierwsza z nich powoduje wyczyszczenie historii za pomocą Identyfikatora wystąpienia orchestration:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

Drugi przykład przedstawia funkcji wyzwalanej przez czasomierz, który powoduje wyczyszczenie historii dla wszystkich wystąpień aranżacji zakończone po określonym interwale. W tym przypadku usuwa dane dla wszystkich wystąpień ukończone co najmniej 30 dni temu. Jego uruchomienie zaplanowano raz dziennie, o 12: 00:

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
> Dla funkcji wyzwalanej przez czas procesu zakończyło się sukcesem, stan środowiska uruchomieniowego musi być **Ukończono**, **zwolniony**, lub **niepowodzenie**.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Mogą przeczyścić historię wystąpienie aranżacji przy użyciu [podstawowych narzędzi usługi Azure Functions](../functions-run-local.md) `durable purge-history` polecenia. Podobnie jak drugi C# przykładzie w poprzedniej sekcji Przeczyszcza historii dla wszystkich wystąpień aranżacji utworzone w określonym interwale. Przeczyszczone wystąpienia można dalej filtrować według stanu środowiska uruchomieniowego. Polecenie ma kilka parametrów:

* **`created-after` (opcjonalnie)** : Wyczyść historię wystąpień utworzonych po tej daty/godziny (UTC). ISO 8601 formacie Data/Godzina zaakceptowane.
* **`created-before` (opcjonalnie)** : Wyczyść historię wystąpienia utworzone przed tym data/godzina (UTC). ISO 8601 formacie Data/Godzina zaakceptowane.
* **`runtime-status` (opcjonalnie)** : Wyczyść historię wystąpień z określonym statusem (na przykład uruchomiona lub ukończone). Można podać wiele stanów (oddzielone spacjami).
* **`connection-string-setting` (opcjonalnie)** : Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* **`task-hub-name` (opcjonalnie)** : Nazwa koncentratora zadań funkcje trwałe do użycia. Wartość domyślna to `DurableFunctionsHub`. Ponadto można ustawić w [host.json](durable-functions-bindings.md#host-json), za pomocą durableTask:HubName.

Następujące polecenie usuwa historię wszystkich wystąpień zakończonych niepowodzeniem przed 14 listopada 2018 r. utworzono 7:35 PM (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Usunąć Centrum zadań

Za pomocą [podstawowych narzędzi usługi Azure Functions](../functions-run-local.md) `durable delete-task-hub` polecenia, możesz usunąć wszystkie artefakty magazynu skojarzonego z rozwiązaniem hub danego zadania. W tym usługi Azure storage tabel, kolejek i obiektów blob. Polecenie ma dwa parametry:

* **`connection-string-setting` (opcjonalnie)** : Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* **`task-hub-name` (opcjonalnie)** : Nazwa koncentratora zadań funkcje trwałe do użycia. Wartość domyślna to `DurableFunctionsHub`. Ponadto można ustawić w [host.json](durable-functions-bindings.md#host-json), za pomocą durableTask:HubName.

Następujące polecenie usuwa wszystkie dane usługi Azure storage skojarzonych z `UserTest` Centrum zadania.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak używać interfejsów API protokołu HTTP dla wystąpienia zarządzania](durable-functions-http-api.md)

<!-- Update_Description: wording update -->