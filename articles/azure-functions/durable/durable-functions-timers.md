---
title: Czasomierze w trwałych funkcjach — Azure
description: Dowiedz się, jak zaimplementować trwałe czasomierze w rozszerzeniu Funkcje trwałe dla usługi Azure Functions.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 0565cc149a36baf31d8516fffcf48b194c465760
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261487"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Czasomierze w funkcjach trwałych (funkcje platformy Azure)

[Funkcje trwałe](durable-functions-overview.md) zapewnia *trwałe czasomierze* do użytku w funkcjach orkiestratora do implementacji opóźnień lub konfigurowania limitów czasu na akcje asynchronicznej. Trwałe czasomierze powinny być używane `Thread.Sleep` w `Task.Delay` funkcjach aranżacji zamiast i (C#) lub `setTimeout()` `setInterval()` (JavaScript).

Czasomierz trwałego można `CreateTimer` utworzyć, wywołując metodę `createTimer` (.NET) lub metodę (JavaScript) [powiązania wyzwalacza aranżacji](durable-functions-bindings.md#orchestration-trigger). Metoda zwraca zadanie, które kończy się w określonym dniu i godzinie.

## <a name="timer-limitations"></a>Ograniczenia czasomierza

Po utworzeniu czasomierza, który wygasa o godzinie 16:30, podstawowa struktura trwałego zadania w kolejce wysyła komunikat, który staje się widoczny tylko o godzinie 16:30. Po uruchomieniu w planie użycia funkcji platformy Azure, nowo widoczny komunikat czasomierza zapewni, że aplikacja funkcji zostanie aktywowana na odpowiedniej maszynie wirtualnej.

> [!NOTE]
> * Trwałe czasomierze są obecnie ograniczone do 7 dni. Jeśli potrzebne są dłuższe opóźnienia, można je symulować przy `while` użyciu interfejsów API czasomierza w pętli.
> * Zawsze `CurrentUtcDateTime` używaj `DateTime.UtcNow` zamiast w `currentUtcDateTime` .NET `Date.now` lub `Date.UTC` zamiast lub w języku JavaScript podczas obliczania czasu ognia dla trwałych czasomierzy. Aby uzyskać więcej informacji, zobacz ograniczenie [kodu funkcji koordynatora.](durable-functions-code-constraints.md)

## <a name="usage-for-delay"></a>Użycie opóźnienia

Poniższy przykład ilustruje sposób używania czasomierzy trwałe do opóźnienia wykonania. W przykładzie jest wydawanie powiadomienia rozliczeniowego codziennie przez 10 dni.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> W poprzednim przykładzie języka C# jest przeznaczony dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy używać `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext`. Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

---

> [!WARNING]
> Unikaj nieskończonych pętli w funkcjach orkiestratora. Aby uzyskać informacje na temat bezpiecznego i wydajnego wdrażania scenariuszy nieskończonej pętli, zobacz [Eternal Orchestrations](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Użycie limitu czasu

W tym przykładzie pokazano, jak używać czasomierzy trwałe do zaimplementowania limitów czasu.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> W poprzednim przykładzie języka C# jest przeznaczony dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy używać `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext`. Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

> [!WARNING]
> Użyj `CancellationTokenSource` (.NET) lub `cancel()` wywołania `TimerTask` na zwrócony (JavaScript), aby anulować trwały czasomierz, jeśli kod nie będzie czekać na jego zakończenie. Struktura zadań trwałe nie zmieni stanu aranżacji na "ukończone", dopóki wszystkie zadania nie zostaną ukończone lub anulowane.

Ten mechanizm anulowania nie kończy funkcji działania w toku lub wykonania podaranżacji. Przeciwnie, po prostu pozwala funkcji orkiestratora zignorować wynik i przejść dalej. Jeśli aplikacja funkcji używa planu zużycie, nadal będą naliczane naliczane przez cały czas i pamięci używane przez funkcję porzuconej aktywności. Domyślnie funkcje uruchomione w planie zużycia mają limit czasu pięciu minut. Jeśli ten limit zostanie przekroczony, host usług Azure Functions jest odtworzenia, aby zatrzymać wszystkie wykonanie i zapobiec sytuacji rozliczeń ucieczki. Limit [czasu funkcji jest konfigurowalny](../functions-host-json.md#functiontimeout).

Aby uzyskać bardziej szczegółowy przykład sposobu implementowania limitów czasu w funkcjach programu orchestrator, zobacz [interakcja międzyludzkie & limity czasu — weryfikacja telefoniczna.](durable-functions-phone-verification.md)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak podnosić i obsługiwać zdarzenia zewnętrzne](durable-functions-external-events.md)
