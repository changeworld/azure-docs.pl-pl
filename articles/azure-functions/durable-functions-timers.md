---
title: Czasomierze w funkcjach trwałe - Azure
description: Jak zaimplementować czasomierze trwałe w rozszerzeniu trwałe funkcji dla usługi Azure Functions.
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
ms.openlocfilehash: 4fd86b70965a7be84c72e265af798292819cbe96
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="timers-in-durable-functions-azure-functions"></a>Czasomierze w funkcji trwałe (funkcje platformy Azure)

[Funkcje trwałe](durable-functions-overview.md) zapewnia *trwałe czasomierze* do użytku w funkcjach programu orchestrator do zaimplementowania opóźnienia lub ustawienie przekroczeń limitu czasu na asynchronicznych akcji. Czasomierze trwałe powinien być używany w funkcji orchestrator zamiast `Thread.Sleep` i `Task.Delay` (C#) lub `setTimeout()` i `setInterval()` (JavaScript).

Utwórz trwałe czasomierza, wywołując [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) metody w [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html). Metoda zwraca klasę task, która zostanie wznowione od określonej daty i godziny.

## <a name="timer-limitations"></a>Ograniczenia czasomierza

Podczas tworzenia czasomierza wygasa o 4:30 będzie, podstawowej enqueues trwałe Framework zadań komunikat, który staje się tylko na 4:30 będzie widoczna. Podczas uruchamiania w planie użycia usługi Azure Functions, komunikatu czasomierza nowo widoczne będą upewnij się, że aplikacji funkcja pobiera aktywowana w odpowiedniej maszyny Wirtualnej.

> [!NOTE]
> * Czasomierze trwałe nie dłużej niż 7 dni, ze względu na ograniczenia w usłudze Azure Storage. Pracujemy nad [żądanie funkcji rozszerzania czasomierze innych niż 7 dni](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * Zawsze używaj [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) zamiast `DateTime.UtcNow` jak pokazano w przykładzie poniżej podczas obliczania względną terminu trwałe czasomierza.

## <a name="usage-for-delay"></a>Użycie opóźnienia

Poniższy przykład przedstawia sposób użycia trwałe czasomierze dla opóźnienie wykonywania. Przykład wysyła powiadomienie rozliczeń codziennie przez 10 dni.

#### <a name="c"></a>C#

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

#### <a name="javascript"></a>JavaScript

```js
const df = require("durable-functions");
const moment = require("moment-js");

module.exports = df(function*(context) {
    for (let i = 0; i < 10; i++) {
        const dayOfMonth = context.df.currentUtcDateTime.getDate();
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivityAsync("SendBillingEvent");
    }
});
```

> [!WARNING]
> Unikaj nieskończone pętle w funkcjach programu orchestrator. Uzyskać informacji o sposobie bezpiecznego i efektywnego wdrożenia scenariusze nieskończoną pętlę, zobacz [Eternal Orchestrations](durable-functions-eternal-orchestrations.md). 

## <a name="usage-for-timeout"></a>Użycie limitu czasu

W tym przykładzie przedstawiono metodę zastosowania trwałe czasomierze do zaimplementowania przekroczeń limitu czasu.

#### <a name="c"></a>C#

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

#### <a name="javascript"></a>JavaScript

```js
const df = require("durable-functions");
const moment = require("moment-js");

module.exports = df(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, 's');

    const activityTask = context.df.callActivityAsync("GetQuote");
    const timeoutTask = context.df.createTimer(deadline);

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
> Użyj `CancellationTokenSource` anulować czasomierza trwałe (C#) lub wywołanie `cancel()` w zwróconym `TimerTask` (JavaScript), jeśli kod nie będzie czekać na jej zakończenie. Trwałe Framework zadania nie zmieni aranżacji stan "ukończona", dopóki wszystkie oczekujące zadania zostały ukończone lub anulowane.

Ten mechanizm nie faktycznie kończy wykonywanie funkcja działania w toku. Zamiast po prostu umożliwia funkcja orchestrator zignorować wynik i przenieść na. Jeśli aplikacja funkcja korzysta z planu zużycia, możesz nadal będą naliczane czasu i pamięci używanej przez funkcję porzuconych działania. Domyślnie funkcje uruchomione w planie zużycie mają limit czasu równy pięć minut. Po przekroczeniu tego limitu hosta usługi Azure Functions odtworzeniem zatrzymuje wykonywanie wszystkich i zapobiec sytuację wyczerpanie rozliczeń. [Konfiguruje się limit czasu funkcji](functions-host-json.md#functiontimeout).

Na pełniejsze przykład sposób implementowania limitów czasu w funkcjach programu orchestrator, zobacz [człowieka & limity czasu - weryfikację telefoniczną](durable-functions-phone-verification.md) wskazówki.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak podnieść i obsługiwać zdarzenia zewnętrzne](durable-functions-external-events.md)

