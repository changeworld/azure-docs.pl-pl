---
title: Przegląd funkcje trwałe — platformy Azure
description: Wprowadzenie do rozszerzenia funkcji trwałych dla usługi Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/7/2018
ms.author: azfuncdf
ms.openlocfilehash: cf643c8c2a0921cfbe1f5599a9e96a27770ef1ef
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718592"
---
# <a name="durable-functions-overview"></a>Trwałe Functions — omówienie

*Trwałe funkcje* jest rozszerzeniem [usługi Azure Functions](../functions-overview.md) i [zadań Azure WebJobs](../../app-service/webjobs-create.md) umożliwiający zapis stanowych funkcji w środowisku bezserwerowym. Rozszerzenie zarządza stanem, punktami kontrolnymi i ponownym uruchamianiem.

Rozszerzenie pozwala zdefiniować stanowa przepływy pracy nowego typu funkcji o nazwie [ *funkcję orkiestratora*](durable-functions-types-features-overview.md#orchestrator-functions). Poniżej przedstawiono niektóre zalety funkcji programu orchestrator:

* Przepływy pracy mogą określać w kodzie. Projektantów lub schematów JSON nie są wymagane.
* Wywołują innych funkcji synchronicznie i asynchronicznie. Zmienne lokalne można zapisywać dane wyjściowe z wywoływane funkcje.
* One automatycznie punktu kontrolnego ich postęp w każdym przypadku, gdy czeka na funkcję. Stan lokalnego nigdy nie jest utracone, jeśli proces jest odtwarzana lub ponowne uruchomienie maszyny Wirtualnej.

> [!NOTE]
> Funkcje trwałe to rozszerzenie zaawansowany dla usługi Azure Functions, który nie jest odpowiednia dla wszystkich aplikacji. W pozostałej części tego artykułu przyjęto założenie, iż silne znajomość [usługi Azure Functions](../functions-overview.md) pojęcia i o wyzwaniach zaangażowana w rozwój aplikacji bez użycia serwera.

Podstawowym zastosowaniem przypadku funkcje trwałe jest upraszczanie złożonych i stanowej koordynacji problemy w aplikacji bez użycia serwera. W poniższych sekcjach opisano niektóre wzorce typowej aplikacji, które mogą korzystać z funkcji trwałe.

## <a name="pattern-1-function-chaining"></a>Wzorzec #1: Łączenie funkcji w łańcuchy

*Funkcja tworzenia łańcucha* odwołuje się do wzorca wykonywanie sekwencji funkcji w określonej kolejności. Często dane wyjściowe jedna funkcja musi zostać zastosowana na dane wejściowe innej funkcji.

![Diagram łańcucha — funkcja](./media/durable-functions-overview/function-chaining.png)

Trwałe funkcje pozwala zwięźle implementacja tego wzorca, w kodzie.

### <a name="c-script"></a>Skrypt języka C#

```cs
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
        // error handling/compensation goes here
    }
}
```

> [!NOTE]
> Istnieją drobne różnice podczas zapisywania w programie vs języka C# funkcję trwałego prekompilowanego przykładowego skryptu języka C# pokazano wcześniej. Funkcja języka C# wstępnie skompilowany wymagałoby trwałe parametry, aby być dekorowane za pomocą odpowiednich atrybutów. Na przykład `[OrchestrationTrigger]` atrybutu dla `DurableOrchestrationContext` parametru. Jeśli parametry nie są prawidłowo dekorowane, środowisko wykonawcze nie będą mogli wstrzyknąć zmienne do funkcji i spowodowałoby to nadanie błędu. Odwiedź stronę [przykładowe](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples) więcej przykładów.

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```js
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

Wartości "F1", "F2", "F3" i "F4" są nazwami innych funkcji w aplikacji funkcji. Przepływ sterowania jest implementowany przy użyciu normalnych imperatywne kodowania konstrukcji. Oznacza to, że kod wykonuje góra dół i może obejmować istniejących semantykę przepływu sterowania języka, takich jak instrukcje warunkowe i pętle.  Logika obsługi błędów mogą być dołączane w blokach try/catch/finally.

`context` Parametru ([DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html)) (.NET) i `context.df` obiekt (JavaScript) zapewnia metody do wywołania innych funkcji przy użyciu nazwy, przekazywanie parametrów i zwraca dane wyjściowe funkcji. Każdym kod wywołuje `await` (C#) lub `yield` (JavaScript) w ramach funkcje trwałe *punkty kontrolne* postęp bieżące wystąpienie funkcji. Jeśli proces lub maszyny Wirtualnej jest odtwarzany midway przez wykonanie, wystąpienie funkcji wznawia z poprzedniego `await` lub `yield` wywołania. Więcej informacji na temat to zachowanie ponownego uruchamiania później.

> [!NOTE]
> `context` Obiektu w języku JavaScript reprezentuje [funkcja kontekst jako całość], nie DurableOrchestrationContext. (.. / node.md-functions odwołanie do obiektu context #).

## <a name="pattern-2-fan-outfan-in"></a>Wzorzec #2: Model fan-out/fan-in

*Fan-wyjściowego/fan-w* odwołuje się do wzorca wykonywanie równoległe wiele funkcji, a następnie oczekiwanie na wszystkie, aby zakończyć.  Często niektóre zadania agregacji są wykonywane na wyniki zwrócone z funkcji.

![Diagram Fan-wyjściowego/fan-w](./media/durable-functions-overview/fan-out-fan-in.png)

Przy użyciu normalnych funkcji wentylujące jest możliwe dzięki funkcji wysłać wiele wiadomości do kolejki. Jednak wentylujące w jest znacznie trudniejsze. Trzeba napisać kod, aby śledzić, kiedy funkcji wyzwalanej przez kolejkę zakończenia i przechowywać dane wyjściowe funkcji. Rozszerzenia funkcji trwałych obsługuje ten wzorzec kodem stosunkowo proste.

### <a name="c-script"></a>Skrypt języka C#

```cs
public static async Task Run(DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // get a list of N work items to process in parallel
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // aggregate all N outputs and send result to F3
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```js
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // get a list of N work items to process in parallel
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // aggregate all N outputs and send result to F3
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

Praca wielokierunkowego jest dystrybuowany do wielu wystąpień funkcji `F2`, i śledzenia pracy przy użyciu dynamicznej listy zadań. .NET `Task.WhenAll` JavaScript i interfejsów API `context.df.Task.all` wywoływany jest interfejs API oczekiwania dla wszystkich wywoływanych funkcji, aby zakończyć. A następnie `F2` funkcja danych wyjściowych są agregowane z listy zadań dynamiczne i przekazywane do `F3` funkcji.

Automatyczne tworzenie punktów kontrolnych, które odbywa się na `await` lub `yield` wywołanie na `Task.WhenAll` lub `context.df.Task.all` gwarantuje, że wszelkie midway awarii lub ponownego uruchomienia przy użyciu nie wymaga ponownego uruchomienia wszelkich już wykonane zadania.

## <a name="pattern-3-async-http-apis"></a>Wzorzec #3: Interfejsy API protokołu HTTP Async

Trzeci wzorzec jest problem koordynacji stanu operacji długotrwałych z klientami zewnętrznymi. Typowa implementacja tego wzorca jest ustawienie akcji długotrwałych wyzwalane przez wywołanie HTTP przez usługę i następnie przekierowywania klientów do stanu punktu końcowego, który one sondowania, aby dowiedzieć się więcej, po zakończeniu operacji.

![Diagram interfejsu API protokołu HTTP](./media/durable-functions-overview/async-http-api.png)

Trwałe funkcje udostępnia wbudowane interfejsy API, które upraszczają kod, który zapisu do interakcji z długim wykonań funkcji. Quickstart — przykłady ([C#](durable-functions-create-first-csharp.md), [JavaScript](quickstart-js-vscode.md)) Pokaż prostego polecenia REST, który może służyć do uruchomienia nowych wystąpień funkcji programu orchestrator. Po uruchomieniu wystąpienia rozszerzenie udostępnia interfejsy API protokołu HTTP to zapytanie stanu funkcji programu orchestrator elementów webhook. Poniższy przykład pokazuje poleceniami REST, aby uruchomić koordynatora, a także aby zbadać jego stan. Dla jasności pominięto niektóre szczegóły z przykładu.

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

Ponieważ stan jest zarządzany przez środowisko uruchomieniowe usługi Functions trwałe, nie trzeba zaimplementować swój własny status mechanizm śledzenia.

Mimo że rozszerzenia funkcji trwałych zawiera wbudowane elementy webhook dla zarządzania długotrwałych aranżacji, można zaimplementować ten wzorzec samodzielnie przy użyciu własnych wyzwalacze funkcji (np. HTTP, kolejki lub Centrum zdarzeń) oraz `orchestrationClient` powiązania. Na przykład można użyć komunikatu w kolejce do wyzwolenia zakończenia.  Można także użyć wyzwalacza HTTP, który jest chroniony przez zasady uwierzytelniania usługi Azure Active Directory zamiast wbudowane elementy webhook, które wygenerowany klucz na użytek uwierzytelniania.

### <a name="c"></a>C#

```cs
// HTTP-triggered function to start a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function name comes from the request URL.
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```javascript
// HTTP-triggered function to start a new orchestrator function instance.
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);

    // Function name comes from the request URL.
    // Function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    return client.createCheckStatusResponse(req, instanceId);
};
```

> [!WARNING]
> Wdrażając aplikacje lokalnie w języku JavaScript, musisz ustawić zmienną środowiskową `WEBSITE_HOSTNAME` do `localhost:<port>`, np. `localhost:7071` na korzystanie z metod `DurableOrchestrationClient`. Aby uzyskać więcej informacji na temat tego wymagania, zobacz [problem w usłudze GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

Na platformie .NET [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` parametr ma wartość z zakresu od `orchestrationClient` danych wyjściowych powiązania, który jest częścią rozszerzenia funkcji trwałych. W języku JavaScript, ten obiekt jest zwracany przez wywołanie metody `df.getClient(context)`. Te obiekty oferują metody rozruchu, wysyłanie zdarzeń, przerywanie i wykonuje zapytania dotyczące wystąpień funkcji nowego lub istniejącego programu orchestrator.

W poprzednim przykładzie przyjmuje HTTP funkcji wyzwalanej przez protokół- `functionName` wartość z adresu URL przychodzące i przekazuje tę wartość [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) powiązanie interfejsu API, następnie zwraca odpowiedź zawierającą `Location` nagłówek i dodatkowe informacje na temat wystąpienia, które później mogą być używane do wyszukiwania w górę stan uruchomiono wystąpienia lub zakończenie go.

## <a name="pattern-4-monitoring"></a>Wzorzec #4: Monitorowanie

Wzorzec monitora dotyczy elastyczne *cyklicznego* procesu w przepływie pracy — na przykład sondowania, dopóki nie zostaną spełnione określone warunki. Wyrażenie [wyzwalacza czasomierza](../functions-bindings-timer.md) można adres Prosty scenariusz, takie jak zadanie okresowe czyszczenie, ale interwale jest statyczna i zarządzanie okresy istnienia wystąpienia staje się złożone. Funkcje trwałe umożliwia elastyczny cykl w odstępach czasu, zarządzanie okresem istnienia zadania i możliwość tworzenia monitor wielu procesów z jednym aranżacji.

Przykładem będzie wycofywania wcześniej scenariusza async interfejsu API protokołu HTTP. Zamiast uwidaczniania punktu końcowego dla klienta zewnętrznego do monitorowania operacji długotrwałych, monitor długotrwałych wykorzystuje zewnętrzny punkt końcowy, oczekiwanie na niektóre zmiany stanu.

![Diagram monitora](./media/durable-functions-overview/monitor.png)

Za pomocą funkcji trwałe, wiele monitorów, w których stosuje się dowolne punkty końcowe można utworzyć w zaledwie kilku wierszach kodu. Monitory można zakończyć wykonywania, gdy jakiś warunek jest spełniony lub zostać zakończone przez [DurableOrchestrationClient](durable-functions-instance-management.md), oraz ich interwał oczekiwania można zmienić zależnie od jakiegoś warunku (czyli wykładniczego wycofywania.) Poniższy kod implementuje podstawowe monitora.

### <a name="c-script"></a>Skrypt języka C#

```cs
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
            // Perform action when condition met
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform further work here, or let the orchestration end
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform action when condition met
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform further work here, or let the orchestration end
});
```

Po odebraniu żądania tworzone jest nowe wystąpienie aranżacji w dla tego identyfikatora zadania. Wystąpienie sonduje stan, dopóki spełniony jest warunek i pętla zostanie zakończone. Trwałe czasomierza jest używane do kontrolowania interwału sondowania. Praca może być wtedy wykonywane lub zakończyć aranżacji. Gdy `context.CurrentUtcDateTime` (.NET) lub `context.df.currentUtcDateTime` (JavaScript) przekracza `expiryTime`, kończy się monitora.

## <a name="pattern-5-human-interaction"></a>Wzorzec #5: Interakcja z użytkownikami

Wiele procesów obejmują pewnego rodzaju z reakcji człowieka. Skomplikowanych kwestią dotyczącą obejmujące ludzie w zautomatyzowany proces jest, że osób nie zawsze są jako wysoko dostępna i działa prawidłowo w usłudze cloud services. Zautomatyzowane procesy muszą zezwalać na w tym i często w tym celu przy użyciu logiki przekroczeń limitu czasu i rekompensaty.

Przykład procesu biznesowego, który obejmuje z reakcji człowieka jest proces zatwierdzania. Na przykład może być wymagane dla raportu wydatków, który przekroczy pewien zatwierdzenia z Menedżera. Jeśli Menedżer nie zatwierdzenia w ciągu 72 godzin (być może wystąpił one na urlopie), proces eskalacji jest uruchamiane, aby uzyskać zatwierdzenie od kogoś innego (być może Menedżer manager).

![Interakcja z diagramu](./media/durable-functions-overview/approval.png)

Ten wzorzec można zaimplementować przy użyciu funkcji orkiestratora. Użyć koordynatora [trwałe czasomierza](durable-functions-timers.md) żądania zatwierdzenia i eskalacji w przypadku przekroczenia limitu czasu. Oczekiwania dla [zewnętrznego zdarzenia](durable-functions-external-events.md), którym będzie powiadomień generowanych przez niektóre z reakcji człowieka.

### <a name="c-script"></a>Skrypt języka C#

```cs
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

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```js
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

Trwałe czasomierza jest tworzony przez wywołanie `context.CreateTimer` (.NET) lub `context.df.createTimer`(JavaScript). W przypadku otrzymania powiadomienia przez `context.WaitForExternalEvent` (.NET) lub `context.df.waitForExternalEvent` (JavaScript). I `Task.WhenAny` (.NET) lub `context.df.Task.any` (JavaScript) jest wywoływana, aby zdecydować, czy do podwyższania poziomu (limit czasu jest wykonywane najpierw) lub przetwarzać zatwierdzenia (zatwierdzenia odebrane przed upływem limitu czasu).

Klienta zewnętrznego mogą dostarczać powiadomień o zdarzeniach za pomocą funkcji programu orchestrator oczekiwania [wbudowane interfejsy API protokołu HTTP](durable-functions-http-api.md#raise-event) lub za pomocą [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) interfejsu API inną funkcję:

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

## <a name="the-technology"></a>Technologia

W tle, rozszerzenia funkcji trwałych jest wbudowana w górnej części [trwałe Framework zadań](https://github.com/Azure/durabletask), biblioteki typu open source w serwisie GitHub do tworzenia aranżacji trwałe zadania. Znacznie takich jak jak usługi Azure Functions jest ewolucji bez użycia serwera usługi Azure WebJobs, funkcje trwałe to bezserwerowe ewolucji trwałe Framework zadania. Trwałe Framework zadania umożliwia intensywnie w firmie Microsoft i na zewnątrz także automatyzować procesy o kluczowym znaczeniu. Jest to naturalny wybór dla środowiska bez użycia serwera usługi Azure Functions.

### <a name="event-sourcing-checkpointing-and-replay"></a>Określanie źródła zdarzeń, punkt kontrolny i powtarzanie

Funkcje programu orchestrator w sposób niezawodny utrzymać ich stan wykonywania przy użyciu wzorca projektowego, znane jako [określania źródła zdarzeń](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing). Zamiast bezpośredniego przechowywania *bieżącego* stanu aranżacji, trwałe rozszerzenie używa magazynu tylko do dołączania do rejestrowania *pełnych szeregów działań* analizowaniem aranżacji funkcji. To ma wiele zalet, w tym poprawy wydajność, skalowalność i elastyczność w porównaniu do "zrzucanie" stan pełnego środowiska uruchomieniowego. Inne zalety, zapewniając spójność danych transakcyjnych i zachowaniu pełnego dziennika inspekcji i historii. Inspekcyjne, samodzielnie włączyć niezawodne akcje kompensacyjne.

Użycie określania źródła zdarzeń przez to rozszerzenie jest przezroczysty. Dzieje się w tle `await` (C#) lub `yield` operator (JavaScript) w funkcji programu orchestrator daje kontroli wątku orchestrator do dyspozytora trwałe Framework zadania. Dyspozytor następnie zatwierdza nowe akcje, które funkcja orkiestratora zaplanowane (takich jak wywoływanie jedną lub więcej funkcji podrzędnych lub planowania trwałe czasomierza) do magazynu. Ta akcja zatwierdzenia przezroczyste dołącza do *historię wykonywania* wystąpienia aranżacji. Historia jest przechowywana w tabeli magazynu. Następnie za pomocą akcji zatwierdzenia komunikaty są dodawane do kolejki, aby zaplanować faktyczną pracę. W tym momencie funkcja orkiestratora, może być zwolniony z pamięci. Karta dla niego zatrzymuje, jeśli jest używany Plan zużycie funkcji platformy Azure.  Po więcej pracy do wykonania funkcji jest uruchomiona ponownie oraz zostać odtworzone jest jego stan.

Gdy funkcja aranżacji podano więcej pracy do wykonania (na przykład odebraniu komunikatu odpowiedzi lub okresu działania czasomierza trwałość), orchestrator wznowi się ponownie i ponownie wykonuje całą funkcję od samego początku, aby można było odbudować stan lokalnego. Jeśli podczas tej powtarzania kod próbuje wywołać funkcję (lub inne async pracy), trwałe Framework zadań konsultować się z *historię wykonywania* bieżącego aranżacji. Jeśli stwierdzi, że [działania funkcji](durable-functions-types-features-overview.md#activity-functions) ma już wykonane i niektóre yielded wyniki, powoduje ponowne uruchomienie wynik tej funkcji i kod programu orchestrator będzie kontynuował działanie. Ten proces jest kontynuowany wykonywane, dopóki kod funkcji pobiera do punktu, w której zostanie zakończone lub ma on zaplanowanych nowych zadań asynchronicznych.

### <a name="orchestrator-code-constraints"></a>Ograniczenia kodu programu orchestrator

Zachowanie powtarzania tworzy ograniczenia dotyczące typu kodu, które mogą być zapisywane w funkcji programu orchestrator. Na przykład kod orchestrator musi być deterministyczna, zostaną odtworzone wiele razy, a musi mieć ten sam wynik każdorazowo. Pełna lista ograniczeń znajduje się w [ograniczenia kodu programu Orchestrator](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints) części **punkty kontrolne i ponowne uruchomienie** artykułu.

## <a name="language-support"></a>Obsługa języków

Obecnie C# (z funkcji wersji 1.x i 2.x) F# i języka JavaScript (funkcje 2.x tylko funkcje trwałe 1.7.0 lub nowszej) są tylko języki obsługiwane przez funkcje trwałe. Obejmuje to działanie i funkcje programu orchestrator. W przyszłości dodamy obsługę wszystkich języków, które obsługuje usługi Azure Functions. Zobacz usługi Azure Functions [listę problemów z repozytorium GitHub](https://github.com/Azure/azure-functions-durable-extension/issues) aby zobaczyć najnowszy stan naszych dodatkowy język, obsługi prac.

## <a name="monitoring-and-diagnostics"></a>Monitorowanie i diagnostyka

Rozszerzenia funkcji trwałych automatycznie emituje danych ze strukturą śledzenia [usługi Application Insights](../functions-monitoring.md) gdy aplikacja funkcji jest skonfigurowany przy użyciu klucza Instrumentacji usługi Application Insights. Te dane śledzenia może służyć do monitorowania zachowanie i postępu z mechanizmów.

Poniżej przedstawiono przykładowy wygląd funkcje trwałe śledzenia zdarzeń w portalu usługi Application Insights przy użyciu [analizy usługi Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-analytics):

![Wyniki zapytania programu App Insights](./media/durable-functions-overview/app-insights-1.png)

Istnieje mnóstwo przydatnych danych ze strukturą pakowane w `customDimensions` pole każdego wpisu dziennika. Oto przykład jeden taki zapis w pełni rozwinięta.

![pola tabeli customDimensions w zapytaniu usługi App Insights](./media/durable-functions-overview/app-insights-2.png)

Ze względu na zachowanie powtarzania dyspozytora trwałe Framework zadania może pojawić się wpisy dziennika nadmiarowe powtórzonym akcji. Może to być przydatne zrozumieć zachowanie powtarzania podstawowego aparatu. [Diagnostyki](durable-functions-diagnostics.md) artykule przedstawiono przykładowe zapytania, które odfiltrować powtarzania dzienniki, aby było widać tylko dzienniki "w czasie rzeczywistym".

## <a name="storage-and-scalability"></a>Magazyn i skalowalność

Rozszerzenia funkcji trwałych używa kolejek usługi Azure Storage, tabel i obiektów blob można utrwalić wykonywania historii stanu i wyzwalacza wykonywania funkcji. Domyślne konto magazynu dla aplikacji funkcji mogą być używane, lub można skonfigurować oddzielne konto magazynu. Możesz chcieć osobnego konta z powodu ograniczeń przepływności magazynu. Utworzony kod programu orchestrator nie musi i nie powinien wchodzić w interakcje przy użyciu jednostek w ramach tych kont magazynu. Jednostki są zarządzane bezpośrednio przez platformę, na który trwały zadania jako szczegółowo opisuje implementacja.

Funkcje programu orchestrator zaplanować działania funkcji i otrzymywać odpowiedzi za pomocą wewnętrznej kolejki komunikatów. Po uruchomieniu aplikacji funkcji w planie użycia funkcji platformy Azure, te kolejki są monitorowane przez [Azure funkcji skalowania kontrolera](../functions-scale.md#how-the-consumption-plan-works) i nowe obliczenie wystąpienia zostaną dodane zgodnie z potrzebami. Podczas skalowania do wewnątrz na wielu maszynach wirtualnych funkcję programu orchestrator mogą być uruchamiane w jednej maszyny Wirtualnej, natomiast wywoływanych funkcji działania są uruchamiane na kilka różnych maszyn wirtualnych. Więcej szczegółów można znaleźć na zachowanie skalowania funkcje trwałe w [wydajności i skali](durable-functions-perf-and-scale.md).

Magazyn tabel jest używany do przechowywania historii wykonywania dla kont usługi orchestrator. Zawsze, gdy wystąpienie rehydrates na konkretnej maszyny Wirtualnej, narzędzie pobiera informacje o historię jej wykonywania z usługi table storage, dzięki czemu można odtworzyć, jego stan lokalnego. Jedną z rzeczy wygodne o dostępnej w usłudze table storage historii jest można zapoznaj się z i wyświetlić historię z mechanizmów przy użyciu narzędzi takich jak [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).

Magazyn obiektów blob są używane przede wszystkim jako mechanizm dzierżawienia do koordynowania skalowanie wystąpień aranżacji na wielu maszynach wirtualnych. Służą one również do przechowywania danych dla dużych komunikatów, które nie mogą być przechowywane bezpośrednio tabele i kolejki.

![Zrzut ekranu Eksploratora usługi Storage platformy Azure](./media/durable-functions-overview/storage-explorer.png)

> [!WARNING]
> Mimo że jest łatwe i wygodne wyświetlić historię wykonywania w usłudze table storage, należy unikać wykonywania wszelkich zależności w tej tabeli. Go mogą ulec zmianie w miarę rozwoju rozszerzenia funkcji trwałych.

## <a name="known-issues-and-faq"></a>Znane problemy i często zadawane pytania

Powinny być śledzone wszystkie znane problemy występujące w [problemy usługi GitHub](https://github.com/Azure/azure-functions-durable-extension/issues) listy. W przypadku napotkania problemów i nie można odnaleźć problem w serwisie GitHub, otwórz nowy problem i zawierają szczegółowy opis problemu.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat funkcje trwałe, zobacz [Przegląd typy funkcji i funkcji dla funkcje trwałe (usługi Azure Functions)](durable-functions-types-features-overview.md), lub...

> [!div class="nextstepaction"]
> [Tworzenie pierwszej funkcji trwałych](durable-functions-create-first-csharp.md)