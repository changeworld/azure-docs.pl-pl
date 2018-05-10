---
title: Omówienie funkcji trwałe - Azure
description: Wprowadzenie do funkcji trwałe rozszerzenie dla usługi Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/30/2018
ms.author: azfuncdf
ms.openlocfilehash: d253562e0ecb0d53739a4cdc5f9747e33d7e1171
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="durable-functions-overview"></a>Trwałe Functions — omówienie

*Funkcje trwałe* jest rozszerzeniem [usługi Azure Functions](functions-overview.md) i [zadań Webjob Azure](../app-service/web-sites-create-web-jobs.md) , która umożliwia pisanie stanowe funkcji w środowisku bez serwera. Rozszerzenie zarządza stanu, punkty kontrolne i ponownego uruchomienia dla Ciebie.

Rozszerzenie pozwala zdefiniować stanowe przepływów pracy w nowym typem funkcji o nazwie *funkcja orchestrator*. Oto niektóre zalety funkcji programu orchestrator:

* Określają one przepływów pracy w kodzie. Niezbędne są nie projektantów lub schematów JSON.
* Można wywołują inne funkcje synchronicznego i asynchronicznego. Dane wyjściowe funkcji o nazwie może zostać zapisany do zmiennych lokalnych.
* One automatycznie punktu kontrolnego postępów zawsze, gdy funkcja oczekujące na. Stan lokalnego nigdy nie zostaną utracone, jeśli odtwarzania procesu lub ponownego uruchamiania maszyny Wirtualnej.

> [!NOTE]
> Trwałe funkcje to zaawansowane rozszerzenie dla usługi Azure Functions, który nie jest odpowiedni dla wszystkich aplikacji. Dalszej części tego artykułu założono, że silne znajomość [usługi Azure Functions](functions-overview.md) pojęcia i wyzwania zaangażowane w tworzenie aplikacji bez serwera.

Pierwotnym zastosowaniem funkcji trwałe upraszcza koordynacji złożonych, stanowe problemów w aplikacjach niekorzystającą. W poniższych sekcjach opisano niektóre wzorce typowych aplikacji, które mogą korzystać z funkcji trwałe.

## <a name="pattern-1-function-chaining"></a>Wzorca #1: Funkcja tworzenie łańcucha

*Funkcja łańcucha* odwołuje się do wzorca wykonywanych kolejności funkcji w określonej kolejności. Często dane wyjściowe jedną funkcję musi zostać zastosowana do innej funkcji danych wejściowych.

![Diagram CBC — funkcja](media/durable-functions-overview/function-chaining.png)

Funkcje trwałe umożliwia zwięzłym implementacji tego wzorca w kodzie.

#### <a name="c"></a>C#

```cs
public static async Task<object> Run(DurableOrchestrationContext ctx)
{
    try
    {
        var x = await ctx.CallActivityAsync<object>("F1");
        var y = await ctx.CallActivityAsync<object>("F2", x);
        var z = await ctx.CallActivityAsync<object>("F3", y);
        return  await ctx.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // error handling/compensation goes here
    }
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (tylko funkcje v2)

```js
const df = require("durable-functions");

module.exports = df(function*(ctx) {
    const x = yield ctx.df.callActivityAsync("F1");
    const y = yield ctx.df.callActivityAsync("F2", x);
    const z = yield ctx.df.callActivityAsync("F3", y);
    return yield ctx.df.callActivityAsync("F4", z);
});
```

Wartości "F1", "F2", "F3" i "F4" są nazwami innych funkcji w funkcji aplikacji. Przepływ sterowania zaimplementowano normalnego użycia konstrukcji kodowania. Oznacza to, że kod wykonuje góra dół i może obejmować istniejących semantykę przepływu sterowania języka, takich jak warunków i pętli.  Błąd podczas obsługi logiki mogą zostać włączone w blokach try/catch/finally.

`ctx` Parametr ([DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html)) udostępnia metody wywoływania innych funkcji według nazw, przekazywanie parametrów i zwraca dane wyjściowe funkcji. Po każdej aktualizacji kod wywołuje `await`, framework trwałe funkcji *punktów kontrolnych* postęp bieżące wystąpienie funkcji. Jeśli proces lub maszyna wirtualna jest odtwarzany połowie przez wykonanie, wznawiając wystąpienie funkcji poprzedniej `await` wywołania. Więcej informacji na temat to zachowanie ponownego uruchamiania później.

## <a name="pattern-2-fan-outfan-in"></a>Wzorzec #2: Fan-wyjściowego/fan-w

*Fan-wyjściowego/fan-w* odwołuje się do wzorca wykonywanie wielu funkcji równolegle, a następnie oczekiwanie na wszystkie, aby zakończyć.  Często niektóre agregacji jest wykonać wyniki zwrócone z funkcji.

![Diagram Fan-wyjściowego/fan-w](media/durable-functions-overview/fan-out-fan-in.png)

Normalne funkcje wentylujące jest możliwe dzięki użyciu funkcji wysyłania wielu wiadomości do kolejki. Jednak wentylujące w jest znacznie trudniejsze. Trzeba napisać kod, aby sprawdzić, kiedy funkcji wyzwalanych przez kolejki zakończyć i przechowywać dane wyjściowe funkcji. Rozszerzenie funkcji trwałe obsługuje tego wzorca z kodem stosunkowo proste.

#### <a name="c"></a>C#

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    var parallelTasks = new List<Task<int>>();
 
    // get a list of N work items to process in parallel
    object[] workBatch = await ctx.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = ctx.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }
 
    await Task.WhenAll(parallelTasks);
 
    // aggregate all N outputs and send result to F3
    int sum = parallelTasks.Sum(t => t.Result);
    await ctx.CallActivityAsync("F3", sum);
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (tylko funkcje v2)

```js
const df = require("durable-functions");

module.exports = df(function*(ctx) {
    const parallelTasks = [];

    // get a list of N work items to process in parallel
    const workBatch = yield ctx.df.callActivityAsync("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(ctx.df.callActivityAsync("F2", workBatch[i]));
    }

    yield ctx.df.task.all(parallelTasks);

    // aggregate all N outputs and send result to F3
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield ctx.df.callActivityAsync("F3", sum);
});
```

Praca rozdysponowywania jest dystrybuowany do wielu wystąpień funkcji `F2`, i pracy są śledzone za pomocą dynamicznej listy zadań. .NET `Task.WhenAll` interfejsu API nosi nazwę oczekiwania dla wszystkich funkcji o nazwie, aby zakończyć. Następnie przy użyciu `F2`funkcja przekazuje są agregowane z listy zadań dynamiczne i przekazane do `F3` funkcji.

Automatyczne tworzenie punktów kontrolnych, które odbywa się na `await` wywołać `Task.WhenAll` gwarantuje, że wszelkie połowie awarię lub ponowne uruchomienie komputera przez nie wymaga ponownego uruchomienia wszelkich ukończenia zadania.

## <a name="pattern-3-async-http-apis"></a>Wzorzec #3: Interfejsy API protokołu HTTP Async

Trzeci wzorzec wszystkim chodzi o problem koordynacji stanu długotrwałej operacji z klientami zewnętrznymi. Typowym sposobem wykonania tego wzorca jest wprowadzenie akcji długotrwałe wyzwalane przez wywołanie HTTP, a następnie przekierowywania klientów do stanu punktu końcowego, który można ich sondować Aby dowiedzieć się po zakończeniu operacji.

![Diagram HTTP interfejsu API](media/durable-functions-overview/async-http-api.png)

Funkcje trwałe zawiera wbudowane interfejsów API, które uprościć kod napisany interakcji z wykonaniami funkcja długotrwałe. [Przykłady](durable-functions-install.md) Pokaż prostego polecenia REST, który może służyć do Uruchom nowe wystąpienia funkcji programu orchestrator. Po rozpoczęciu wystąpienia rozszerzenia ujawnia elementu webhook HTTP interfejsów API kwerendy stanu funkcji programu orchestrator. W poniższym przykładzie przedstawiono polecenia REST, aby uruchomić orchestrator i aby zbadać jego stan. Dla uzyskania przejrzystości niektóre szczegóły zostały pominięte w przykładzie.

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

Ponieważ stan jest zarządzana przez środowisko uruchomieniowe trwałe funkcji, nie trzeba zaimplementować własne śledzenie mechanizmu stanu.

Nawet jeśli rozszerzenie funkcji trwałe ma wbudowanych elementów webhook zarządzania orchestrations długotrwałe, można wdrożyć tego wzorca samodzielnie przy użyciu wyzwalaczy własnych funkcji (np. HTTP, kolejki lub Centrum zdarzeń) oraz `orchestrationClient` powiązania. Na przykład można użyć komunikatu w kolejce do wyzwolenia zakończenia.  Można także użyć wyzwalacza HTTP chronione przez zasady uwierzytelniania usługi Azure Active Directory zamiast wbudowanych elementów webhook, że wygenerowany klucz na użytek uwierzytelniania. 

```cs
// HTTP-triggered function to start a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    TraceWriter log)
{
    // Function name comes from the request URL.
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);
    
    log.Info($"Started orchestration with ID = '{instanceId}'.");
    
    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` parametr ma wartość z zakresu od `orchestrationClient` output powiązania, który jest częścią rozszerzenie funkcji trwałe. Go udostępnia metody rozruchu, wysyłania zdarzeń, przerywanie i wykonywania zapytania dotyczącego orchestrator do nowego lub istniejącego wystąpienia funkcji. W poprzednim przykładzie HTTP wyzwalane — funkcja przyjmuje `functionName` wartość z przychodzącego adresu URL i przekazuje, które do wartości [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). Ten interfejs API powiązanie zwraca odpowiedź, która zawiera `Location` nagłówka i dodatkowe informacje o wystąpieniu, które później mogą być używane do wyszukiwania Konfigurowanie stan wystąpienia uruchomiony lub przerwać jego działanie.

## <a name="pattern-4-monitoring"></a>Wzorzec #4: monitorowania

Wzorzec monitor odwołuje się do elastycznych *cyklicznego* procesu w przepływie pracy — na przykład sondowania, dopóki nie zostaną spełnione określone warunki. Regularne wyzwalacza czasomierza można rozwiązać Prosty scenariusz, takie jak zadanie oczyszczania okresowe, ale jej interwał jest statyczna i zarządzanie okresy istnienia wystąpienia staje się złożonych. Trwałe funkcji umożliwia elastyczne cyklu interwałów, zarządzanie okresem istnienia zadań i możliwość tworzenia monitor wielu procesów z jednym aranżacji.

Przykład czy wycofywania wcześniejszych scenariusz HTTP API asynchronicznego. Zamiast udostępnianie punktu końcowego dla zewnętrznych klienta do monitorowania długotrwałej operacji, monitor długotrwałe zużywa zewnętrznego punktu końcowego, oczekiwanie na niektóre zmiany stanu.

![Diagram monitora](media/durable-functions-overview/monitor.png)

Przy użyciu funkcji trwałe, wiele monitorów, które obserwować dowolnego punkty końcowe można tworzyć w zaledwie kilku wierszach kodu. Monitory można zakończenie wykonywania, gdy niektóre warunek jest spełniony lub być został przerwany przez [DurableOrchestrationClient](durable-functions-instance-management.md), oraz ich interwału oczekiwania można zmienić na podstawie pod warunkiem, niektóre (tj. wykładniczego wycofywania.) Poniższy kod implementuje podstawowe monitora.

#### <a name="c"></a>C#

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    int jobId = ctx.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();
    
    while (ctx.CurrentUtcDateTime < expiryTime) 
    {
        var jobStatus = await ctx.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform action when condition met
            await ctx.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        var nextCheck = ctx.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await ctx.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform further work here, or let the orchestration end
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (tylko funkcje v2)

```js
const df = require("durable-functions");
const df = require("moment");

module.exports = df(function*(ctx) {
    const jobId = ctx.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(ctx.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield ctx.df.callActivityAsync("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform action when condition met
            yield ctx.df.callActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        const nextCheck = moment.utc(ctx.df.currentUtcDateTime).add(pollingInterval, 's');
        yield ctx.df.createTimer(nextCheck.toDate());
    }

    // Perform further work here, or let the orchestration end
});
```

Po odebraniu żądania nowego wystąpienia orchestration jest tworzona dla danego identyfikatora zadania. Wystąpienie sonduje stan, dopóki spełniony jest warunek i pętla zostanie zakończone. Trwałe czasomierza służy do kontrolowania interwału sondowania. Następnie można wykonać dalszej pracy lub orchestration można zakończyć. Gdy `ctx.CurrentUtcDateTime` przekracza `expiryTime`, zakończenia monitora.

## <a name="pattern-5-human-interaction"></a>Wzorzec #5: Człowieka

Wiele procesów obejmują określonego rodzaju człowieka. Zaletą trudnych obejmujące ludzi w zautomatyzowany proces jest czy osoby nie są zawsze jako wysoko dostępne i szybko reagowały jako usługi w chmurze. Musi umożliwić zautomatyzowanych procesów, a często w tym celu za pomocą reguł przekroczeń limitu czasu i kompensacji.

Przykładem procesów biznesowych, która obejmuje człowieka jest proces zatwierdzania. Na przykład zatwierdzenia z Menedżera może być wymagane dla raportu przekracza pewne. Jeśli Menedżer nie zatwierdza w 72 godziny (może być, który zakończył się one na urlopie), proces eskalacji jest uruchamiane uzyskanie zatwierdzenia od kogoś innego (możliwe, że Menedżer Menedżera).

![Diagram interakcji](media/durable-functions-overview/approval.png)

Ten wzorzec może być zaimplementowany przy użyciu funkcji programu orchestrator. Użyje orchestrator [trwałe czasomierza](durable-functions-timers.md) zażądać zatwierdzenia i przekazać w przypadku przekroczenia limitu czasu. Czy poczekać [zewnętrzne zdarzenie](durable-functions-external-events.md), byłoby powiadomień generowanych przez niektóre człowieka.

#### <a name="c"></a>C#

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    await ctx.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = ctx.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = ctx.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = ctx.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await ctx.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await ctx.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (tylko funkcje v2)

```js
const df = require("durable-functions");
const moment = require('moment');

module.exports = df(function*(ctx) {
    yield ctx.df.callActivityAsync("RequestApproval");

    const dueTime = moment.utc(ctx.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = ctx.df.createTimer(dueTime.toDate());

    const approvalEvent = ctx.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield ctx.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield ctx.df.callActivityAsync("ProcessApproval", approvalEvent.result);
    } else {
        yield ctx.df.callActivityAsync("Escalate");
    }
});
```

Trwałe czasomierz jest tworzony przez wywołanie metody `ctx.CreateTimer`. W przypadku otrzymania powiadomienia przez `ctx.WaitForExternalEvent`. I `Task.WhenAny` jest wywoływana, aby zdecydować, czy przekazać (limit czasu się stanie, najpierw) lub procesu zatwierdzania (zatwierdzenia odebrał przed upływem limitu czasu).

Klientom zewnętrznym zapewnia powiadomienie o zdarzeniu do oczekiwania funkcji programu orchestrator za pomocą [wbudowanych API HTTP](durable-functions-http-api.md#raise-event) lub za pomocą [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) interfejsu API z inną funkcję:

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

## <a name="the-technology"></a>Technologia

W tle rozszerzenie funkcji trwałe jest oparty na [trwałe Framework zadań](https://github.com/Azure/durabletask), biblioteki open source, w witrynie GitHub tworzenia orchestrations trwałe zadań. Znacznie podobnie jak jak usługi Azure Functions jest niekorzystającą rozwoju zadań Webjob Azure, trwałe funkcji jest niekorzystającą rozwoju trwałe Framework zadań. Trwałe Framework zadania umożliwia silnie w obrębie firmy Microsoft i poza również zautomatyzować procesy o krytycznym znaczeniu. Jest fizyczną Dopasuj niekorzystającą środowiska usługi Azure Functions.

### <a name="event-sourcing-checkpointing-and-replay"></a>Źródłem zdarzenia, punkty kontrolne i powtórzeń

Funkcje programu orchestrator w sposób niezawodny utrzymać ich stan wykonywania przy użyciu wzorca projektowego chmury, znany jako [źródłem zdarzeń](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing). Zamiast bezpośredniego przechowywania *bieżącego* stanu aranżacji trwałe rozszerzenie używa magazynu tylko do dołączania do rejestrowania *pełne serii akcji* wykonywaną przez aranżacji funkcji. Ma to wiele korzyści, w tym poprawę wydajności, skalowalności i elastyczność w porównaniu do stanu pełnego środowiska uruchomieniowego "zrzucanie". Inne zalety, zapewniając spójność ostateczna dla danych transakcyjnych i ślady pełne inspekcji i historii. Zapisy inspekcji, same zezwolenia na akcje kompensacyjnych wiarygodne.

Korzystanie z Sourcing zdarzenia przez to rozszerzenie jest przezroczysty. W obszarze obejmuje `await` operatora w funkcji orchestrator daje kontrolę nad wątku orchestrator do dyspozytora trwałe Framework zadań. Dyspozytor następnie zatwierdza nowe akcje, które funkcja orchestrator zaplanowane (np. wywoływanie jednego lub więcej funkcji podrzędnych lub planowania trwałe czasomierza) do magazynu. Ta akcja zatwierdzenia przezroczysty dołącza do *historii wykonywania* wystąpienia aranżacji. Historia są przechowywane w tabeli magazynu. Następnie akcji zatwierdzania dodaje wiadomości do kolejki, aby zaplanować rzeczywistą pracę. W tym momencie funkcja orchestrator może być usunięte z pamięci. Karta dla niego zatrzymuje, jeśli używasz zaplanować użycie funkcji Azure.  W przypadku więcej pracy funkcji zostanie ponownie uruchomiony i jego stan jest odtworzone.

Jeśli funkcja aranżacji uzyska więcej pracy (na przykład zostaje otrzymany komunikat odpowiedzi lub trwałe wygaśnięciem), orchestrator budzi się ponownie i ponownie wykonuje całą funkcję od początku, aby można było odbudować stanu lokalnego. Podczas powtarzania ten kod będzie próbować wywołać funkcję (lub inne async pracy), trwałe Framework zadań sprawdza z *historii wykonywania* z bieżącym aranżacji. Jeśli stwierdzi, że funkcja działanie zostało już wykonane zwróciło niektórych wyników, odtwarzaniem wyniku tej funkcji i kod orchestrator będzie kontynuował działanie. To jest nadal wykonywane, dopóki kod funkcji pobiera do punktu, gdzie zakończeniu albo ma zaplanowanych zadań asynchronicznych nowe.

### <a name="orchestrator-code-constraints"></a>Ograniczenia kodu programu orchestrator

Zachowanie powtarzania tworzy ograniczenia dotyczące typu kodu, które mogą być zapisywane w funkcji programu orchestrator. Na przykład kod orchestrator musi być deterministyczna, zostaną odtworzone wiele razy, a musi mieć takiego samego wyniku zawsze. Pełna lista ograniczeń znajduje się w [ograniczenia kodu Orchestrator](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints) sekcji **tworzenie punktów kontrolnych i ponownie uruchom** artykułu.

## <a name="language-support"></a>Obsługa języków

Obecnie C# (funkcje v1 i v2) i JavaScript (tylko funkcje v2) są tylko języki obsługiwane dla funkcji trwałe. W tym funkcje programu orchestrator i działania funkcji. W przyszłości dodamy obsługę wszystkich języków obsługiwanych przez usługę Azure Functions. Zobacz usługi Azure Functions [listę problemów repozytorium GitHub](https://github.com/Azure/azure-functions-durable-extension/issues) wyświetlić najnowszy stan naszej dodatkowy język obsługuje pracę.

## <a name="monitoring-and-diagnostics"></a>Monitorowanie i diagnostyka

Rozszerzenie funkcji trwałe automatycznie emituje danych strukturalnych śledzenia [usługi Application Insights](functions-monitoring.md) po skonfigurowaniu aplikacji funkcji przy użyciu klucza Instrumentacji usługi Application Insights. Te dane śledzenia może służyć do monitorowania zachowania i postęp Twojej orchestrations.

Oto przykład wyglądu trwałe funkcji śledzenia zdarzeń w portalu usługi Application Insights przy użyciu [Application Insights Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics):

![Wyniki zapytania App Insights](media/durable-functions-overview/app-insights-1.png)

Istnieje wiele przydatnych danych strukturalnych pakowane w `customDimensions` w każdego wpisu dziennika. Oto przykład jednego takiego wpisu w pełni rozwinięta.

![pole customDimensions w zapytaniu App Insights](media/durable-functions-overview/app-insights-2.png)

Ze względu na zachowanie powtarzania dyspozytora trwałe Framework zadań może spodziewać się wyświetlić wpisy dziennika nadmiarowe dla akcji powtórzony. Może to być przydatne do zrozumienie zachowania powtarzania aparatu core. [Diagnostyki](durable-functions-diagnostics.md) artykule przedstawiono przykładowe zapytania, które odfiltrowywania dzienniki powtarzania pozwala zobaczyć "w czasie rzeczywistym" dzienniki.

## <a name="storage-and-scalability"></a>Magazyn i skalowalność

Rozszerzenie funkcji trwałe używa usługi Azure Storage kolejek, tabel i obiektów blob do utrwalenia wykonywania historii stanu i wyzwalacza funkcji wykonywania. Domyślne konto magazynu dla funkcji aplikacji można używać, lub można skonfigurować oddzielnego konta magazynu. Możesz oddzielne konto z powodu ograniczenia przepływności magazynu. Kod orchestrator nie musi i nie powinien interakcji z obiektami w tych kont magazynu. Jednostki zarządzane bezpośrednio przez trwałe Framework zadań jako szczegóły implementacji.

Funkcje programu orchestrator harmonogram działania funkcji i odbierania odpowiedzi za pomocą wewnętrznej kolejki wiadomości. Po uruchomieniu aplikacji funkcji w programie Azure — użytek funkcje te kolejki są monitorowane przez [kontroler skali funkcji Azure](functions-scale.md#how-the-consumption-plan-works) i nowych zasobów obliczeniowych wystąpienia są dodawane w razie potrzeby. Skalowana w poziomie do wielu maszyn wirtualnych, funkcja orchestrator może działać na jednej maszynie Wirtualnej, natomiast wywoływanych funkcji działania są uruchamiane na kilka różnych maszyn wirtualnych. Więcej informacji można znaleźć na zachowanie skalowania trwałe funkcji [wydajności i skalowania](durable-functions-perf-and-scale.md).

Magazyn tabel jest używany do przechowywania historii wykonywania dla konta programu orchestrator. Zawsze, gdy wystąpienie rehydrates na określonej maszynie Wirtualnej, dzięki czemu można odtworzyć stanu lokalnego go pobiera jego historii wykonywania z magazynu tabel. Z wygodny czynności o możliwości dostępne w magazynie tabel historii jest można Spójrz i zobaczyć historię użytkownika orchestrations za pomocą narzędzi takich jak [Eksploratora usługi Microsoft Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Zrzut ekranu Eksploratora usługi Storage platformy Azure](media/durable-functions-overview/storage-explorer.png)

> [!WARNING]
> Jest łatwy i wygodny wyświetlić historię wykonywania w magazynie tabel, należy unikać wykonywania żadnych zależności w tej tabeli. Mogą ulec zmianie w miarę rozwoju środowisko rozszerzenie funkcji trwałe.

## <a name="known-issues-and-faq"></a>Znane problemy i często zadawane pytania

Powinny być śledzone wszystkie znane problemy w [problemów GitHub](https://github.com/Azure/azure-functions-durable-extension/issues) listy. Jeśli wystąpi problem i nie można odnaleźć problem w usłudze GitHub, otwórz nowy problem i zawiera szczegółowy opis problemu.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Kontynuuj czytanie dokumentacji trwałe funkcji](durable-functions-bindings.md)

> [!div class="nextstepaction"]
> [Zainstaluj rozszerzenie funkcji trwałe i przykłady](durable-functions-install.md)

