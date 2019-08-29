---
title: Czasomierze w Durable Functions — Azure
description: Dowiedz się, jak zaimplementować trwałe czasomierze w rozszerzeniu Durable Functions dla Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: azfuncdf
ms.openlocfilehash: 11edfc11fc1e54684a99774c21517d4c322348b1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087041"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Czasomierze w Durable Functions (Azure Functions)

[Durable Functions](durable-functions-overview.md) zapewnia *trwałe czasomierze* do użycia w funkcjach programu Orchestrator do implementowania opóźnień lub skonfigurowania limitów czasu dla akcji asynchronicznych. Trwałe czasomierze powinny być używane w funkcjach programu `Thread.Sleep` Orchestrator `Task.Delay` zamiastC#i () `setTimeout()` , `setInterval()` lub i (JavaScript).

Aby utworzyć trwały czasomierz [](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) `createTimer` , należy wywołać metodę [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) w programie .NET lub metodę `DurableOrchestrationContext` języka JavaScript. Metoda zwraca zadanie, które wznawia działanie w określonym dniu i o określonej godzinie.

## <a name="timer-limitations"></a>Ograniczenia czasomierza

Podczas tworzenia czasomierza, który wygaśnie o 4:30 PM, podstawowa infrastruktura zadań trwałych enqueues komunikat, który zostanie widoczny tylko na 4:30 PM. Po uruchomieniu w planie zużycia Azure Functions, nowo widoczny komunikat czasomierza zapewni, że aplikacja funkcji zostanie aktywowana na odpowiedniej maszynie wirtualnej.

> [!NOTE]
> * Trwałe czasomierze nie mogą trwać dłużej niż 7 dni ze względu na ograniczenia w usłudze Azure Storage. Pracujemy nad [żądaniem funkcji, aby rozciągnąć czasomierze dłużej niż 7 dni](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * Zawsze używaj [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) zamiast `DateTime.UtcNow` programu `currentUtcDateTime` .NET i zamiast `Date.now`językaJavaScript , jak pokazano w poniższych przykładach podczas obliczania względnego terminu trwałego czasomierza. `Date.UTC`

## <a name="usage-for-delay"></a>Użycie opóźnienia

Poniższy przykład ilustruje sposób użycia trwałych czasomierzy do opóźnienia wykonywania. Przykład wysyła powiadomienie o rozliczeniach codziennie przez dziesięć dni.

### <a name="c"></a>C#

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const dayOfMonth = context.df.currentUtcDateTime.getDate();
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

> [!WARNING]
> Unikaj nieskończonych pętli w funkcjach programu Orchestrator. Aby dowiedzieć się, jak bezpiecznie i efektywnie zaimplementować nieskończone scenariusze pętli, zobacz [Eternal aranżacji](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Użycie limitu czasu

Ten przykład ilustruje sposób używania trwałych czasomierzy do implementowania limitów czasu.

### <a name="c"></a>C#

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

> [!WARNING]
> Użyj, `CancellationTokenSource` aby anulować trwały czasomierz (C#) lub wywoływać `cancel()` zwracaną `TimerTask` wartość (JavaScript), jeśli kod nie będzie oczekiwał na jego zakończenie. W przypadku trwałej struktury zadań nie zostanie zmieniony stan aranżacji na "ukończone", dopóki wszystkie zaległe zadania nie zostaną ukończone lub anulowane.

Ten mechanizm nie kończy w rzeczywistości wykonywania funkcji działania w toku. Zamiast tego po prostu umożliwia funkcji programu Orchestrator ignorowanie wyniku i przejście. Jeśli aplikacja funkcji korzysta z planu zużycia, opłaty są naliczane za dowolny czas i ilość pamięci zużytą przez zaniechaną funkcję działania. Domyślnie funkcje działające w ramach planu zużycia mają limit czasu równy pięć minut. W przypadku przekroczenia tego limitu Host Azure Functions zostanie odtworzony w celu zatrzymania całego wykonywania i uniemożliwienia rozliczenia. [Limit czasu funkcji można skonfigurować](../functions-host-json.md#functiontimeout).

Aby zapoznać się z bardziej szczegółowym przykładem implementacji limitów czasu w funkcjach programu Orchestrator, zobacz temat [interakcja ludzka & limity czasu — Przewodnik weryfikacji telefonu](durable-functions-phone-verification.md) .

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak podnieść i obsłużyć zdarzenia zewnętrzne](durable-functions-external-events.md)
