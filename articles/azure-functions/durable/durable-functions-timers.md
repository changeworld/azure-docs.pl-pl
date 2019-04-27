---
title: Czasomierze w funkcje trwałe - Azure
description: Dowiedz się, jak wdrożyć czasomierzy trwałe w rozszerzenia funkcji trwałych dla usługi Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: azfuncdf
ms.openlocfilehash: a05f75a7e38ee7cd4dc056629d9acaacad875e08
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730229"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Czasomierze w funkcje trwałe (usługa Azure Functions)

[Trwałe funkcje](durable-functions-overview.md) zapewnia *trwałe czasomierzy* do użytku w funkcjach programu orchestrator do zaimplementowania opóźnienia lub skonfigurować przekroczeń limitu czasu na operacje asynchroniczne. Czasomierze trwałe powinien być używany w funkcji programu orchestrator, zamiast `Thread.Sleep` i `Task.Delay` (C#) lub `setTimeout()` i `setInterval()` (JavaScript).

Tworzenie trwałych czasomierza, wywołując [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) metody [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) na platformie .NET, lub `createTimer` metody `DurableOrchestrationContext` w języku JavaScript. Metoda zwraca klasę task, która zostanie wznowione od określonej daty i godziny.

## <a name="timer-limitations"></a>Ograniczenia czasomierza

Podczas tworzenia czasomierza, która wygaśnie po o 4:30 pm, podstawowej umieszczeniu trwałe Framework zadań wiadomość, która staje się widoczna tylko na 4:30 pm. Podczas pracy w planie zużycie usługi Azure Functions, komunikat widoczny nowo czasomierza będzie upewnić się, że aplikacji funkcji pobiera aktywowana na odpowiednią maszynę Wirtualną.

> [!NOTE]
> * Czasomierze trwałe nie wystarczą na dłużej, niż 7 dni, ze względu na ograniczenia w usłudze Azure Storage. Firma Microsoft pracuje nad [żądanie funkcji, aby rozszerzyć czasomierzy dłużej niż 7 dni](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * Zawsze używaj [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) zamiast `DateTime.UtcNow` na platformie .NET i `currentUtcDateTime` zamiast `Date.now` lub `Date.UTC` w języku JavaScript, jak pokazano na poniższych przykładów, podczas obliczania względne ostatecznym terminem przypadającym trwałe czasomierza .

## <a name="usage-for-delay"></a>Użycie dla opóźnienia

Poniższy przykład ilustruje sposób używania trwałe czasomierzy do opóźniania wykonania. Przykład wysyła powiadomienie rozliczeń codziennie przez dziesięć dni.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

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
> Należy unikać pętli nieskończonej w funkcjach programu orchestrator. Aby dowiedzieć się, jak bezpiecznie i wydajnie Implementowanie scenariuszy nieskończoną pętlę, zobacz [Orkiestracje](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Użycie limitu czasu

Ten przykład ilustruje sposób używania trwałe czasomierzy do zaimplementowania przekroczeń limitu czasu.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

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
> Użyj `CancellationTokenSource` do anulowania trwałe czasomierza (C#) lub wywołania `cancel()` na zwracanego `TimerTask` (JavaScript), jeśli kod nie będzie czekać na jego zakończenie. Trwałe Framework zadania nie ulegnie zmianie aranżacji stan "ukończone", dopóki wszystkie oczekujące zadania zostały ukończone lub anulowane.

Ten mechanizm faktycznie kończy działanie w trakcie wykonywania funkcji. Przeciwnie po prostu umożliwia funkcję programu orchestrator, aby zignorować wynik i przejść. Jeśli aplikacja funkcji korzysta z planu zużycie, będą nadal opłata dowolnej godzinie i pamięci używane przez funkcję opuszczoną działania. Domyślnie funkcje działające w ramach planu zużycie mają limit czasu równy pięć minut. W przypadku przekroczenia tego limitu hosta usługi Azure Functions jest przetworzony ponownie, aby zatrzymać wykonywanie wszystkich i zapobiec braków sytuacji rozliczeń. [Limit czasu funkcji jest konfigurowany](../functions-host-json.md#functiontimeout).

Więcej informacji na temat przykład implementacji przekroczeń limitu czasu w funkcjach programu orchestrator, zobacz [z reakcji człowieka i limity czasu — Weryfikacja telefonu](durable-functions-phone-verification.md) wskazówki.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak podnieść i obsługiwać zdarzenia zewnętrzne](durable-functions-external-events.md)
