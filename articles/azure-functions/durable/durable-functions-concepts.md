---
title: Wzorce Durable Functions i koncepcje techniczne w Azure Functions
description: Dowiedz się, w jaki sposób rozszerzenie Durable Functions w programie Azure Functions daje zalety wykonywania kodu stanowego w chmurze.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: fe3000181ed02e3640e7af48fa492f4a7db55191
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734583"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Wzorce Durable Functions i koncepcje techniczne (Azure Functions)

Durable Functions jest rozszerzeniem [Azure Functions](../functions-overview.md) i [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md). Za pomocą Durable Functions można pisać funkcje stanowe w środowisku bezserwerowym. Rozszerzenie zarządza stanem, punktami kontrolnymi i ponownym uruchamianiem. 

Ten artykuł zawiera szczegółowe informacje na temat zachowań rozszerzenia Durable Functions dla Azure Functions i wspólnych wzorców implementacji. Informacje te mogą pomóc w ustaleniu, jak używać Durable Functions, aby pomóc w rozwiązaniu problemów z programowaniem.

> [!NOTE]
> Durable Functions jest zaawansowanym rozszerzeniem Azure Functions, które nie jest odpowiednie dla wszystkich aplikacji. W tym artykule przyjęto założenie, że masz silny stopień znajomości [Azure Functions](../functions-overview.md) i wyzwań związanych z tworzeniem aplikacji bezserwerowych.

## <a name="patterns"></a>Wzorce

W tej sekcji opisano niektóre typowe wzorce aplikacji, w których Durable Functions mogą być przydatne.

### <a name="chaining"></a>#1 wzorca: Łączenie funkcji w łańcuchy

W wzorcu łańcucha funkcji sekwencja funkcji jest wykonywana w określonej kolejności. W tym wzorcu dane wyjściowe jednej funkcji są stosowane do danych wejściowych innej funkcji.

![Diagram wzorca łańcucha funkcji](./media/durable-functions-concepts/function-chaining.png)

Można użyć Durable Functions do wdrożenia wzorca łańcucha funkcji zwięzłie, jak pokazano w następującym przykładzie:

#### <a name="precompiled-c"></a>PrekompilowanegoC#

```csharp
public static async Task<object> Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
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

#### <a name="c-script"></a>Skrypt języka C#

```csharp
public static async Task<object> Run(DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
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

> [!NOTE]
> Istnieją delikatne różnice między pisaniem wstępnie skompilowanej funkcji trwałej w programie C# i pisaniem wstępnie skompilowanej funkcji trwałej w C# skrypcie. C# We wstępnie skompilowanej funkcji trwałe parametry muszą być dekoracyjne z odpowiednimi atrybutami. Przykładem jest `[OrchestrationTrigger]` atrybut `DurableOrchestrationContext` dla parametru. C# W przypadku wstępnie skompilowanej funkcji trwałej, jeśli parametry nie są prawidłowo dekoracyjne, środowisko uruchomieniowe nie może wstrzyknąć zmiennych do funkcji i wystąpi błąd. Aby uzyskać więcej przykładów, zobacz artykuł [Azure-Functions-trwałe-Extension — przykłady w witrynie GitHub](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples).

#### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

W tym przykładzie `F1`wartości, `F2`, `F3`i `F4` są nazwami innych funkcji w aplikacji funkcji. Przepływ sterowania można zaimplementować przy użyciu zwykłych konstrukcji kodowania. Kod jest wykonywany z góry. Kod może dotyczyć istniejącej semantyki przepływu sterowania języka, takich jak warunkowe i pętle. Logikę obsługi błędów można uwzględnić w `try` / / `catch` blokach`finally` .

Możesz użyć `context` parametru [DurableOrchestrationContext] \(.NET\) i obiektu(JavaScript),abywywołaćinnefunkcjewedługnazwy,przekazywaniaparametrówizwracanychdanychwyjściowychfunkcji.`context.df` Za każdym razem, gdy `await` kodC#wywołuje ( `yield` ) lub (JavaScript), program Durable Functions Framework punkty kontrolne postępu bieżącego wystąpienia funkcji. Jeśli proces lub maszyna wirtualna odtwarza w połowie wykonywania, wystąpienie funkcji zostaje wznowione od poprzedniego `await` lub `yield` wywołania. Aby uzyskać więcej informacji, zobacz następną sekcję #2 wzorca: Wentylator na zewnątrz.

> [!NOTE]
> Obiekt w języku JavaScript reprezentuje cały [kontekst funkcji](../functions-reference-node.md#context-object), nie tylko parametr [DurableOrchestrationContext] `context`

### <a name="fan-in-out"></a>#2 wzorca: Wentylator na zewnątrz

W wzorcu wentylator/wentylator w wzorcem wykonujesz równolegle wiele funkcji, a następnie poczekaj na zakończenie wszystkich funkcji. Często pewne zadania agregacji są wykonywane na wynikach, które są zwracane przez funkcje.

![Diagram z wzorcem wentylator/wentylator](./media/durable-functions-concepts/fan-out-fan-in.png)

Dzięki normalnym funkcjom można wyrównać, że funkcja wysyła wiele komunikatów do kolejki. Fanning z powrotem w programie jest znacznie bardziej trudne. Aby obwentylatorować, w normalnej funkcji napiszesz kod do śledzenia, gdy zakończy się funkcje wyzwalane przez kolejkę, a następnie przechowują dane wyjściowe funkcji. 

Rozszerzenie Durable Functions obsługuje ten wzorzec z stosunkowo prostym kodem:

#### <a name="precompiled-c"></a>PrekompilowanegoC#

```csharp
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
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

#### <a name="c-script"></a>Skrypt języka C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

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

Wentylator-out Work jest dystrybuowany do wielu wystąpień `F2` funkcji. Zadanie jest śledzone przy użyciu dynamicznej listy zadań. Interfejs API `Task.WhenAll` platformy .NET lub `context.df.Task.all` interfejs API języka JavaScript jest wywoływany, aby poczekać na zakończenie wszystkich wywoływanych funkcji. Następnie dane `F2` wyjściowe funkcji są agregowane z listy zadań dynamicznych i przenoszone `F3` do funkcji.

Automatyczne tworzenie punktów kontrolnych, które odbywa `await` się `yield` w trakcie `Task.WhenAll` wywołania `context.df.Task.all` lub zapewnia, że potencjalne awarie w Midway lub ponowny rozruch nie wymagają ponownego uruchomienia już wykonanego zadania.

### <a name="async-http"></a>#3 wzorca: Asynchroniczne interfejsy API HTTP

Wzorzec asynchronicznych interfejsów API protokołu HTTP rozwiązuje problem związany z uzgadnianiem stanu długotrwałych operacji z klientami zewnętrznymi. Typowym sposobem implementacji tego wzorca jest wywołanie HTTP wyzwalające długotrwałą akcję. Następnie Przekieruj klienta do punktu końcowego stanu, który będzie sondował klient, aby dowiedzieć się, kiedy operacja zostanie zakończona.

![Diagram wzorca interfejsu API protokołu HTTP](./media/durable-functions-concepts/async-http-api.png)

Durable Functions udostępnia wbudowane interfejsy API, które upraszczają zapisany kod w celu współdziałania z długotrwałymi wykonaniami funkcji. Przykłady szybkiego startu Durable Functions[C#](durable-functions-create-first-csharp.md) (i [JavaScript](quickstart-js-vscode.md)) pokazują proste polecenie REST, za pomocą którego można uruchamiać nowe wystąpienia funkcji programu Orchestrator. Po rozpoczęciu wystąpienia rozszerzenie uwidacznia interfejsy API HTTP elementu webhook, które wykonują zapytania o stan funkcji programu Orchestrator. 

Poniższy przykład przedstawia polecenia REST, które uruchamiają koordynatora i badają jego stan. Dla jasności niektóre szczegóły są pomijane w przykładzie.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

Ponieważ środowisko uruchomieniowe Durable Functions zarządza stanem, nie trzeba implementować własnego mechanizmu śledzenia stanu.

Rozszerzenie Durable Functions ma wbudowane elementy webhook, które zarządzają długotrwałymi aranżacjami. Możesz zaimplementować ten wzorzec samodzielnie przy użyciu własnych wyzwalaczy funkcji (takich jak http, Queue lub Azure Event Hubs) i `orchestrationClient` powiązania. Na przykład możesz użyć komunikatu kolejki, aby wyzwolić zakończenie. Lub można użyć wyzwalacza HTTP, który jest chroniony przez zasady uwierzytelniania Azure Active Directory zamiast wbudowanych elementów webhook, które używają wygenerowanego klucza do uwierzytelniania.

Poniżej przedstawiono kilka przykładów użycia wzorca interfejsu API protokołu HTTP:

#### <a name="precompiled-c"></a>PrekompilowanegoC#

```csharp
// An HTTP-triggered function starts a new orchestrator function instance.
[FunctionName("StartNewOrchestration")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // The function name comes from the request URL.
    // The function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="c-script"></a>Skrypt języka C#

```csharp
// An HTTP-triggered function starts a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // The function name comes from the request URL.
    // The function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

```javascript
// An HTTP-triggered function starts a new orchestrator function instance.
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);

    // The function name comes from the request URL.
    // The function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    return client.createCheckStatusResponse(req, instanceId);
};
```

W programie .NET parametr [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` jest wartością z `orchestrationClient` powiązania danych wyjściowych, która jest częścią rozszerzenia Durable Functions. W języku JavaScript ten obiekt jest zwracany przez wywołanie `df.getClient(context)`. Te obiekty zapewniają metody, których można użyć do uruchamiania, wysyłania zdarzeń do, kończenia i wykonywania zapytań o nowe lub istniejące wystąpienia funkcji programu Orchestrator.

W powyższych przykładach funkcja wyzwalana przez protokół http przyjmuje `functionName` wartość z przychodzącego adresu URL i przekazuje wartość do [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). Interfejs API powiązań [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) następnie zwraca odpowiedź, która zawiera `Location` nagłówek i dodatkowe informacje o wystąpieniu. Możesz użyć informacji później, aby wyszukać stan uruchomionego wystąpienia lub przerwać wystąpienie.

### <a name="monitoring"></a>#4 wzorca: Monitorowanie

Wzorzec monitora odnosi się do elastycznego, cyklicznego procesu w przepływie pracy. Przykład jest sondowany, dopóki nie zostaną spełnione określone warunki. Można użyć [wyzwalacza regularnego czasomierza](../functions-bindings-timer.md) , aby zająć się podstawowym scenariuszem, takim jak okresowe zadanie oczyszczania, ale jego interwał jest statyczny i zarządzanie okresami istnienia wystąpienia stanie się skomplikowany. Za pomocą Durable Functions można tworzyć Elastyczne interwały cykliczne, zarządzać okresami istnienia zadań oraz tworzyć wiele procesów monitorowania z jednej aranżacji.

Przykładem wzorca monitora jest odwrócenie wcześniejszego scenariusza asynchronicznego interfejsu API HTTP. Zamiast uwidaczniać punkt końcowy dla klienta zewnętrznego do monitorowania długotrwałej operacji, długotrwały monitor zużywa zewnętrzny punkt końcowy, a następnie czeka na zmianę stanu.

![Diagram wzorca monitora](./media/durable-functions-concepts/monitor.png)

W kilku wierszach kodu można użyć Durable Functions, aby utworzyć wiele monitorów, które obserwują dowolne punkty końcowe. Monitory mogą kończyć wykonywanie, gdy spełniony jest warunek, lub [DurableOrchestrationClient](durable-functions-instance-management.md) może zakończyć działanie monitorów. `wait` Interwał monitora można zmienić na podstawie określonego warunku (na przykład wykładniczy wycofywania). 

Poniższy kod implementuje podstawowy Monitor:

#### <a name="precompiled-c"></a>PrekompilowanegoC#

```csharp
[FunctionName("Orchestrator")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
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

#### <a name="c-script"></a>Skrypt języka C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
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

Po odebraniu żądania dla tego identyfikatora zadania zostanie utworzone nowe wystąpienie aranżacji. Wystąpienie sonduje stan do momentu spełnienia warunku, a pętla zostanie zakończona. Trwały czasomierz steruje interwałem sondowania. Następnie można wykonać więcej pracy lub zorganizować. Po przekroczeniu `expiryTime` wartości `context.df.currentUtcDateTime` (.NET)lub(JavaScript)Monitorzostaniezakończony.`context.CurrentUtcDateTime`

### <a name="human"></a>#5 wzorca: Interakcja z użytkownikami

Wiele zautomatyzowanych procesów dotyczy pewnego rodzaju interakcji przez człowieka. W przypadku ludzi w zautomatyzowanym procesie jest to trudne, ponieważ ludzie nie są w pełni dostępni i jako usługi w chmurze. Zautomatyzowany proces może pozwolić na to przy użyciu limitów czasu i logiki kompensacji.

Proces zatwierdzania jest przykładem procesu biznesowego, który obejmuje interakcje człowieka. Dla raportu wydatków, który przekracza określoną kwotę dolara, może być wymagane zatwierdzenie przez Menedżera. Jeśli Menedżer nie zatwierdzi raportu wydatków w ciągu 72 godzin (być może Menedżer przeszedł do urlopu), proces eskalacji zostanie rozpoczęty w celu uzyskania zatwierdzenia od kogoś innego (być może menedżerem).

![Diagram wzorca interakcji człowieka](./media/durable-functions-concepts/approval.png)

Wzorzec można zaimplementować w tym przykładzie przy użyciu funkcji programu Orchestrator. Program Orchestrator używa [trwałego czasomierza](durable-functions-timers.md) , aby zażądać zatwierdzenia. Koordynator zostanie przekazany w przypadku wystąpienia limitu czasu. Koordynator czeka na [zdarzenie zewnętrzne](durable-functions-external-events.md), takie jak powiadomienie, które zostało wygenerowane przez interakcję przez człowieka.

Te przykłady umożliwiają utworzenie procesu zatwierdzania w celu zademonstrowania wzorca interakcji człowieka:

#### <a name="precompiled-c"></a>PrekompilowanegoC#

```csharp
[FunctionName("Orchestrator")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
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
            await context.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="c-script"></a>Skrypt języka C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
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
            await context.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

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

Aby utworzyć trwały czasomierz, wywołaj `context.CreateTimer` (.NET) lub `context.df.createTimer` (JavaScript). Powiadomienie jest odbierane przez `context.WaitForExternalEvent` (.NET) lub `context.df.waitForExternalEvent` (JavaScript). Następnie ( `Task.WhenAny` .NET) lub `context.df.Task.any` (JavaScript) jest wywoływana, aby zdecydować, czy należy eskalować (przekroczyć limit czasu) lub przetworzyć zatwierdzenie (zatwierdzenie zostanie odebrane przed upływem limitu czasu).

Klient zewnętrzny może dostarczyć powiadomienie o zdarzeniu do oczekującej funkcji programu Orchestrator przy użyciu [wbudowanych interfejsów API protokołu HTTP](durable-functions-http-api.md#raise-event) lub przy użyciu interfejsu API [DurableOrchestrationClient. RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) z innej funkcji:

#### <a name="precompiled-c"></a>PrekompilowanegoC#

```csharp
public static async Task Run(
  [HttpTrigger] string instanceId,
  [OrchestrationClient] DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

#### <a name="c-script"></a>C#Napisy

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

#### <a name="javascript"></a>Javascript

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

### <a name="aggregator"></a>#6 wzorca: Agregator (wersja zapoznawcza)

Szósty wzorzec polega na agregowaniu danych zdarzeń w danym okresie do pojedynczej, adresowanej *jednostki*. W tym wzorcu dane agregowane mogą pochodzić z wielu źródeł, mogą być dostarczane w partiach lub mogą być rozproszone w długim okresie czasu. Agregator może wymagać podjęcia działania względem danych zdarzenia w miarę ich nadejścia, a klienci zewnętrzni mogą potrzebować zapytania do zagregowanych danych.

![Diagram agregatora](./media/durable-functions-concepts/aggregator.png)

W celu zaimplementowania tego wzorca przy użyciu normalnych, bezstanowych funkcji Kontrola współbieżności jest bardzo trudne. Nie tylko trzeba martwić się o wiele wątków modyfikujących te same dane w tym samym czasie, należy również pamiętać o zapewnieniu, że agregator działa tylko na jednej maszynie wirtualnej naraz.

Korzystając z [funkcji trwałej jednostki](durable-functions-preview.md#entity-functions), może ona łatwo zaimplementować ten wzorzec jako pojedynczą funkcję.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

Trwałe jednostki mogą być również modelowane jako klasy .NET. Może to być przydatne, jeśli lista operacji stanie się duża i jeśli jest głównie statyczna. Poniższy przykład jest równoważną implementacją `Counter` jednostki przy użyciu klas i metod platformy .NET.

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

Klienci mogą umieścić w kolejce *operacje* dla (zwane także "sygnalizacją") funkcją jednostki przy użyciu `orchestrationClient` powiązania.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [OrchestrationClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

Dynamicznie generowane serwery proxy są również dostępne do sygnalizowania jednostkami w bezpiecznym typie. Oprócz sygnalizowania klienci mogą również wysyłać zapytania dotyczące stanu funkcji jednostki przy użyciu metod w `orchestrationClient` powiązaniu.

> [!NOTE]
> Funkcje jednostki są obecnie dostępne tylko w [wersji zapoznawczej Durable Functions 2,0](durable-functions-preview.md).

## <a name="the-technology"></a>Technologia

W tle rozszerzenie Durable Functions jest tworzone na podstawie [trwałej struktury zadań](https://github.com/Azure/durabletask), biblioteki typu open source w usłudze GitHub, która jest używana do tworzenia trwałych aranżacji zadań. Podobnie jak Azure Functions jest ewolucją bezserwerową Azure WebJobs, Durable Functions jest bezserwerowym ewolucją struktury zadań trwałych. Firma Microsoft i inne organizacje często wykorzystują strukturę zadań trwałych w celu zautomatyzowania procesów o kluczowym znaczeniu. Jest to naturalne dopasowanie do bezserwerowego środowiska Azure Functionsowego.

### <a name="event-sourcing-checkpointing-and-replay"></a>Określanie źródła zdarzeń, tworzenie punktów kontrolnych i odtwarzanie

Program Orchestrator działa niezawodnie utrzymują swój stan wykonywania przy użyciu wzorca projektowego [pozyskiwania zdarzeń](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) . Zamiast bezpośredniego przechowywania bieżącego stanu aranżacji, rozszerzenie Durable Functions używa magazynu tylko do dołączenia do rejestrowania pełnego szeregu akcji podejmowanych przez aranżację funkcji. Magazyn tylko do dołączania ma wiele korzyści w porównaniu do "zatopienia" całego stanu środowiska uruchomieniowego. Korzyści obejmują zwiększoną wydajność, skalowalność i elastyczność. Istnieje również możliwość zapewnienia spójności ostatecznej danych transakcyjnych oraz pełnych i historycznych historii inspekcji. Dzienniki inspekcji obsługują niezawodne akcje kompensowania.

Durable Functions używa źródeł zdarzeń w sposób przezroczysty. W tle operatorC# `yield` () lub (JavaScript) w funkcji Orchestrator zapewnia kontrolę nad wątkiem programu Orchestrator z powrotem do dyspozytora trwałej struktury zadań. `await` Następnie Dyspozytor zatwierdza wszelkie nowe akcje zaplanowane przez funkcję programu Orchestrator (takie jak wywołanie co najmniej jednej funkcji podrzędnej lub planowanie trwałego czasomierza) do magazynu. Nieprzezroczysta akcja zatwierdzania dołącza do historii wykonywania wystąpienia aranżacji. Historia jest przechowywana w tabeli magazynu. Akcja Zatwierdź powoduje dodanie komunikatów do kolejki w celu zaplanowania rzeczywistej pracy. W tym momencie funkcja programu Orchestrator może zostać zwolniona z pamięci. 

Rozliczanie dla funkcji programu Orchestrator jest przerywane, jeśli używasz planu zużycia Azure Functions. Gdy jest więcej pracy, funkcja zostanie ponownie uruchomiona, a jej stan jest odtworzony.

Gdy funkcja aranżacji ma więcej pracy do wykonania (na przykład komunikat odpowiedzi jest odbierany lub trwały czasomierz wygasa), program Orchestrator wznawia działanie i ponownie wykonuje całą funkcję od początku, aby skompilować stan lokalny. 

W trakcie powtarzania, jeśli kod próbuje wywołać funkcję (lub wykonać inną zadani asynchroniczne), w środowisku trwałym można sprawdzić historię wykonywania bieżącej aranżacji. Jeśli okaże się, że [Funkcja działania](durable-functions-types-features-overview.md#activity-functions) została już wykonana i wygeneruje wynik, odtwarza wynik tej funkcji, a kod programu Orchestrator nadal działa. Odtwarzanie powtarza się do momentu zakończenia kodu funkcji lub do momentu zaplanowania nowej pracy asynchronicznej.

### <a name="orchestrator-code-constraints"></a>Ograniczenia kodu programu Orchestrator

Zachowanie powtarzania kodu programu Orchestrator tworzy ograniczenia dotyczące typu kodu, który można napisać w funkcji programu Orchestrator. Na przykład kod programu Orchestrator musi być deterministyczny, ponieważ będzie powtarzany wielokrotnie i musi generować ten sam wynik za każdym razem. Aby uzyskać pełną listę ograniczeń, zobacz [ograniczenia kodu programu Orchestrator](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints).

## <a name="monitoring-and-diagnostics"></a>Monitorowanie i diagnostyka

Rozszerzenie Durable Functions automatycznie emituje dane śledzenia strukturalnego, aby [Application Insights](../functions-monitoring.md) w przypadku skonfigurowania aplikacji funkcji przy użyciu klucza Instrumentacji usługi Azure Application Insights. Możesz użyć danych śledzenia do monitorowania akcji i postępu aranżacji.

Poniżej przedstawiono przykładowe zdarzenia śledzenia Durable Functions w portalu Application Insights w przypadku korzystania z usługi [Application Insights Analytics](../../application-insights/app-insights-analytics.md):

![Application Insights wyniki zapytania](./media/durable-functions-concepts/app-insights-1.png)

Przydatne dane strukturalne można znaleźć w `customDimensions` polu w każdym wpisie dziennika. Oto przykład wpisu, który jest w pełni rozwinięty:

![Pole customDimensions w zapytaniu Application Insights](./media/durable-functions-concepts/app-insights-2.png)

Ze względu na zachowanie podczas odtwarzania trwałego dyspozytora struktury zadań można oczekiwać, że wpisy dziennika nadmiarowego są wyświetlane w przypadku akcji odtwarzanych. Nadmiarowe wpisy dziennika mogą pomóc w zrozumieniu zachowania powtarzania aparatu podstawowego. W artykule dotyczącym [diagnostyki](durable-functions-diagnostics.md) przedstawiono przykładowe zapytania, które odfiltrują dzienniki powtarzania, dzięki czemu można zobaczyć tylko dzienniki "w czasie rzeczywistym".

## <a name="storage-and-scalability"></a>Magazyn i skalowalność

Rozszerzenie Durable Functions używa kolejek, tabel i obiektów BLOB w usłudze Azure Storage w celu utrwalenia stanu historii wykonywania i uruchomienia funkcji wyzwalacza. Możesz użyć domyślnego konta magazynu dla aplikacji funkcji lub można skonfigurować oddzielne konto magazynu. Można chcieć użyć oddzielnego konta na podstawie limitów przepływności magazynu. Zapisany kod programu Orchestrator nie współdziała z jednostkami na tych kontach magazynu. Trwała struktura zadań zarządza jednostkami bezpośrednio jako szczegóły implementacji.

Funkcje programu Orchestrator planują funkcje działania i odbierają odpowiedzi za pośrednictwem wewnętrznych komunikatów w kolejce. Gdy aplikacja funkcji jest uruchamiana w planie zużycia Azure Functions, [kontroler Azure Functions skalowania](../functions-scale.md#how-the-consumption-and-premium-plans-work) monitoruje te kolejki. Nowe wystąpienia obliczeniowe są dodawane zgodnie z wymaganiami. W przypadku skalowania do wielu maszyn wirtualnych funkcja programu Orchestrator może działać na jednej maszynie wirtualnej, natomiast funkcje działania, które wywołuje funkcja programu Orchestrator, mogą działać na kilku różnych maszynach wirtualnych. Aby uzyskać więcej informacji na temat zachowania skalowania Durable Functions, zobacz [wydajność i skalowanie](durable-functions-perf-and-scale.md).

Historia wykonywania dla kont programu Orchestrator jest przechowywana w usłudze Table Storage. Za każdym razem, gdy wystąpienie jest zmieniane na określonej maszynie wirtualnej, koordynator pobiera swoją historię wykonywania z magazynu tabel, aby można było skompilować swój stan lokalny. Wygodnym aspektem posiadania historii dostępnej w usłudze Table Storage jest możliwość używania narzędzi takich jak [Eksplorator usługi Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md) , aby zobaczyć historię swoich aranżacji.

Obiekty blob magazynu są głównie używane jako mechanizm dzierżawienia w celu koordynowania wystąpień aranżacji między wieloma maszynami wirtualnymi. Obiekty blob magazynu przechowują dane w przypadku dużych komunikatów, które nie mogą być przechowywane bezpośrednio w tabelach lub kolejkach.

![Zrzut ekranu przedstawiający Eksplorator usługi Azure Storage](./media/durable-functions-concepts/storage-explorer.png)

> [!NOTE]
> Mimo że jest to łatwe i wygodne Wyświetlanie historii wykonywania w usłudze Table Storage, nie należy wprowadzać żadnych zależności w tej tabeli. Tabela może ulec zmianie w miarę rozwoju rozszerzenia Durable Functions.

## <a name="known-issues"></a>Znane problemy

Wszystkie znane problemy powinny być śledzone na liście [problemów usługi GitHub](https://github.com/Azure/azure-functions-durable-extension/issues) . Jeśli napotkasz problem i nie możesz znaleźć problemu w usłudze GitHub, Otwórz nowy problem. Dołącz szczegółowy opis problemu.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Durable Functions, zobacz [Durable Functions funkcji i funkcji](durable-functions-types-features-overview.md). 

Aby rozpocząć pracę:

> [!div class="nextstepaction"]
> [Tworzenie pierwszej trwałej funkcji](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html