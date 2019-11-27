---
title: Obsługa błędów w Durable Functions — Azure
description: Dowiedz się, jak obsłużyć błędy w rozszerzeniu Durable Functions Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 0900e3f3b76f4a82e06fe3c0e6d9bbe63b545f56
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231419"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Obsługa błędów w Durable Functions (Azure Functions)

Trwałe aranżacje funkcji są implementowane w kodzie i mogą korzystać z wbudowanych funkcji obsługi błędów w języku programowania. Nie ma żadnych nowych koncepcji, które należy poznać, aby dowiedzieć się, jak dodać obsługę błędów i kompensację do swoich aranżacji. Istnieje jednak kilka zachowań, z którymi należy się zapoznać.

## <a name="errors-in-activity-functions"></a>Błędy w funkcjach działania

Każdy wyjątek, który jest generowany w funkcji działania, zostaje zorganizowany z powrotem do funkcji programu Orchestrator i zgłaszany jako `FunctionFailedException`. Można napisać obsługę błędów i kod kompensaty, który odpowiada Twoim potrzebom w funkcji programu Orchestrator.

Rozważmy na przykład następujące funkcje programu Orchestrator, które przesyłają fundusze z jednego konta do innego:

### <a name="precompiled-c"></a>PrekompilowanegoC#

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

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

### <a name="c-script"></a>C#Napisy

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(IDurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

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
> Poprzednie C# przykłady dotyczą Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext`. Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

### <a name="javascript-functions-20-only"></a>JavaScript (tylko funkcje 2,0)

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

Jeśli pierwsze wywołanie funkcji **CreditAccount** zakończy się niepowodzeniem, funkcja programu Orchestrator będzie kompensować środki z powrotem do konta źródłowego.

## <a name="automatic-retry-on-failure"></a>Automatyczne ponawianie próby w przypadku niepowodzenia

Podczas wywoływania funkcji działania lub funkcji organizowania podrzędnego można określić zasady automatycznego ponawiania. Poniższy przykład próbuje wywołać funkcję maksymalnie trzy razy i czeka 5 sekund między kolejnymi próbami:

### <a name="precompiled-c"></a>PrekompilowanegoC#

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

### <a name="c-script"></a>C#Napisy

```csharp
public static async Task Run(IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> Poprzednie C# przykłady dotyczą Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext`. Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

### <a name="javascript-functions-20-only"></a>JavaScript (tylko funkcje 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const retryOptions = new df.RetryOptions(5000, 3);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

Interfejs API `CallActivityWithRetryAsync` (.NET) lub `callActivityWithRetry` (JavaScript) przyjmuje parametr `RetryOptions`. Wywołania aranżacji podrzędnej przy użyciu interfejsu API `CallSubOrchestratorWithRetryAsync` (.NET) lub `callSubOrchestratorWithRetry` (JavaScript) mogą używać tych samych zasad ponawiania.

Istnieje kilka opcji dostosowywania zasad automatycznego ponawiania:

* **Maksymalna liczba prób**: Maksymalna liczba ponownych prób.
* **Interwał pierwszego ponowienia próby**: ilość czasu oczekiwania przed pierwszym ponowieniem próby.
* **Współczynnik wycofywania**: współczynnik używany do określenia współczynnika wzrostu wycofywania. Wartość domyślna to 1.
* **Maksymalna długość interwału ponawiania**: Maksymalna ilość czasu oczekiwania między ponownymi próbami.
* **Limit czasu ponawiania**: maksymalny czas poświęcany na wykonanie ponownych prób. Domyślne zachowanie polega na ponownym ponowieniu próby.
* **Dojście**: zdefiniowane przez użytkownika wywołanie zwrotne można określić, aby określić, czy należy ponowić próbę wykonania funkcji.

## <a name="function-timeouts"></a>Limity czasu funkcji

Może zajść potrzeba porzucenia wywołania funkcji w ramach funkcji programu Orchestrator, jeśli trwa zbyt długo. Aby to zrobić, należy utworzyć [trwały czasomierz](durable-functions-timers.md) przy użyciu `context.CreateTimer` (.NET) lub `context.df.createTimer` (JavaScript) w połączeniu z `Task.WhenAny` (.NET) lub `context.df.Task.any` (JavaScript), jak w poniższym przykładzie:

### <a name="precompiled-c"></a>PrekompilowanegoC#

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

### <a name="c-script"></a>C#Napisy

```csharp
public static async Task<bool> Run(IDurableOrchestrationContext context)
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
> Poprzednie C# przykłady dotyczą Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext`. Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

### <a name="javascript-functions-20-only"></a>JavaScript (tylko funkcje 2,0)

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

> [!NOTE]
> Ten mechanizm nie kończy w rzeczywistości wykonywania funkcji działania w toku. Zamiast tego po prostu umożliwia funkcji programu Orchestrator ignorowanie wyniku i przejście. Aby uzyskać więcej informacji, zobacz dokumentację [czasomierzy](durable-functions-timers.md#usage-for-timeout) .

## <a name="unhandled-exceptions"></a>Nieobsługiwane wyjątki

Jeśli funkcja programu Orchestrator zakończy się niepowodzeniem z powodu nieobsługiwanego wyjątku, szczegóły wyjątku zostaną zarejestrowane, a wystąpienie zostanie ukończone ze stanem `Failed`.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o aranżacjach Eternal](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Dowiedz się, jak zdiagnozować problemy](durable-functions-diagnostics.md)
