---
title: Trwałe wzorce funkcji i zagadnienia techniczne w usłudze Azure Functions
description: Dowiedz się, jak rozszerzenia funkcji trwałych w usłudze Azure Functions zapewnia korzyści z wykonywania kodu stanowych w chmurze.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: a244883f470f4906879725daf0d37bd1759e65c4
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812907"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Trwałe wzorce funkcji i zagadnienia techniczne (usługi Azure Functions)

Funkcje trwałe to rozszerzenie [usługi Azure Functions](../functions-overview.md) i [zadań Azure WebJobs](../../app-service/web-sites-create-web-jobs.md). Trwałe funkcje służy do pisania funkcji stanowych w środowisku bezserwerowym. Rozszerzenie zarządza stanem, punktami kontrolnymi i ponownym uruchamianiem. 

Podano szczegółowe informacje na temat zachowania rozszerzenia funkcji trwałych dla usługi Azure Functions i typowych wzorców implementacji. Informacje mogą pomóc w określeniu, jak pomóc w rozwiązywaniu wyzwaniom związanym z programowaniem za pomocą funkcje trwałe.

> [!NOTE]
> Funkcje trwałe to rozszerzenie zaawansowany dla usługi Azure Functions, która nie jest odpowiednia dla wszystkich aplikacji. W tym artykule założono, że masz silne znajomość pojęcia związane z [usługi Azure Functions](../functions-overview.md) i wyzwania zaangażowana w rozwój aplikacji bez użycia serwera.

## <a name="patterns"></a>Wzorce

W tej sekcji opisano niektóre typowe wzorce aplikacji, w którym może być przydatne funkcje trwałe.

### <a name="chaining"></a>Wzorzec #1: Łączenie funkcji w łańcuchy

W funkcji łańcucha wzorzec sekwencji funkcji wykonuje się w określonej kolejności. W tym wzorcu dane wyjściowe w jednej funkcji — są stosowane do danych wejściowych innej funkcji.

![Diagram funkcji łańcucha wzorzec](./media/durable-functions-concepts/function-chaining.png)

Trwałe funkcje służy do implementowania funkcji łańcucha wzorzec zwięźle, jak pokazano w poniższym przykładzie:

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
> Istnieją drobne różnice między pisania funkcję trwałego prekompilowanego C# i pisania funkcję trwałego prekompilowanego C# skrypt, który jest wyświetlany w przykładzie. W C# prekompilowanych funkcji, trwałe parametry muszą być dekorowane za pomocą odpowiednich atrybutów. Na przykład `[OrchestrationTrigger]` atrybutu dla `DurableOrchestrationContext` parametru. W C# prekompilowanych trwałe funkcji, jeśli parametry nie są poprawnie uzupełniona, środowisko wykonawcze nie można wstrzyknąć zmienne do funkcji i wystąpi błąd. Aby uzyskać więcej przykładów, zobacz [azure-functions-durable-extension przykłady w witrynie GitHub](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples).

#### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

W tym przykładzie wartości `F1`, `F2`, `F3`, i `F4` są nazwami innych funkcji w aplikacji funkcji. Przepływ sterowania można wdrożyć przy użyciu normalnych imperatywne kodowania konstrukcji. Kod jest wykonywany z góry w dół. Kod może obejmować istniejących semantykę przepływu sterowania języka, takich jak instrukcje warunkowe i pętle. Może zawierać logika obsługi błędów `try` / `catch` / `finally` bloków.

Możesz użyć `context` parametru [DurableOrchestrationContext] \(.NET\) i `context.df` wywołania innych funkcji przez nazwę, parametry są przekazywane i zwraca funkcję — obiekt (JavaScript) dane wyjściowe. Każdym kod wywołuje `await` (C#) lub `yield` (JavaScript), punkty kontrolne framework funkcje trwałe postęp bieżące wystąpienie funkcji. Jeśli proces lub maszyny Wirtualnej jest odtwarzany midway przez wykonanie, wystąpienie funkcji wznawia z poprzednim `await` lub `yield` wywołania. Aby uzyskać więcej informacji zobacz następną sekcję — wzorzec nr 2: Wentylator poza/wychodzącą.

> [!NOTE]
> `context` Obiekt w języku JavaScript reprezentuje cały [kontekście funkcja](../functions-reference-node.md#context-object), nie tylko [DurableOrchestrationContext] parametru.

### <a name="fan-in-out"></a>Wzorzec #2: Wentylator, poza/wentylatora

W wentylatora poza/wentylator we wzorcu, równolegle uruchomić wiele funkcji, a następnie poczekaj, aż wszystkie funkcje, aby zakończyć. Często niektóre zadania agregacji są wykonywane na wyniki, które są zwracane przez funkcje.

![Diagram wentylatora poza/wentylator wzorzec](./media/durable-functions-concepts/fan-out-fan-in.png)

Przy użyciu normalnych funkcji może rozdysponować się przez funkcję Wyślij wiele wiadomości do kolejki. Wentylujące ponownie jest znacznie trudniejsze. Aby wentylatorów, w przypadku normalnej funkcji pisanie kodu, aby śledzić, kiedy końcowego funkcji wyzwalanej przez kolejkę, a następnie magazynu funkcji danych wyjściowych. 

Rozszerzenia funkcji trwałych obsługuje ten wzorzec kodem stosunkowo proste:

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

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

Praca wielokierunkowego jest dystrybuowany do wielu wystąpień `F2` funkcji. Praca jest śledzone przy użyciu dynamicznej listy zadań. .NET `Task.WhenAll` JavaScript i interfejsów API `context.df.Task.all` wywoływany jest interfejs API oczekiwania na zakończenie wszystkich wywoływanych funkcji. Następnie `F2` funkcja danych wyjściowych są agregowane z listy zadań dynamiczne i przekazywane do `F3` funkcji.

Automatyczne tworzenie punktów kontrolnych, które odbywa się na `await` lub `yield` wywołanie na `Task.WhenAll` lub `context.df.Task.all` gwarantuje, że midway awarię lub ponowne uruchomienie komputera nie wymaga ponownego uruchamiania już ukończone zadanie podrzędne.

### <a name="async-http"></a>Wzorzec #3: Interfejsy API protokołu HTTP Async

Interfejsy API protokołu HTTP, wzorzec async rozwiązuje problem z koordynowanie stanu operacji długotrwałych z klientami zewnętrznymi. Typowa implementacja tego wzorca jest posiadanie wywołania akcji długotrwałych wyzwalacza HTTP. Następnie przekierowuje klienta do punktu końcowego stanu, który sonduje klienta, aby dowiedzieć się więcej, po zakończeniu operacji.

![Diagram wzorzec interfejsu API protokołu HTTP](./media/durable-functions-concepts/async-http-api.png)

Trwałe funkcje udostępnia wbudowane interfejsy API, które upraszczają kod, który można zapisać do interakcji z długim wykonań funkcji. Trwałe funkcje quickstart — przykłady ([ C# ](durable-functions-create-first-csharp.md) i [JavaScript](quickstart-js-vscode.md)) Pokaż prostego polecenia REST, który umożliwia uruchomienie nowych wystąpień funkcji programu orchestrator. Po uruchomieniu wystąpienia rozszerzenie udostępnia interfejsy API protokołu HTTP, które zbadać stan funkcji programu orchestrator elementów webhook. 

Poniższy przykład pokazuje poleceniami REST, które uruchamiają orchestrator i zbadać jego stan. Dla jasności pominięto niektóre szczegóły z przykładu.

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

Ponieważ środowisko uruchomieniowe usługi Functions trwałe zarządza stanu, nie trzeba zaimplementować własny mechanizm śledzenia stanu.

Rozszerzenia funkcji trwałych zawiera wbudowane elementy webhook, które zarządzać długotrwałych aranżacji. Można zaimplementować ten wzorzec samodzielnie przy użyciu własnych wyzwalacze funkcji (np. HTTP, kolejki lub usługi Azure Event Hubs) i `orchestrationClient` powiązania. Może na przykład użyć komunikatu w kolejce, aby wyzwolić zakończenia. Alternatywnie można użyć wyzwalacza HTTP, który jest chroniony przez zasady uwierzytelniania usługi Azure Active Directory zamiast wbudowane elementy webhook, które wygenerowany klucz na użytek uwierzytelniania.

Poniżej przedstawiono kilka przykładów sposobu użycia wzorca interfejsu API protokołu HTTP:

#### <a name="c"></a>C#

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

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

Na platformie .NET [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` parametr ma wartość z zakresu od `orchestrationClient` danych wyjściowych powiązania, który jest częścią rozszerzenia funkcji trwałych. W języku JavaScript, ten obiekt jest zwracany przez wywołanie metody `df.getClient(context)`. Te obiekty zawierają metody, które służy do uruchamiania, wysyłanie zdarzeń do, zakończenia i zapytania dla wystąpień funkcji nowego lub istniejącego programu orchestrator.

W powyższych przykładach, przyjmuje funkcji wyzwalanej przez HTTP `functionName` wartość z adresu URL przychodzące i przekazuje wartość [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) powiązanie interfejsu API, następnie zwraca odpowiedź zawierającą `Location` nagłówek i dodatkowe informacje na temat wystąpienia. Informacje można użyć później, sprawdzić stan uruchomiono wystąpienie lub zakończyć wystąpienia.

### <a name="monitoring"></a>Wzorzec #4: Monitorowanie

Wzorzec monitor odnosi się do procesu elastyczne, cykliczne w przepływie pracy. Przykładem jest sondowania, dopóki nie są spełnione określone warunki. Można użyć regularnych [wyzwalacza czasomierza](../functions-bindings-timer.md) do adresu podstawowego scenariusza, takiego jak zadanie okresowe czyszczenie, ale jej interwał jest statyczna i zarządzanie okresy istnienia wystąpienia staje się złożone. Trwałe funkcje umożliwia tworzenie elastycznych cyklu w odstępach czasu i zarządzanie okresy istnienia zadania Tworzenie monitor wielu procesów na podstawie pojedynczego aranżacji.

Przykładem wzorca monitor jest odwrotna wcześniej scenariusza async interfejsu API protokołu HTTP. Zamiast uwidaczniania punktu końcowego dla klienta zewnętrznego do monitorowania operacji długotrwałych, monitor długotrwałych wykorzystuje zewnętrzny punkt końcowy, a następnie czeka na zmianę stanu.

![Diagram wzorzec monitora](./media/durable-functions-concepts/monitor.png)

W zaledwie kilku wierszach kodu można użyć funkcje trwałe, aby utworzyć wiele monitorów, w których stosuje się dowolne punkty końcowe. Monitory można zakończyć wykonywania, gdy warunek jest spełniony, lub [DurableOrchestrationClient](durable-functions-instance-management.md) może zakończyć monitorów. Możesz zmienić monitor `wait` interwał na podstawie określonego warunku (na przykład wykładniczego wycofywania.) 

Poniższy kod implementuje podstawowe monitora:

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

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

Po odebraniu żądania tworzone jest nowe wystąpienie aranżacji w dla tego identyfikatora zadania. Wystąpienie sonduje stan, dopóki spełniony jest warunek i pętla zostanie zakończone. Trwałe czasomierza Określa interwał sondowania. Następnie można wykonać więcej pracy lub zakończyć aranżacji. Gdy `context.CurrentUtcDateTime` (.NET) lub `context.df.currentUtcDateTime` (JavaScript) przekracza `expiryTime` wartość kończy się monitora.

### <a name="human"></a>Wzorzec #5: Interakcja z użytkownikami

Wiele zautomatyzowane procesy obejmują pewnego rodzaju z reakcji człowieka. Obejmujących ludzie w zautomatyzowany proces jest trudne, ponieważ osoby nie są jako wysoko dostępna i jak działa prawidłowo w usłudze cloud services. Zautomatyzowany proces może umożliwić przy użyciu logiki przekroczeń limitu czasu i rekompensaty.

Proces zatwierdzania jest przykładem procesu biznesowego, który obejmuje z reakcji człowieka. Zatwierdzenie przez Menedżera może być wymagane dla raportu wydatków przekraczającą pewien dolara. Jeśli Menedżer nie dokonał zatwierdzenia raportu wydatków w ciągu 72 godzin (być może Menedżer zakończył się na urlopie), proces eskalacji jest uruchamiane, aby uzyskać zatwierdzenie od kogoś innego (być może Menedżer manager).

![Diagram wzorca z reakcji człowieka](./media/durable-functions-concepts/approval.png)

Aby zaimplementować wzorzec w tym przykładzie, za pomocą funkcji orkiestratora. Program orchestrator używa [trwałe czasomierza](durable-functions-timers.md) wniosków o zatwierdzenie. Koordynatora Eskalowanie w przypadku przekroczenia limitu czasu. Czeka koordynatora [zewnętrznego zdarzenia](durable-functions-external-events.md), takich jak powiadomienia, który jest generowany przez człowieka.

Te przykłady tworzą proces zatwierdzania, aby zademonstrować wzorca interakcji z człowiekiem:

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

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

Aby utworzyć trwałe czasomierza, wywołaj `context.CreateTimer` (.NET) lub `context.df.createTimer` (JavaScript). W przypadku otrzymania powiadomienia przez `context.WaitForExternalEvent` (.NET) lub `context.df.waitForExternalEvent` (JavaScript). Następnie `Task.WhenAny` (.NET) lub `context.df.Task.any` (JavaScript) jest wywoływana, aby zdecydować, czy do podwyższania poziomu (limit czasu jest wykonywane najpierw) lub przetwarzać zatwierdzenia (zatwierdzenia odebrane przed upływem limitu czasu).

Klienta zewnętrznego mogą dostarczać powiadomienie o zdarzeniu do funkcji programu orchestrator oczekiwania przy użyciu [wbudowane interfejsy API protokołu HTTP](durable-functions-http-api.md#raise-event) lub za pomocą [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) interfejsu API inną funkcję:

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

### <a name="aggregator"></a>Wzorzec #6: Agregatora (wersja zapoznawcza)

Szósty wzorzec dotyczy agregowanie danych zdarzeń przez okres czasu w jednym, mogą być adresowane *jednostki*. W tym wzorcu danych agregowana mogą pochodzić z wielu źródeł, mogą być dostarczane w plikach wsadowych lub mogą być rozproszone w długich okresach czasu. Agregator może być konieczne podejmowanie akcji na dane zdarzeń, zgodnie z jego nadejścia i może być konieczne wykonywanie zapytań o dane zagregowane klientów zewnętrznych.

![Diagram agregatora](./media/durable-functions-concepts/aggregator.png)

Skomplikowanych czynności o podjęcie próby implementacja tego wzorca przy użyciu normalnego bezstanowe funkcje jest, czy kontroli współbieżności staje się ogromnym wyzwaniom. Nie tylko muszą zajmować wiele wątków, zmodyfikowanie tych samych danych, w tym samym czasie, również muszą się martwić o zapewnienie, że agregator działa tylko w jednej maszyny Wirtualnej w danym momencie.

Za pomocą [funkcji jednostki trwałe](durable-functions-preview.md#entity-functions), jeden można zaimplementować ten wzorzec łatwo jako pojedynczą funkcję.

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

Trwałe jednostki mogą być również modelowane jako klas .NET. Może to być przydatne, jeśli listy operacji stanie się zbyt długa, a przede wszystkim jest statyczna. Poniższy przykład jest równoważny implementację `Counter` jednostki przy użyciu metod i klas platformy .NET.

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

Klientów można umieścić w kolejce *operacji* do (znany także jako "sygnalizacji") funkcji jednostki przy użyciu `orchestrationClient` powiązania.

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

Dynamicznie generowanym serwery proxy są również dostępne dla sygnalizowanie jednostek w sposób bezpieczny. A oprócz sygnalizowanie, klientów można także wysyłać zapytania dotyczące stanu funkcji jednostki przy użyciu metod na `orchestrationClient` powiązania.

> [!NOTE]
> Funkcje jednostki są obecnie dostępne wyłącznie w [niezawodne funkcje 2.0 w wersji zapoznawczej](durable-functions-preview.md).

## <a name="the-technology"></a>Technologia

W tle, rozszerzenia funkcji trwałych jest wbudowana w górnej części [trwałe Framework zadań](https://github.com/Azure/durabletask), biblioteka typu open source w serwisie GitHub, który jest używany do tworzenia aranżacji trwałe zadania. Jak usługa Azure Functions jest ewolucji bez użycia serwera usługi Azure WebJobs, funkcje trwałe to bezserwerowe ewolucji trwałe Framework zadania. Firma Microsoft i innych organizacji umożliwia trwałe Framework zadanie często automatyzować procesy o kluczowym znaczeniu. Jest to naturalny wybór dla środowiska bez użycia serwera usługi Azure Functions.

### <a name="event-sourcing-checkpointing-and-replay"></a>Określanie źródła zdarzeń, punkt kontrolny i powtarzanie

Funkcje programu orchestrator w sposób niezawodny utrzymać ich stan wykonywania za pomocą [określania źródła zdarzeń](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) wzorca projektowego. Zamiast bezpośredniego przechowywania bieżący stan aranżacji, rozszerzenia funkcji trwałych używa magazynu tylko do dołączania do rejestrowania pełnych szeregów działań, potrzebnego do aranżacji funkcji. Magazyn tylko do dołączania ma wiele zalet w porównaniu do "zrzucanie" stan pełnego środowiska uruchomieniowego. Korzyści to m.in. zwiększyć wydajność, skalowalność i elastyczność. Możesz także uzyskać spójność danych transakcyjnych i pełnego dziennika inspekcji i historii. Ślady inspekcji obsługuje niezawodne akcje kompensacyjne.

Trwałe funkcje używa event sourcing w sposób niewidoczny dla użytkownika. Za kulisami `await` (C#) lub `yield` operator (JavaScript) w funkcji programu orchestrator daje kontroli wątku orchestrator do dyspozytora trwałe Framework zadania. Dyspozytor następnie zatwierdza nowe akcje, które funkcja orkiestratora zaplanowane (takich jak wywoływanie jedną lub więcej funkcji podrzędnych lub planowania trwałe czasomierza) do magazynu. Akcja zatwierdzenia przezroczyste dołącza historię wykonywania wystąpienia aranżacji. Historia jest przechowywana w tabeli magazynu. Następnie za pomocą akcji zatwierdzenia komunikaty są dodawane do kolejki, aby zaplanować faktyczną pracę. W tym momencie funkcja orkiestratora, może być zwolniony z pamięci. 

Rozliczenia dla funkcji programu orchestrator zatrzymuje, jeśli używasz usługi Azure Functions planu zużycie. Gdy istnieje więcej pracy do wykonania funkcji jest uruchamiany ponownie, i jego stan jest odtworzone.

Gdy funkcja aranżacji otrzymuje więcej pracy do wykonania (na przykład odebraniu komunikatu odpowiedzi lub okresu działania czasomierza trwałość), koordynatora budzi się i ponownie uruchamia całą funkcję od samego początku, aby odbudować stan lokalnego. 

Podczas powtórzeń, jeśli kod próbuje wywołać funkcję (lub inne async pracy), trwałe Framework zadań konsultacje dotyczące historii wykonywania bieżącej aranżacji. Jeśli stwierdzi, że [działania funkcji](durable-functions-types-features-overview.md#activity-functions) już wykonane i uzyskanych wyników, powoduje ponowne uruchomienie wynik tej funkcji i kod programu orchestrator będzie kontynuowane do czasu. Powtarzanie będzie kontynuowane dopiero po zakończeniu kod funkcji, lub do momentu jej zaplanowano nowe Praca asynchroniczna.

### <a name="orchestrator-code-constraints"></a>Ograniczenia kodu programu orchestrator

Zachowanie powtarzania kod orchestrator tworzy ograniczenia dotyczące typu kodu, który może zapisać w funkcji programu orchestrator. Na przykład kod orchestrator musi być deterministyczna, ponieważ jego zostaną odtworzone wiele razy, a musi mieć ten sam wynik każdorazowo. Aby uzyskać pełną listę ograniczeń, zobacz [ograniczenia kodu programu Orchestrator](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints).

## <a name="monitoring-and-diagnostics"></a>Monitorowanie i diagnostyka

Rozszerzenia funkcji trwałych automatycznie emituje danych ze strukturą śledzenia [usługi Application Insights](../functions-monitoring.md) po skonfigurowaniu aplikacji funkcji przy użyciu klucza Instrumentacji usługi Azure Application Insights. Dane śledzenia służy do monitorowania działań i postęp swoje aranżacji.

Oto przykład trwałe funkcje śledzenia zdarzeń wygląd w portalu usługi Application Insights gdy używasz [analizy usługi Application Insights](../../application-insights/app-insights-analytics.md):

![Wyniki zapytania programu Application Insights](./media/durable-functions-concepts/app-insights-1.png)

Znajdziesz przydatne dane ze strukturą w `customDimensions` pole każdego wpisu dziennika. Oto przykład wpisu, który jest w pełni rozwinięta:

![Pola tabeli customDimensions w zapytaniu usługi Application Insights](./media/durable-functions-concepts/app-insights-2.png)

Ze względu na zachowanie powtarzania dyspozytora trwałe Framework zadania może pojawić się wpisy dziennika nadmiarowe powtórzonym akcji. Wpisy dziennika nadmiarowe może ułatwić zrozumienie zachowania powtarzania podstawowy aparat. [Diagnostyki](durable-functions-diagnostics.md) artykule przedstawiono przykładowe zapytania, które odfiltrować powtarzania dzienniki, aby było widać tylko dzienniki "w czasie rzeczywistym".

## <a name="storage-and-scalability"></a>Magazyn i skalowalność

Rozszerzenia funkcji trwałych używa kolejek, tabel i obiektów blob w usłudze Azure Storage do utrwalenia wykonywania historii stanu i wyzwalacza wykonywania funkcji. Można użyć domyślnego konta magazynu dla aplikacji funkcji, lub można skonfigurować oddzielne konto magazynu. Możesz chcieć oddzielnego konta, na podstawie limitów przepływności magazynu. Utworzony kod programu orchestrator nie wchodzi w interakcje przy użyciu jednostek w ramach tych kont magazynu. Trwałe Framework zadań zarządza jednostek bezpośrednio jako szczegółowo opisuje implementacja.

Funkcje programu orchestrator zaplanować działania funkcji i otrzymywać odpowiedzi za pomocą wewnętrznej kolejki komunikatów. Uruchomienie aplikacji funkcji w planie zużycie usługi Azure Functions [kontrolera skalowania usługi Azure Functions](../functions-scale.md#how-the-consumption-and-premium-plans-work) monitoruje te kolejki. Nowe wystąpienia obliczeniowe są dodawane, zgodnie z potrzebami. Podczas skalowania do wewnątrz na wielu maszynach wirtualnych funkcji programu orchestrator mogą być uruchamiane w jednej maszyny Wirtualnej podczas działania funkcji, które wywołania funkcji programu orchestrator mogą być uruchamiane na kilka różnych maszyn wirtualnych. Aby uzyskać więcej informacji na temat zachowania skalowania funkcje trwałe, zobacz [wydajności i skali](durable-functions-perf-and-scale.md).

Historia wykonywania kont programu orchestrator są przechowywane w usłudze table storage. Zawsze, gdy wystąpienie rehydrates na konkretnej maszyny Wirtualnej, koordynatora pobiera historię jej wykonywania z usługi table storage, dzięki czemu można odtworzyć, jego stan lokalnego. Wygodne aspektów o dostępnej w usłudze table storage historii jest użyć narzędzi, takich jak [Eksploratora usługi Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md) aby zobaczyć historię użytkownika aranżacji.

Magazyn obiektów blob przede wszystkim są używane jako mechanizm dzierżawienia do koordynowania skalowanie wystąpień aranżacji na wielu maszynach wirtualnych. Magazyn obiektów blob przechowuje dane dla dużych komunikatów, które nie mogą być przechowywane bezpośrednio tabele i kolejki.

![Zrzut ekranu przedstawiający Eksploratora usługi Azure Storage](./media/durable-functions-concepts/storage-explorer.png)

> [!NOTE]
> Chociaż można łatwo i wygodnie wyświetlać historię wykonania w usłudze table storage, nie należy wszelkie zależności w tej tabeli. Tabela mogą ulec zmianie w miarę rozwoju rozszerzenia funkcji trwałych.

## <a name="known-issues"></a>Znane problemy

Powinny być śledzone wszystkie znane problemy występujące w [problemy usługi GitHub](https://github.com/Azure/azure-functions-durable-extension/issues) listy. W przypadku napotkania problemów i nie można odnaleźć problem w serwisie GitHub, otwórz nowy problem. Zawierają szczegółowy opis problemu.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat funkcje trwałe, zobacz [funkcje trwałe funkcji typy i funkcje](durable-functions-types-features-overview.md). 

Aby rozpocząć pracę:

> [!div class="nextstepaction"]
> [Tworzenie pierwszej funkcji trwałych](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html
