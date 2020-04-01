---
title: Zarządzanie wystąpieniami w funkcjach trwałych — Azure
description: Dowiedz się, jak zarządzać wystąpieniami w rozszerzeniu Funkcje trwałe dla usługi Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 1837d342c4476633ee33a8579abe7389ac9bbddf
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476833"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Zarządzanie wystąpieniami w funkcjach trwałych na platformie Azure

Jeśli używasz rozszerzenia [funkcje trwałe](durable-functions-overview.md) dla usługi Azure Functions lub chcesz rozpocząć ten sposób, upewnij się, że otrzymujesz najlepsze użycie z niego. Można zoptymalizować wystąpienia aranżacji funkcji trwałych, dowiedzieć się więcej o tym, jak nimi zarządzać. Ten artykuł zawiera szczegółowe informacje na temat każdej operacji zarządzania wystąpieniami.

Można rozpocząć i zakończyć wystąpienia, na przykład i można wysyłać zapytania wystąpień, w tym możliwość kwerendy wszystkich wystąpień i wystąpień kwerendy z filtrów. Ponadto można wysyłać zdarzenia do wystąpień, czekać na zakończenie aranżacji i pobierać adresy URL elementów webhook zarządzania HTTP. W tym artykule opisano również inne operacje zarządzania, w tym przewijanie wystąpień, czyszczenie historii wystąpień i usuwanie centrum zadań.

W funkcji trwałych, masz opcje, jak chcesz zaimplementować każdą z tych operacji zarządzania. W tym artykule przedstawiono przykłady korzystające z [podstawowych narzędzi azure functions](../functions-run-local.md) dla platformy .NET (C#) i JavaScript.

## <a name="start-instances"></a>Początkowe wystąpienia

Ważne jest, aby móc rozpocząć wystąpienie aranżacji. Jest to często wykonywane, gdy używasz trwałe funkcje powiązania w wyzwalaczu innej funkcji.

Metoda `StartNewAsync` (.NET) `startNew` lub (JavaScript) na [powiązanie klienta aranżacji](durable-functions-bindings.md#orchestration-client) uruchamia nowe wystąpienie. Wewnętrznie ta metoda umieszcza w kolejce komunikat w kolejce formantu, który następnie wyzwala początek funkcji o określonej nazwie, która używa [powiązania wyzwalacza aranżacji](durable-functions-bindings.md#orchestration-trigger).

Ta operacja asynchroniczna kończy się po pomyślnym zaplanowaniu procesu aranżacji.

Parametry uruchamiania nowego wystąpienia aranżacji są następujące:

* **Nazwa**: Nazwa funkcji koordynatora do zaplanowania.
* **Dane wejściowe:** Wszelkie dane z serializable JSON, które powinny być przekazywane jako dane wejściowe do funkcji koordynatora.
* **InstanceId**: (Opcjonalnie) Unikatowy identyfikator wystąpienia. Jeśli ten parametr nie zostanie określony, metoda używa losowego identyfikatora.

> [!TIP]
> Użyj losowego identyfikatora dla identyfikatora wystąpienia. Identyfikatory wystąpienia losowe pomagają zapewnić równy rozkład obciążenia podczas skalowania funkcji koordynatora na wielu maszynach wirtualnych. Odpowiedni czas, aby użyć identyfikatorów wystąpienia nielosowych jest, gdy identyfikator musi pochodzić z zewnętrznego źródła lub podczas implementowania [wzorca programu orkiestrowego singleton.](durable-functions-singletons.md)

Poniższy kod jest przykładową funkcją, która uruchamia nowe wystąpienie aranżacji:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("HelloWorldQueueTrigger")]
public static async Task Run(
    [QueueTrigger("start-queue")] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> Poprzedni kod języka C# jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy `OrchestrationClient` użyć `DurableClient` atrybutu zamiast atrybutu `DurableOrchestrationClient` i zamiast `IDurableOrchestrationClient`parametru . Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>O ile nie określono inaczej, przykłady na tej stronie używają wyzwalacza HTTP z następującą funkcją.json.

**funkcja.json**

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": ["post"]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> W tym przykładzie jest przeznaczony dla funkcji trwałych w wersji 2.x. W wersji 1.x `orchestrationClient` użyj `durableClient`zamiast .

**indeks.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

---

### <a name="azure-functions-core-tools"></a>Podstawowe narzędzia funkcji platformy Azure

Wystąpienie można również uruchomić bezpośrednio za pomocą polecenia [Podstawowe narzędzia](../functions-run-local.md) `durable start-new` azure functions. Przyjmuje następujące parametry:

* (wymagane) : Nazwa funkcji do uruchomienia. ** `function-name` **
* (opcjonalnie) : Wejście do funkcji, w linii lub za pośrednictwem pliku JSON. ** `input` ** W przypadku plików dodaj prefiks do `@`ścieżki do `@path/to/file.json`pliku za pomocą , takiego jak .
* (opcjonalnie) : Identyfikator wystąpienia aranżacji. ** `id` ** Jeśli ten parametr nie zostanie określony, polecenie użyje losowego identyfikatora GUID.
* (opcjonalnie) : Nazwa ustawienia aplikacji zawierającego ciąg połączenia magazynu do użycia. ** `connection-string-setting` ** Wartość domyślna to AzureWebJobsStorage.
* (opcjonalnie) : Nazwa centrum zadań Funkcje trwałe do użycia. ** `task-hub-name` ** Wartość domyślna to DurableFunctionsHub. Można również ustawić to w [host.json](durable-functions-bindings.md#host-json) przy użyciu durableTask:HubName.

> [!NOTE]
> Polecenia Narzędzia podstawowe zakładają, że uruchamiasz je z katalogu głównego aplikacji funkcji. Jeśli jawnie `connection-string-setting` podasz `task-hub-name` i parametry, można uruchomić polecenia z dowolnego katalogu. Chociaż można uruchomić te polecenia bez uruchomionego hosta aplikacji funkcji, może się okazać, że nie można zaobserwować niektórych efektów, chyba że host jest uruchomiony. Na przykład `start-new` polecenie w kolejce komunikat start do centrum zadań docelowych, ale aranżacji faktycznie nie działa, chyba że istnieje uruchomiony proces hosta aplikacji funkcji, który może przetwarzać komunikat.

Następujące polecenie uruchamia funkcję o nazwie HelloWorld i przekazuje `counter-data.json` do niego zawartość pliku:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Wystąpienia kwerend

W ramach wysiłków na rzecz zarządzania aranżacjami, najprawdopodobniej trzeba będzie zebrać informacje o stanie wystąpienia aranżacji (na przykład, czy został ukończony normalnie lub nie powiodło się).

Metoda `GetStatusAsync` (.NET) `getStatus` lub (JavaScript) w powiązaniu [klienta aranżacji](durable-functions-bindings.md#orchestration-client) pyta o stan wystąpienia aranżacji.

Przyjmuje `instanceId` (wymagane), `showHistory` (opcjonalnie), `showHistoryOutput` (opcjonalnie) i `showInput` (opcjonalnie) jako parametry.

* **`showHistory`**: Jeśli `true`ustawiona na , odpowiedź zawiera historię wykonywania.
* **`showHistoryOutput`**: Jeśli `true`ustawiono na , historia wykonywania zawiera dane wyjściowe działania.
* **`showInput`**: Jeśli `false`ustawiona na , odpowiedź nie będzie zawierać danych wejściowych funkcji. Wartością domyślną jest `true`.

Metoda zwraca obiekt o następujących właściwościach:

* **Nazwa**: Nazwa funkcji koordynatora.
* **Identyfikator wystąpienia:** Identyfikator wystąpienia aranżacji (powinien być `instanceId` taki sam jak dane wejściowe).
* **CreatedTime**: Czas, w którym funkcja koordynatora rozpoczęła działanie.
* **LastUpdatedTime:** czas, w którym aranżacji ostatnio checkpointed.
* **Dane wejściowe:** Wejście funkcji jako wartość JSON. To pole nie jest `showInput` wypełniane, jeśli jest false.
* **CustomStatus**: Niestandardowy stan aranżacji w formacie JSON.
* **Wyjście:** Dane wyjściowe funkcji jako wartość JSON (jeśli funkcja została ukończona). Jeśli funkcja koordynatora nie powiodła się, ta właściwość zawiera szczegóły błędu. Jeśli funkcja koordynatora została zakończona, ta właściwość zawiera przyczynę zakończenia (jeśli istnieje).
* **RuntimeStatus**: Jedna z następujących wartości:
  * **Oczekujące:** Wystąpienie zostało zaplanowane, ale nie zostało jeszcze uruchomione.
  * **Uruchamianie:** Wystąpienie zostało uruchomione.
  * **Zakończone**: Wystąpienie zostało ukończone normalnie.
  * **ContinuedAsNew**: Wystąpienie zostało ponownie uruchomione z nową historią. Ten stan jest stanem przejściowym.
  * **Nie powiodło się:** wystąpienie nie powiodło się z powodu błędu.
  * **Zakończone**: Wystąpienie zostało nagle zatrzymane.
* **Historia**: Historia wykonania aranżacji. To pole jest `showHistory` wypełniane `true`tylko wtedy, gdy jest ustawione na .

Ta metoda `null` zwraca (.NET) lub `undefined` (JavaScript), jeśli wystąpienie nie istnieje.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("check-status-queue")] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> Poprzedni kod języka C# jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy `OrchestrationClient` użyć `DurableClient` atrybutu zamiast atrybutu `DurableOrchestrationClient` i zamiast `IDurableOrchestrationClient`parametru . Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

Zobacz [Uruchom wystąpienia](#javascript-function-json) dla konfiguracji function.json.

---

### <a name="azure-functions-core-tools"></a>Podstawowe narzędzia funkcji platformy Azure

Jest również możliwe, aby uzyskać stan wystąpienia aranżacji bezpośrednio, za pomocą polecenia [Podstawowe narzędzia](../functions-run-local.md) `durable get-runtime-status` azure functions. Przyjmuje następujące parametry:

* (wymagane) : Identyfikator wystąpienia aranżacji. ** `id` **
* (opcjonalnie) : `true`Jeśli ustawiono na , odpowiedź zawiera wejście funkcji. ** `show-input` ** Wartością domyślną jest `false`.
* (opcjonalnie) : `true`Jeśli ustawiono na , odpowiedź zawiera dane wyjściowe funkcji. ** `show-output` ** Wartością domyślną jest `false`.
* (opcjonalnie) : Nazwa ustawienia aplikacji zawierającego ciąg połączenia magazynu do użycia. ** `connection-string-setting` ** Wartość domyślna to `AzureWebJobsStorage`.
* (opcjonalnie) : Nazwa centrum zadań Funkcje trwałe do użycia. ** `task-hub-name` ** Wartość domyślna to `DurableFunctionsHub`. Można go również ustawić w [host.json](durable-functions-bindings.md#host-json), przy użyciu durableTask:HubName.

Następujące polecenie pobiera stan (w tym dane wejściowe i wyjściowe) wystąpienia o identyfikatorze wystąpienia aranżacji o identyfikatorze 0ab8c55a66644d68a3a8b220b12d209c. Przyjęto założenie, że `func` polecenie jest uruchamiane z katalogu głównego aplikacji funkcyjnej:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Za pomocą `durable get-history` polecenia można pobrać historię wystąpienia aranżacji. Przyjmuje następujące parametry:

* (wymagane) : Identyfikator wystąpienia aranżacji. ** `id` **
* (opcjonalnie) : Nazwa ustawienia aplikacji zawierającego ciąg połączenia magazynu do użycia. ** `connection-string-setting` ** Wartość domyślna to `AzureWebJobsStorage`.
* (opcjonalnie) : Nazwa centrum zadań Funkcje trwałe do użycia. ** `task-hub-name` ** Wartość domyślna to `DurableFunctionsHub`. Można go również ustawić w host.json, przy użyciu durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Kwerenda we wszystkich wystąpieniach

Zamiast kwerendy jedno wystąpienie w aranżacji w czasie, może okazać się bardziej wydajne do kwerendy wszystkich z nich naraz.

Za pomocą `GetStatusAsync` metody (.NET) lub `getStatusAll` (JavaScript) można zbadać stany wszystkich wystąpień aranżacji. W .NET można przekazać `CancellationToken` obiekt w przypadku, gdy chcesz go anulować. Metoda zwraca obiekty o tych samych właściwościach co `GetStatusAsync` metoda z parametrami.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

> [!NOTE]
> Poprzedni kod języka C# jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy `OrchestrationClient` użyć `DurableClient` atrybutu zamiast atrybutu `DurableOrchestrationClient` i zamiast `IDurableOrchestrationClient`parametru . Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Zobacz [Uruchom wystąpienia](#javascript-function-json) dla konfiguracji function.json.

---

### <a name="azure-functions-core-tools"></a>Podstawowe narzędzia funkcji platformy Azure

Jest również możliwe do bezpośredniego zapytania wystąpień, za pomocą polecenia [Podstawowe narzędzia](../functions-run-local.md) `durable get-instances` azure functions. Przyjmuje następujące parametry:

* (opcjonalnie) : To polecenie obsługuje stronicowanie. ** `top` ** Ten parametr odpowiada liczbie wystąpień pobranych na żądanie. Wartość domyślna to 10.
* (opcjonalnie) : Token wskazujący, która strona lub sekcja wystąpień ma być pobierana. ** `continuation-token` ** Każde `get-instances` wykonanie zwraca token do następnego zestawu wystąpień.
* (opcjonalnie) : Nazwa ustawienia aplikacji zawierającego ciąg połączenia magazynu do użycia. ** `connection-string-setting` ** Wartość domyślna to `AzureWebJobsStorage`.
* (opcjonalnie) : Nazwa centrum zadań Funkcje trwałe do użycia. ** `task-hub-name` ** Wartość domyślna to `DurableFunctionsHub`. Można go również ustawić w [host.json](durable-functions-bindings.md#host-json), przy użyciu durableTask:HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Wystąpienia kwerend z filtrami

Co zrobić, jeśli tak naprawdę nie potrzebujesz wszystkich informacji, które może dostarczyć zapytanie o wystąpienie standardowe? Na przykład co zrobić, jeśli po prostu szukasz czasu tworzenia aranżacji lub stanu środowiska uruchomieniowego aranżacji? Kwerendę można zawęzić, stosując filtry.

Użyj `GetStatusAsync` metody (.NET) lub `getStatusBy` (JavaScript), aby uzyskać listę wystąpień aranżacji, które pasują do zestawu wstępnie zdefiniowanych filtrów.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus> {
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

> [!NOTE]
> Poprzedni kod języka C# jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy `OrchestrationClient` użyć `DurableClient` atrybutu zamiast atrybutu `DurableOrchestrationClient` i zamiast `IDurableOrchestrationClient`parametru . Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Zobacz [Uruchom wystąpienia](#javascript-function-json) dla konfiguracji function.json.

---

### <a name="azure-functions-core-tools"></a>Podstawowe narzędzia funkcji platformy Azure

W narzędziach podstawowe usług Azure Functions `durable get-instances` można również użyć polecenia z filtrami. Oprócz `top`wyżej wymienionych parametrów `continuation-token` `connection-string-setting`, `task-hub-name` i parametrów można użyć`created-after`trzech `created-before`parametrów filtru ( , , i `runtime-status`).

* (opcjonalnie) : Pobierz wystąpienia utworzone po tej dacie/godzinie (UTC). ** `created-after` ** Iso 8601 sformatowany datetimes akceptowane.
* (opcjonalnie) : Pobierz wystąpienia utworzone przed tą datą/godziną (UTC). ** `created-before` ** Iso 8601 sformatowany datetimes akceptowane.
* (opcjonalnie) : Pobierz wystąpienia o określonym stanie (na przykład uruchomione lub ukończone). ** `runtime-status` ** Może zapewnić wiele stanów (oddzielone spacjami).
* (opcjonalnie) : Liczba wystąpień pobranych na żądanie. ** `top` ** Wartość domyślna to 10.
* (opcjonalnie) : Token wskazujący, która strona lub sekcja wystąpień ma być pobierana. ** `continuation-token` ** Każde `get-instances` wykonanie zwraca token do następnego zestawu wystąpień.
* (opcjonalnie) : Nazwa ustawienia aplikacji zawierającego ciąg połączenia magazynu do użycia. ** `connection-string-setting` ** Wartość domyślna to `AzureWebJobsStorage`.
* (opcjonalnie) : Nazwa centrum zadań Funkcje trwałe do użycia. ** `task-hub-name` ** Wartość domyślna to `DurableFunctionsHub`. Można go również ustawić w [host.json](durable-functions-bindings.md#host-json), przy użyciu durableTask:HubName.

Jeśli nie podasz żadnych`created-after`filtrów `created-before`( `runtime-status`, , lub `top` ), polecenie po prostu pobiera wystąpienia, bez względu na stan środowiska uruchomieniowego lub czas tworzenia.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Kończenie wystąpień

Jeśli masz wystąpienie aranżacji, które trwa zbyt długo, aby uruchomić lub po prostu trzeba go zatrzymać, zanim zakończy się z jakiegokolwiek powodu, masz możliwość, aby zakończyć go.

Można użyć `TerminateAsync` metody (.NET) `terminate` lub (JavaScript) [powiązania klienta aranżacji,](durable-functions-bindings.md#orchestration-client) aby zakończyć wystąpienia. Dwa parametry są `instanceId` i `reason` ciąg, które są zapisywane do dzienników i stanu wystąpienia.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("terminate-queue")] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Poprzedni kod języka C# jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy `OrchestrationClient` użyć `DurableClient` atrybutu zamiast atrybutu `DurableOrchestrationClient` i zamiast `IDurableOrchestrationClient`parametru . Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

Zobacz [Uruchom wystąpienia](#javascript-function-json) dla konfiguracji function.json.

---

Zakończone wystąpienie zostanie ostatecznie przejście `Terminated` do stanu. Jednak to przejście nie nastąpi natychmiast. Zamiast tego operacja zakończenia zostanie śmiga w kolejce w centrum zadań wraz z innymi operacjami dla tego wystąpienia. Można użyć [interfejsów API kwerendy wystąpienia,](#query-instances) aby wiedzieć, `Terminated` kiedy zakończone wystąpienie rzeczywiście osiągnął stan.

> [!NOTE]
> Zakończenie wystąpienia nie jest obecnie propagowane. Funkcje działania i podaranżacji uruchomić do zakończenia, niezależnie od tego, czy zostało zakończone wystąpienie aranżacji, który je nazwał.

### <a name="azure-functions-core-tools"></a>Podstawowe narzędzia funkcji platformy Azure

Można również zakończyć wystąpienie aranżacji bezpośrednio, za pomocą polecenia [Podstawowe narzędzia](../functions-run-local.md) `durable terminate` azure functions. Przyjmuje następujące parametry:

* (wymagane) : Identyfikator wystąpienia aranżacji do zakończenia. ** `id` **
* (opcjonalnie) : Powód rozwiązania umowy. ** `reason` **
* (opcjonalnie) : Nazwa ustawienia aplikacji zawierającego ciąg połączenia magazynu do użycia. ** `connection-string-setting` ** Wartość domyślna to `AzureWebJobsStorage`.
* (opcjonalnie) : Nazwa centrum zadań Funkcje trwałe do użycia. ** `task-hub-name` ** Wartość domyślna to `DurableFunctionsHub`. Można go również ustawić w [host.json](durable-functions-bindings.md#host-json), przy użyciu durableTask:HubName.

Następujące polecenie kończy wystąpienie aranżacji o identyfikatorze 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Wysyłanie zdarzeń do wystąpień

W niektórych scenariuszach ważne jest, aby funkcje programu orchestrator mogły czekać i słuchać zdarzeń zewnętrznych. Obejmuje to [funkcje monitora](durable-functions-overview.md#monitoring) i funkcje, które czekają na [interakcję z człowiekiem.](durable-functions-overview.md#human)

Wysyłaj powiadomienia o zdarzeniach do `RaiseEventAsync` uruchomionych wystąpień `raiseEvent` przy użyciu metody (.NET) lub metody (JavaScript) [powiązania klienta aranżacji](durable-functions-bindings.md#orchestration-client). Wystąpienia, które mogą obsługiwać te zdarzenia są `WaitForExternalEvent` te, które oczekują na `waitForExternalEvent` wywołanie (.NET) lub ulegając wywołaniu (JavaScript).

Parametry `RaiseEventAsync` (.NET) i `raiseEvent` (JavaScript) są następujące:

* **Identyfikator wystąpienia:** unikatowy identyfikator wystąpienia.
* **Nazwa zdarzenia:** Nazwa zdarzenia do wysłania.
* **EventData**: Ładunek seryjny JSON do wysłania do wystąpienia.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("event-queue")] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> Poprzedni kod języka C# jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy `OrchestrationClient` użyć `DurableClient` atrybutu zamiast atrybutu `DurableOrchestrationClient` i zamiast `IDurableOrchestrationClient`parametru . Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

Zobacz [Uruchom wystąpienia](#javascript-function-json) dla konfiguracji function.json.

---

> [!NOTE]
> Jeśli nie ma wystąpienia aranżacji o określonym identyfikatorze wystąpienia, komunikat o zdarzeniu zostanie odrzucony. Jeśli wystąpienie istnieje, ale nie jest jeszcze oczekiwanie na zdarzenie, zdarzenie będzie przechowywane w stanie wystąpienia, dopóki nie będzie gotowy do odebraniu i przetworzenia.

### <a name="azure-functions-core-tools"></a>Podstawowe narzędzia funkcji platformy Azure

Zdarzenie można również również wywołać bezpośrednio do wystąpienia aranżacji za pomocą polecenia [Podstawowe narzędzia](../functions-run-local.md) `durable raise-event` azure functions. Przyjmuje następujące parametry:

* (wymagane) : Identyfikator wystąpienia aranżacji. ** `id` **
* **`event-name`**: Nazwa wydarzenia do podniesienia.
* (opcjonalnie) : Dane do wysłania do wystąpienia aranżacji. ** `event-data` ** Może to być ścieżka do pliku JSON lub można podać dane bezpośrednio w wierszu polecenia.
* (opcjonalnie) : Nazwa ustawienia aplikacji zawierającego ciąg połączenia magazynu do użycia. ** `connection-string-setting` ** Wartość domyślna to `AzureWebJobsStorage`.
* (opcjonalnie) : Nazwa centrum zadań Funkcje trwałe do użycia. ** `task-hub-name` ** Wartość domyślna to `DurableFunctionsHub`. Można go również ustawić w [host.json](durable-functions-bindings.md#host-json), przy użyciu durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Poczekaj na ukończenie aranżacji

W długotrwałych aranżacji, można poczekać i uzyskać wyniki aranżacji. W takich przypadkach jest również przydatne, aby można było zdefiniować limit czasu na aranżacji. Jeśli limit czasu zostanie przekroczony, stan aranżacji powinny być zwracane zamiast wyników.

Metoda `WaitForCompletionOrCreateCheckStatusResponseAsync` (.NET) `waitForCompletionOrCreateCheckStatusResponse` lub (JavaScript) może służyć do synchronicznie uzyskać rzeczywiste dane wyjściowe z wystąpienia aranżacji. Domyślnie metody te używają domyślnej wartości 10 sekund `timeout`dla `retryInterval`programu , a 1 sekundy dla .  

Oto przykładowa funkcja wyzwalacza HTTP, która pokazuje, jak korzystać z tego interfejsu API:

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Zobacz [Uruchom wystąpienia](#javascript-function-json) dla konfiguracji function.json.

---

Wywołanie funkcji z następującym wierszem. Użyj 2 sekund dla limitu czasu i 0,5 sekundy dla interwału ponawiania:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

W zależności od czasu wymaganego do uzyskania odpowiedzi z wystąpienia aranżacji istnieją dwa przypadki:

* Wystąpienia aranżacji zostaną ukończone w ramach zdefiniowanego limitu czasu (w tym przypadku 2 sekundy), a odpowiedź jest rzeczywistym wyjściem wystąpienia aranżacji, dostarczanym synchronicznie:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:14:29 GMT
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* Wystąpienia aranżacji nie mogą być ukończone w ramach zdefiniowanego limitu czasu, a odpowiedź jest domyślną odpowiedzią opisaną w [odnajdowaniu adresów URL interfejsu API HTTP:](durable-functions-http-api.md)

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:13:51 GMT
        Location: http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Format adresów URL elementu webhook może się różnić, w zależności od wersji hosta usługi Azure Functions, który jest uruchomiony. Poprzednim przykładem jest host usługi Azure Functions 2.0.

## <a name="retrieve-http-management-webhook-urls"></a>Pobieranie adresów URL elementów webhook do zarządzania http

Za pomocą systemu zewnętrznego można monitorować lub podnosić zdarzenia do aranżacji. Systemy zewnętrzne mogą komunikować się z funkcjami trwałymi za pośrednictwem adresów URL elementu webhook, które są częścią domyślnej odpowiedzi opisanej w [odnajdowaniu adresów URL interfejsu API HTTP](durable-functions-http-features.md#http-api-url-discovery). Adresy URL elementu webhook można również uzyskać programowo przy użyciu [powiązania klienta aranżacji.](durable-functions-bindings.md#orchestration-client) Metody `CreateHttpManagementPayload` (.NET) `createHttpManagementPayload` lub (JavaScript) mogą być używane do uzyskania pliku serializable obiektu zawierającego te adresy URL elementu webhook.

Metody `CreateHttpManagementPayload` (.NET) `createHttpManagementPayload` i (JavaScript) mają jeden parametr:

* **instanceId**: unikatowy identyfikator wystąpienia.

Metody zwracają obiekt z następującymi właściwościami ciągu:

* **Identyfikator:** Identyfikator wystąpienia aranżacji (powinien być taki `InstanceId` sam jak dane wejściowe).
* **StatusQueryGetUri:** Adres URL stanu wystąpienia aranżacji.
* **SendEventPostUri**: Adres URL "raise event" wystąpienia aranżacji.
* **TerminatePostUri**: Adres URL "terminate" wystąpienia aranżacji.
* **PurgeHistoryDeleteUri:** Adres URL "historii przeczyszczenia" wystąpienia aranżacji.

Funkcje mogą wysyłać wystąpienia tych obiektów do systemów zewnętrznych w celu monitorowania lub podnoszenia zdarzeń w odpowiednich aranżacjach, jak pokazano w poniższych przykładach:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] IDurableActivityContext ctx,
    [DurableClient] IDurableOrchestrationClient client,
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

> [!NOTE]
> Poprzedni kod języka C# jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x `DurableActivityContext` należy użyć zamiast atrybutu `IDurableActivityContext`, należy użyć `OrchestrationClient` atrybutu zamiast atrybutu `DurableClient` i należy użyć typu `DurableOrchestrationClient` parametru `IDurableOrchestrationClient`zamiast . Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Zobacz [Uruchom wystąpienia](#javascript-function-json) dla konfiguracji function.json.

---

## <a name="rewind-instances-preview"></a>Wystąpienia przewijania do tyłu (wersja zapoznawcza)

Jeśli masz błąd aranżacji z nieoczekiwanego powodu, można *przewinąć* wystąpienie do stanu wcześniej dobrej kondycji przy użyciu interfejsu API utworzonego w tym celu.

> [!NOTE]
> Ten interfejs API nie jest przeznaczony do zastąpienia prawidłowej obsługi błędów i zasad ponawiania prób. Raczej jest przeznaczony do użycia tylko w przypadkach, gdy wystąpienia aranżacji nie powiedzie się z nieoczekiwanych powodów. Aby uzyskać więcej informacji na temat obsługi błędów i zasad ponawiania prób, zobacz artykuł [Obsługa błędów.](durable-functions-error-handling.md)

Użyj `RewindAsync` metody (.NET) lub `rewind` (JavaScript) powiązania klienta [aranżacji,](durable-functions-bindings.md#orchestration-client) aby przywrócić aranżację do stanu *uruchamiania.* Ta metoda będzie również ponownie uruchomić działania lub sub-orchestration niepowodzenia wykonywania, które spowodowały błąd aranżacji.

Załóżmy na przykład, że masz przepływ pracy obejmujący serię [zatwierdzeń ludzkich.](durable-functions-overview.md#human) Załóżmy, że istnieje szereg funkcji działania, które powiadamiają kogoś, że ich zatwierdzenie jest potrzebne, i poczekaj na odpowiedź w czasie rzeczywistym. Po otrzymaniu odpowiedzi lub upływu czasu wszystkich działań zatwierdzania, załóżmy, że inne działanie kończy się niepowodzeniem z powodu błędnej konfiguracji aplikacji, takiej jak nieprawidłowy ciąg połączenia bazy danych. Wynikiem jest błąd aranżacji głęboko w przepływie pracy. Za `RewindAsync` pomocą interfejsu API `rewind` (.NET) lub (JavaScript) administrator aplikacji może naprawić błąd konfiguracji i przewinąć nieudaną aranżację z powrotem do stanu bezpośrednio przed awarią. Żaden z kroków interakcji człowieka nie muszą być ponownie zatwierdzone, a aranżacja może teraz zakończyć się pomyślnie.

> [!NOTE]
> Funkcja *przewijania* do tyłu nie obsługuje przewijania wystąpień aranżacji, które używają trwałych czasomierzy.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("rewind-queue")] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> Poprzedni kod języka C# jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy `OrchestrationClient` użyć `DurableClient` atrybutu zamiast atrybutu `DurableOrchestrationClient` i zamiast `IDurableOrchestrationClient`parametru . Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

Zobacz [Uruchom wystąpienia](#javascript-function-json) dla konfiguracji function.json.

---

### <a name="azure-functions-core-tools"></a>Podstawowe narzędzia funkcji platformy Azure

Można również przewinąć wystąpienie aranżacji bezpośrednio za pomocą polecenia [Podstawowe narzędzia](../functions-run-local.md) `durable rewind` azure functions. Przyjmuje następujące parametry:

* (wymagane) : Identyfikator wystąpienia aranżacji. ** `id` **
* (opcjonalnie) : Przyczyna przewijania instancji aranżacji. ** `reason` **
* (opcjonalnie) : Nazwa ustawienia aplikacji zawierającego ciąg połączenia magazynu do użycia. ** `connection-string-setting` ** Wartość domyślna to `AzureWebJobsStorage`.
* (opcjonalnie) : Nazwa centrum zadań Funkcje trwałe do użycia. ** `task-hub-name` ** Domyślnie używana jest nazwa centrum zadań w pliku [host.json.](durable-functions-bindings.md#host-json)

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Przeczyść historię wystąpień

Aby usunąć wszystkie dane skojarzone z aranżacji, można przeczyścić historię wystąpień. Na przykład można usunąć wszystkie wiersze tabeli platformy Azure i duże obiekty blob wiadomości skojarzone z ukończonym wystąpieniem. Aby to zrobić, `PurgeInstanceHistoryAsync` użyj metody `purgeInstanceHistory` (.NET) lub (JavaScript) [powiązania klienta aranżacji](durable-functions-bindings.md#orchestration-client).

Ta metoda ma dwa przeciążenia. Pierwsze przeciążenie przeczyszcza historię przez identyfikator wystąpienia aranżacji:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("purge-queue")] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

Zobacz [Uruchom wystąpienia](#javascript-function-json) dla konfiguracji function.json.

---

W następnym przykładzie pokazano funkcję wyzwalaną czasomierzem, która przeczyści historię dla wszystkich wystąpień aranżacji, które zostały ukończone po określonym przedziale czasu. W takim przypadku usuwa dane dla wszystkich wystąpień ukończonych 30 lub więcej dni temu. Zaplanowano go raz dziennie, o godzinie 12:00:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
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
> Poprzedni kod języka C# jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy `OrchestrationClient` użyć `DurableClient` atrybutu zamiast atrybutu `DurableOrchestrationClient` i zamiast `IDurableOrchestrationClient`parametru . Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Metoda `purgeInstanceHistoryBy` może służyć do warunkowego przeczyszczanie historii wystąpień dla wielu wystąpień.

**funkcja.json**

```json
{
  "bindings": [
    {
      "schedule": "0 0 12 * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> W tym przykładzie jest przeznaczony dla funkcji trwałych w wersji 2.x. W wersji 1.x `orchestrationClient` użyj `durableClient`zamiast .

**indeks.js**

```javascript
const df = require("durable-functions");

module.exports = async function (context, myTimer) {
    const client = df.getClient(context);
    const createdTimeFrom = new Date(0);
    const createdTimeTo = new Date().setDate(today.getDate() - 30);
    const runtimeStatuses = [ df.OrchestrationRuntimeStatus.Completed ];
    return client.purgeInstanceHistoryBy(createdTimeFrom, createdTimeTo, runtimeStatuses);
};
```

---

> [!NOTE]
> Aby operacja historii przeczyszczenie powiodła się, stan środowiska uruchomieniowego wystąpienia docelowego musi zostać **zakończony,** **zakończony**lub **zakończony.**

### <a name="azure-functions-core-tools"></a>Podstawowe narzędzia funkcji platformy Azure

Historia wystąpienia aranżacji można przeczyścić za pomocą polecenia [Podstawowe narzędzia](../functions-run-local.md) `durable purge-history` azure functions. Podobnie jak w drugim przykładzie Języka C# w poprzedniej sekcji, przeczyści historię dla wszystkich wystąpień aranżacji utworzonych w określonym przedziale czasu. Można dodatkowo filtrować wyczyszczone wystąpienia według stanu środowiska uruchomieniowego. Polecenie ma kilka parametrów:

* (opcjonalnie) : Przeczyść historię wystąpień utworzonych po tej dacie/godzinie (UTC). ** `created-after` ** Iso 8601 sformatowany datetimes akceptowane.
* (opcjonalnie) : Przeczyść historię wystąpień utworzonych przed tą datą/godziną (UTC). ** `created-before` ** Iso 8601 sformatowany datetimes akceptowane.
* (opcjonalnie) : Przeczyść historię wystąpień o określonym stanie (na przykład uruchomione lub ukończone). ** `runtime-status` ** Może zapewnić wiele stanów (oddzielone spacjami).
* (opcjonalnie) : Nazwa ustawienia aplikacji zawierającego ciąg połączenia magazynu do użycia. ** `connection-string-setting` ** Wartość domyślna to `AzureWebJobsStorage`.
* (opcjonalnie) : Nazwa centrum zadań Funkcje trwałe do użycia. ** `task-hub-name` ** Domyślnie używana jest nazwa centrum zadań w pliku [host.json.](durable-functions-bindings.md#host-json)

Następujące polecenie usuwa historię wszystkich nieudanych wystąpień utworzonych przed 14 listopada 2018 r. o godzinie 19:35 (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Usuwanie centrum zadań

Za pomocą polecenia [Podstawowe narzędzia](../functions-run-local.md) `durable delete-task-hub` azure functions można usunąć wszystkie artefakty magazynu skojarzone z centrum zadań określonego zadania, w tym tabele magazynu platformy Azure, kolejki i obiekty blob. Polecenie ma dwa parametry:

* (opcjonalnie) : Nazwa ustawienia aplikacji zawierającego ciąg połączenia magazynu do użycia. ** `connection-string-setting` ** Wartość domyślna to `AzureWebJobsStorage`.
* (opcjonalnie) : Nazwa centrum zadań Funkcje trwałe do użycia. ** `task-hub-name` ** Domyślnie używana jest nazwa centrum zadań w pliku [host.json.](durable-functions-bindings.md#host-json)

Następujące polecenie usuwa wszystkie dane magazynu platformy `UserTest` Azure skojarzone z centrum zadań.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak radzić sobie z przechowywaniem wersji](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Wbudowane odwołanie do interfejsu API HTTP do zarządzania wystąpieniami](durable-functions-http-api.md)
