---
title: Obsługa błędów w funkcjach trwałych — Azure
description: Dowiedz się, jak obsługiwać błędy w rozszerzeniu Funkcje trwałe dla usługi Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 447b3dcf5040835f5a853beff68bde794ece51f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277858"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Obsługa błędów w funkcjach trwałych (usługi Azure)

Aranżacji funkcji trwałe są implementowane w kodzie i można użyć wbudowanych funkcji obsługi błędów języka programowania. Naprawdę nie ma żadnych nowych pojęć, które musisz nauczyć się dodawać obsługę błędów i kompensacji do aranżacji. Istnieje jednak kilka zachowań, o których należy pamiętać.

## <a name="errors-in-activity-functions"></a>Błędy w funkcjach aktywności

Każdy wyjątek, który jest zgłaszany w funkcji działania jest organizowany z powrotem do funkcji koordynatora `FunctionFailedException`i generowane jako . Można napisać kod obsługi błędów i kompensacji, który odpowiada Twoim potrzebom w funkcji koordynatora.

Rozważmy na przykład następującą funkcję koordynatora, która przenosi środki z jednego konta na drugie:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = context.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

> [!NOTE]
> Poprzednie przykłady języka C# są dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy używać `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext`. Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```

---

Jeśli pierwsze wywołanie funkcji **CreditAccount** nie powiedzie się, funkcja koordynatora kompensuje, przypisując środki z powrotem do konta źródłowego.

## <a name="automatic-retry-on-failure"></a>Automatyczne ponawianie próby w przypadku awarii

Podczas wywoływania funkcji działania lub funkcji podaranżacji można określić zasadę automatycznego ponawiania prób. Poniższy przykład próbuje wywołać funkcję do trzech razy i czeka 5 sekund między każdym ponowić próbę:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await context.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> Poprzednie przykłady języka C# są dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy używać `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext`. Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

---

Wywołanie funkcji działania w poprzednim przykładzie przyjmuje parametr do konfigurowania zasad automatycznego ponawiania próby. Istnieje kilka opcji dostosowywania zasad automatycznego ponawiania próby:

* **Maksymalna liczba prób**: Maksymalna liczba prób ponawiania.
* **Pierwszy interwał ponawiania:** czas oczekiwania przed pierwszą próbą ponawiania.
* **Współczynnik wycofywania**: Współczynnik używany do określenia szybkości zwiększenia wycofywania. Wartość domyślna to 1.
* **Maksymalny interwał ponawiania próby:** maksymalny czas oczekiwania między próbami prób.
* **Ponów limit czasu:** maksymalna ilość czasu do spędzenia na wykonywaniu ponownych prób. Domyślnym zachowaniem jest ponowić próbę przez czas nieokreślony.
* **Dojście**: Wywołanie zwrotne zdefiniowane przez użytkownika można określić, aby określić, czy funkcja powinna zostać ponowiona.

## <a name="function-timeouts"></a>Limity czasu funkcji

Można porzucić wywołanie funkcji w ramach funkcji koordynatora, jeśli trwa zbyt długo, aby zakończyć. Właściwym sposobem, aby to zrobić dzisiaj jest utworzenie `context.CreateTimer` [trwałego timera](durable-functions-timers.md) przy użyciu (.NET) lub `context.df.Task.any` `context.df.createTimer` (JavaScript) w połączeniu z `Task.WhenAny` (.NET) lub (JavaScript), jak w poniższym przykładzie:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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
> Poprzednie przykłady języka C# są dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy używać `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext`. Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
        // timeout case
        return false;
    }
});
```

---

> [!NOTE]
> Ten mechanizm faktycznie nie kończy wykonywania funkcji działania w toku. Przeciwnie, po prostu pozwala funkcji orkiestratora zignorować wynik i przejść dalej. Aby uzyskać więcej informacji, zobacz dokumentację [czasomierzy.](durable-functions-timers.md#usage-for-timeout)

## <a name="unhandled-exceptions"></a>Nieobsługiwane wyjątki

Jeśli funkcja koordynatora nie powiedzie się z nieobsługiwał wyjątek, szczegóły `Failed` wyjątku są rejestrowane i wystąpienie kończy się stan.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wiecznych aranżacjach](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Dowiedz się, jak diagnozować problemy](durable-functions-diagnostics.md)
