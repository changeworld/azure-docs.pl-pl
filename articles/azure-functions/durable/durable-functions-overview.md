---
title: Omówienie rozszerzenia Durable Functions — Azure
description: Wprowadzenie do rozszerzenia Durable Functions dla usługi Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 08/07/2019
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: 5d454aefaba89bef9dc9009ff442fa5543dae2ef
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241346"
---
# <a name="what-are-durable-functions"></a>Co to jest Durable Functions?

*Funkcje trwałe* to rozszerzenie [usługi Azure Functions,](../functions-overview.md) które umożliwia pisanie funkcji stanowych w środowisku obliczeniowym bezserwerowym. Rozszerzenie umożliwia definiowanie przepływów pracy stanowych przez pisanie [*funkcji koordynatora*](durable-functions-orchestrations.md) i jednostek stanowych przez pisanie [*funkcji jednostki*](durable-functions-entities.md) przy użyciu modelu programowania usługi Azure Functions. W tle rozszerzenie zarządza stan, punkty kontrolne i restartuje dla Ciebie, co pozwala skupić się na logice biznesowej.

## <a name="supported-languages"></a><a name="language-support"></a>Obsługiwane języki

Rozszerzenie Durable Functions obsługuje obecnie następujące języki:

* **C#**: zarówno [prekompilowane biblioteki klas](../functions-dotnet-class-library.md), jak i [skrypt języka C#](../functions-reference-csharp.md).
* **JavaScript**: obsługiwany tylko w przypadku wersji 2.x środowiska uruchomieniowego usługi Azure Functions. Wymaga rozszerzenia Durable Functions w wersji 1.7.0 lub nowszej. 
* **F#**: prekompilowane biblioteki klas i skrypt języka F#. Skrypt języka F# jest obsługiwany tylko w przypadku wersji 1.x środowiska uruchomieniowego usługi Azure Functions.

Docelowo rozszerzenie Durable Functions ma obsługiwać wszystkie [języki obsługiwane w usłudze Azure Functions](../supported-languages.md). Zobacz [listę problemów z rozszerzeniem Durable Functions](https://github.com/Azure/azure-functions-durable-extension/issues), aby poznać aktualny stan prac nad obsługą dodatkowych języków.

Podobnie jak usługi Azure Functions, istnieją szablony ułatwiające tworzenie trwałych funkcji przy użyciu [programu Visual Studio 2019,](durable-functions-create-first-csharp.md) [kodu programu Visual Studio](quickstart-js-vscode.md)i [portalu Azure.](durable-functions-create-portal.md)

## <a name="application-patterns"></a>Wzorce aplikacji

Podstawowym zastosowaniem rozszerzenia Durable Functions jest uproszczenie złożonych wymagań związanych z koordynacją stanową w aplikacjach bezserwerowych. W poniższych sekcjach opisano typowe wzorce aplikacji, które mogą korzystać z funkcji trwałych:

* [Łączenie funkcji w łańcuchy](#chaining)
* [Model fan-out/fan-in](#fan-in-out)
* [Interfejsy API protokołu HTTP Async](#async-http)
* [Monitorowania](#monitoring)
* [Interakcja z użytkownikami](#human)
* [Agregator (jednostki stanowe)](#aggregator)

### <a name="pattern-1-function-chaining"></a><a name="chaining"></a>Szyk #1: Łączenie funkcji

W szyku łańcucha funkcji sekwencja funkcji wykonuje w określonej kolejności. W tym wzorcu dane wyjściowe jednej funkcji są stosowane do danych wejściowych innej funkcji.

![Diagram wzorca łańcucha funkcji](./media/durable-functions-concepts/function-chaining.png)

Funkcji trwałych można użyć do zaimplementowania wzorca łańcucha funkcji zwięźle, jak pokazano w poniższym przykładzie.

W tym przykładzie `F1` `F2`wartości `F3`, `F4` , i są nazwy innych funkcji w tej samej aplikacji funkcji. Przepływ sterowania można zaimplementować przy użyciu normalnych imperatywów kodowania konstrukcji. Code executes from the top down. Kod może obejmować istniejącą semantykę przepływu sterowania językiem, takich jak warunkowe i pętle. Logikę obsługi błędów `try` / `catch` / `finally` można uwzględnić w blokach.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1", null);
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

Za pomocą `context` parametru można wywołać inne funkcje według nazwy, parametrów przekazywania i zwracania danych wyjściowych funkcji. Za każdym razem, gdy wywołano `await`kod, struktura funkcji trwałych punktu kontrolnego postępu bieżącego wystąpienia funkcji. Jeśli proces lub maszyna wirtualna odtwarza w połowie wykonywania, wystąpienie `await` funkcji wznawia z poprzedniego wywołania. Aby uzyskać więcej informacji, zobacz następną sekcję Wzór #2: Wyjmij wentylatorem/wentylatorem.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    try {
        const x = yield context.df.callActivity("F1");
        const y = yield context.df.callActivity("F2", x);
        const z = yield context.df.callActivity("F3", y);
        return    yield context.df.callActivity("F4", z);
    } catch (error) {
        // Error handling or compensation goes here.
    }
});
```

Za pomocą `context.df` obiektu można wywołać inne funkcje według nazwy, parametrów przekazywania i zwracania danych wyjściowych funkcji. Za każdym razem, gdy wywołano `yield`kod, struktura funkcji trwałych punktu kontrolnego postępu bieżącego wystąpienia funkcji. Jeśli proces lub maszyna wirtualna odtwarza w połowie wykonywania, wystąpienie `yield` funkcji wznawia z poprzedniego wywołania. Aby uzyskać więcej informacji, zobacz następną sekcję Wzór #2: Wyjmij wentylatorem/wentylatorem.

> [!NOTE]
> Obiekt `context` w języku JavaScript reprezentuje cały [kontekst funkcji](../functions-reference-node.md#context-object). Dostęp do kontekstu `df` funkcje trwałe przy użyciu właściwości w kontekście głównym.

---

### <a name="pattern-2-fan-outfan-in"></a><a name="fan-in-out"></a>wzór #2: Wentylator out / wentylator w

W wentylatorze/wentylatorze we wzorze wykonujesz wiele funkcji równolegle, a następnie poczekaj na zakończenie wszystkich funkcji. Często niektóre prace agregacji odbywa się na wyniki, które są zwracane z funkcji.

![Schemat wyjęcia wentylatora/wzoru wentylatora](./media/durable-functions-concepts/fan-out-fan-in.png)

Dzięki normalnym funkcjom można wyeprowić, wysyłając wiele wiadomości do kolejki. Fanning z powrotem jest o wiele trudniejsze. Aby fan w, w normalnej funkcji, należy napisać kod do śledzenia, kiedy kończy się kolejka wyzwalane funkcje, a następnie przechowywać wyjścia funkcji.

Rozszerzenie Funkcje trwałe obsługuje ten wzorzec ze stosunkowo prostym kodem:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1", null);
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

Praca wentylatora jest dystrybuowana do wielu `F2` wystąpień funkcji. Praca jest śledzona przy użyciu dynamicznej listy zadań. `Task.WhenAll`jest wywoływana czekać na zakończenie wszystkich wywoływanych funkcji. Następnie dane `F2` wyjściowe funkcji są agregowane z dynamicznej listy zadań i przekazywane do `F3` funkcji.

Automatyczne punkty kontrolne, `await` które `Task.WhenAll` dzieje się w wywołaniu na zapewnia, że potencjalna awaria w połowie drogi lub ponowne uruchomienie nie wymaga ponownego uruchomienia już wykonane zadanie.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

Praca wentylatora jest dystrybuowana do wielu `F2` wystąpień funkcji. Praca jest śledzona przy użyciu dynamicznej listy zadań. `context.df.Task.all`Interfejs API jest wywoływana, aby czekać na zakończenie wszystkich wywoływanych funkcji. Następnie dane `F2` wyjściowe funkcji są agregowane z dynamicznej listy zadań i przekazywane do `F3` funkcji.

Automatyczne punkty kontrolne, `yield` które `context.df.Task.all` dzieje się w wywołaniu na zapewnia, że potencjalna awaria w połowie drogi lub ponowne uruchomienie nie wymaga ponownego uruchomienia już wykonane zadanie.

---

> [!NOTE]
> W rzadkich przypadkach jest możliwe, że awaria może się zdarzyć w oknie po zakończeniu funkcji działania, ale przed jej zakończeniem jest zapisywany w historii aranżacji. W takim przypadku funkcja działania zostanie ponownie uruchomiony od początku po odzyskaniu procesu.

### <a name="pattern-3-async-http-apis"></a><a name="async-http"></a>Wzorzec #3: Asynchowe interfejsy API HTTP

Asynchowy wzorzec interfejsu HTTP rozwiązuje problem koordynowania stanu długotrwałych operacji z klientami zewnętrznymi. Typowym sposobem zaimplementowania tego wzorca jest posiadanie punktu końcowego HTTP wyzwalania długotrwałej akcji. Następnie przekieruj klienta do punktu końcowego stanu, który klient sonduje, aby dowiedzieć się, kiedy operacja zostanie zakończona.

![Diagram wzorca interfejsu API HTTP](./media/durable-functions-concepts/async-http-api.png)

Funkcje trwałe zapewnia **wbudowaną obsługę** tego wzorca, upraszczając lub nawet usuwając kod, który należy zapisać do interakcji z długotrwałych wykonań funkcji. Na przykład próbki szybkiego startu funkcji trwałych[(C#](durable-functions-create-first-csharp.md) i [JavaScript)](quickstart-js-vscode.md)pokazują proste polecenie REST, którego można użyć do uruchomienia nowych wystąpień funkcji programu orchestrator. Po uruchomieniu wystąpienia rozszerzenie udostępnia interfejsy API HTTP elementu webhook, które kwerendy stanu funkcji programu orchestrator. 

W poniższym przykładzie przedstawiono polecenia REST, które uruchamiają koordynatora i pytają o jego stan. Dla jasności niektóre szczegóły protokołu są pomijane w przykładzie.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

Ponieważ środowisko wykonawcze funkcje trwałe zarządza stanem dla Ciebie, nie trzeba implementować własnego mechanizmu śledzenia stanu.

Rozszerzenie Funkcje trwałe udostępnia wbudowane interfejsy API HTTP, które zarządzają długotrwałe aranżacji. Alternatywnie można samodzielnie zaimplementować ten wzorzec przy użyciu własnych wyzwalaczy funkcji (takich jak HTTP, kolejka lub usługi Azure Event Hubs) i [powiązania klienta aranżacji.](durable-functions-bindings.md#orchestration-client) Na przykład można użyć wiadomości kolejki, aby wyzwolić zakończenie. Możesz też użyć wyzwalacza HTTP, który jest chroniony przez zasady uwierzytelniania usługi Azure Active Directory zamiast wbudowanych interfejsów API HTTP, które używają wygenerowanego klucza do uwierzytelniania.

Aby uzyskać więcej informacji, zobacz artykuł [funkcje HTTP,](durable-functions-http-features.md) który wyjaśnia, jak można udostępnić asynchroniczne, długotrwałe procesy za pośrednictwem protokołu HTTP przy użyciu rozszerzenia Funkcje trwałe.

### <a name="pattern-4-monitor"></a><a name="monitoring"></a>Wzór #4: Monitor

Wzorzec monitora odnosi się do elastycznego, cyklicznego procesu w przepływie pracy. Przykładem jest sondowanie, dopóki nie zostaną spełnione określone warunki. Można użyć [wyzwalacza czasomierza](../functions-bindings-timer.md) regularnych, aby rozwiązać podstawowy scenariusz, taki jak okresowe zadanie oczyszczania, ale jego interwał jest statyczny i zarządzanie okresami istnienia wystąpienia staje się złożone. Funkcji trwałych można używać do tworzenia elastycznych interwałów cykli, zarządzania okresami istnienia zadań i tworzenia wielu procesów monitorowania z jednej aranżacji.

Przykładem wzorca monitora jest odwrócenie wcześniejszego asynchronizowego scenariusza interfejsu HTTP interfejsu API. Zamiast uwidaczniania punktu końcowego dla klienta zewnętrznego do monitorowania długotrwałej operacji, długotrwały monitor zużywa zewnętrzny punkt końcowy, a następnie czeka na zmianę stanu.

![Diagram wzorca monitora](./media/durable-functions-concepts/monitor.png)

W kilku wierszach kodu można użyć funkcji trwałych do tworzenia wielu monitorów, które obserwują dowolne punkty końcowe. Monitory można zakończyć wykonywanie, gdy warunek jest spełniony lub innej funkcji można użyć klienta aranżacji trwałe, aby zakończyć monitory. Interwał monitora `wait` można zmienić na podstawie określonego warunku (na przykład wykładniczego wycofywania). 

Poniższy kod implementuje podstawowy monitor:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInterval = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

---

Po odebraniu żądania zostanie utworzone nowe wystąpienie aranżacji dla tego identyfikatora zadania. Wystąpienie sonduje stan, dopóki warunek nie zostanie spełniony, a pętla zostanie przerwana. Trwały czasomierz steruje interwałem sondowania. Następnie można wykonać więcej pracy lub aranżacji można zakończyć. Po `nextCheck` `expiryTime`przekroczeniu monitora kończy się.

### <a name="pattern-5-human-interaction"></a><a name="human"></a>Wzór #5: Interakcja międzyludem

Wiele zautomatyzowanych procesów wiąże się z jakąś interakcją międzyludyjną. Angażowanie ludzi w zautomatyzowany proces jest trudne, ponieważ ludzie nie są tak wysoko dostępni i responsywni jak usługi w chmurze. Zautomatyzowany proces może zezwalać na tę interakcję przy użyciu limitów czasu i logiki kompensacji.

Proces zatwierdzania jest przykładem procesu biznesowego, który obejmuje interakcję z człowiekiem. Zatwierdzenie przez menedżera może być wymagane dla raportu z wydatków, który przekracza określoną kwotę w dolarach. Jeśli menedżer nie zatwierdzi raportu z wydatków w ciągu 72 godzin (być może menedżer udał się na wakacje), rozpoczyna się proces eskalacji, aby uzyskać zgodę od kogoś innego (być może menedżera menedżera).

![Diagram wzorca interakcji międzyludzkiej](./media/durable-functions-concepts/approval.png)

Wzorzec można zaimplementować w tym przykładzie przy użyciu funkcji aranżatora. Koordynator używa [trwałego timera](durable-functions-timers.md) do żądania zatwierdzenia. Program orchestrator eskaluje, jeśli wystąpi limit czasu. Koordynator czeka na [zdarzenie zewnętrzne,](durable-functions-external-events.md)takie jak powiadomienie generowane przez interakcję z człowiekiem.

Te przykłady tworzą proces zatwierdzania, aby zademonstrować wzorzec interakcji międzyludzkiej:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval", null);
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate", null);
        }
    }
}
```

Aby utworzyć trwały czasomierz, zadzwoń . `context.CreateTimer` Powiadomienie jest odbierane przez `context.WaitForExternalEvent`. Następnie `Task.WhenAny` jest wywoływana, aby zdecydować, czy do eskalacji (limit czasu dzieje się najpierw) lub przetwarzania zatwierdzenia (zatwierdzenie jest odbierane przed przesuwem czasu).

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

Aby utworzyć trwały czasomierz, zadzwoń . `context.df.createTimer` Powiadomienie jest odbierane przez `context.df.waitForExternalEvent`. Następnie `context.df.Task.any` jest wywoływana, aby zdecydować, czy do eskalacji (limit czasu dzieje się najpierw) lub przetwarzania zatwierdzenia (zatwierdzenie jest odbierane przed przesuwem czasu).

---

Klient zewnętrzny może dostarczyć powiadomienie o zdarzeniu do funkcji orkiestratora oczekiwania przy użyciu [wbudowanych interfejsów API HTTP:](durable-functions-http-api.md#raise-event)

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

Zdarzenie można również wywoływać przy użyciu klienta trwałej aranżacji z innej funkcji w tej samej aplikacji funkcji:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

---

### <a name="pattern-6-aggregator-stateful-entities"></a><a name="aggregator"></a>Wzorzec #6: Agregator (jednostki stanowe)

Szósty wzorzec dotyczy agregowania danych zdarzeń w danym okresie czasu w jedną, adresowalną *encję*. W tym wzorcu zagregowane dane mogą pochodzić z wielu źródeł, mogą być dostarczane w partiach lub mogą być rozproszone przez długi okres czasu. Agregator może być konieczne podjęcie działań na dane zdarzeń, jak nadejdzie i klientów zewnętrznych może być konieczne zapytanie zagregowanych danych.

![Diagram agregatora](./media/durable-functions-concepts/aggregator.png)

Trudne rzeczy o próbuje zaimplementować ten wzorzec z normalnych, bezstanowych funkcji jest to, że kontrola współbieżności staje się ogromnym wyzwaniem. Nie tylko trzeba martwić się o wiele wątków modyfikujących te same dane w tym samym czasie, należy również martwić się o zapewnienie, że agregator działa tylko na jednej maszynie wirtualnej w czasie.

Trwałe [jednostki](durable-functions-entities.md) można użyć, aby łatwo zaimplementować ten wzorzec jako pojedynczą funkcję.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            ctx.SetState(currentValue + amount);
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }
}
```

Trwałe jednostki mogą być również modelowane jako klasy w .NET. Ten model może być przydatne, jeśli lista operacji jest stała i staje się duża. Poniższy przykład jest równoważne `Counter` implementacji jednostki przy użyciu .NET klas i metod.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

---

Klienci mogą wyliczyć w kolejce *operacje* dla (znanej również jako "sygnalizacja") funkcji jednostki przy użyciu [powiązania klienta jednostki](durable-functions-bindings.md#entity-client).

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [DurableClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

> [!NOTE]
> Dynamicznie generowane serwery proxy są również dostępne w programie .NET do sygnalizacji jednostek w sposób bezpieczny dla typu. Oprócz sygnalizacji klienci mogą również wyszukiwać stan funkcji jednostki przy użyciu [metod bezpiecznych](durable-functions-bindings.md#entity-client-usage) dla typu w powiązaniu klienta aranżacji.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

---

Funkcje jednostki są dostępne w [funkcji trwałe 2.0](durable-functions-versions.md) i powyżej.

## <a name="the-technology"></a>Technologia

W tle rozszerzenie funkcje trwałe jest zbudowany na podstawie [trwałej struktury zadań](https://github.com/Azure/durabletask), biblioteki open source w usłudze GitHub, która jest używana do tworzenia przepływów pracy w kodzie. Podobnie jak usługi Azure Functions jest bezserwerowej ewolucji azure webjobs, funkcje trwałe jest bezserwerowej ewolucji trwałej struktury zadań. Firma Microsoft i inne organizacje intensywnie korzystają z struktury trwałe zadania do automatyzacji procesów o znaczeniu krytycznym. Jest to naturalne dopasowanie do środowiska usługi Azure bezserwerowe.

## <a name="code-constraints"></a>Ograniczenia kodu

W celu zapewnienia niezawodnych i długotrwałych gwarancji wykonania, funkcje programu orchestrator mają zestaw reguł kodowania, które muszą być przestrzegane. Aby uzyskać więcej informacji, zobacz [ograniczenie kodu funkcji koordynatora.](durable-functions-code-constraints.md)

## <a name="billing"></a>Rozliczenia

Opłaty za rozszerzenie Durable Functions są naliczane tak samo, jak w przypadku usługi Azure Functions. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Podczas wykonywania funkcji programu orchestrator w [planie zużycie](../functions-scale.md#consumption-plan)usług Azure Functions, istnieją pewne zachowania rozliczeń, które należy pamiętać. Aby uzyskać więcej informacji na temat tych zachowań, zobacz artykuł [rozliczeń funkcji trwałych.](durable-functions-billing.md)

## <a name="jump-right-in"></a>Błyskawicznie rozpocznij pracę

Ukończ jeden z tych samouczków Szybki start dotyczących poszczególnych języków, aby rozpocząć korzystanie z rozszerzenia Durable Functions w niecałe 10 minut:

* [C# przy użyciu programu Visual Studio 2019](durable-functions-create-first-csharp.md)
* [JavaScript w programie Visual Studio Code](quickstart-js-vscode.md)

W obu przewodnikach Szybki start utworzysz lokalnie i przetestujesz funkcję trwałą „hello world”. Kod funkcji zostanie następnie opublikowany na platformie Azure. Utworzona przez Ciebie funkcja aranżuje i łączy w łańcuchy wywołania do innych funkcji.

## <a name="learn-more"></a>Dowiedz się więcej

Poniższy klip wideo prezentuje zalety rozszerzenia Durable Functions:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Aby uzyskać bardziej szczegółowe omówienie funkcji trwałych i podstawowej technologii, zobacz następujący film wideo (koncentruje się na .NET, ale pojęcia dotyczą również innych obsługiwanych języków):

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Ponieważ Durable Functions to zaawansowane rozszerzenie usługi [Azure Functions](../functions-overview.md), nie jest odpowiednie dla wszystkich aplikacji. Aby zapoznać się z porównaniem technologii orkiestracji dostępnych na platformie Azure, zobacz [Porównanie usług Azure Functions i Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Funkcje i funkcje funkcji funkcji trwałych](durable-functions-types-features-overview.md)
