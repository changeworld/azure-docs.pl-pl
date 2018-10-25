---
title: Obsługa błędów w funkcje trwałe - Azure
description: Informacje o sposobie obsługi błędów w rozszerzenia funkcji trwałych dla usługi Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: azfuncdf
ms.openlocfilehash: 61496d91c9ec2cd1dcf498df04d2dab6629e009c
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49984132"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Obsługa błędów w funkcje trwałe (usługi Azure Functions)

Trwałe aranżacji funkcji są implementowane w kodzie i skorzystać z możliwości obsługi błędów języka programowania. Pamiętając o tym tak naprawdę nie ma żadnych nowych pojęć, czego potrzebujesz, aby dowiedzieć się, gdy opakowując swoje mechanizmów obsługi błędów i rekompensaty. Istnieją jednak kilka zachowań, które trzeba wiedzieć.

## <a name="errors-in-activity-functions"></a>Błędy w funkcji działań

Każdy wyjątek, który jest generowany w funkcji działania jest skierowany do funkcji programu orchestrator i zgłoszony jako `FunctionFailedException`. Można napisać kod obsługi i Kompensacja błędu, który odpowiada Twoim potrzebom w funkcji programu orchestrator.

Na przykład rozważmy następującą funkcję programu orchestrator, który przenosi środków z jednego konta:

#### <a name="c"></a>C#

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(DurableOrchestrationContext context)
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

#### <a name="javascript-functions-v2-only"></a>JavaScript (tylko funkcje v2)

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

Jeśli wywołanie **CreditAccount** funkcja kończy się niepowodzeniem dla konta docelowego, funkcja orkiestratora kompensuje to przez dobro środków do konta źródłowego.

## <a name="automatic-retry-on-failure"></a>Automatyczne ponawiania prób w przypadku niepowodzenia

Po wywołaniu aranżacji podrzędne lub działanie funkcji, można określić zasady automatycznego ponawiania. Poniższy przykład próbuje wywołać funkcję maksymalnie trzy razy i czeka na 5 sekund między kolejnymi próbami:

#### <a name="c"></a>C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);
    
    // ...
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (tylko funkcje v2)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const retryOptions = new df.RetryOptions(5000, 3);
    
    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

`CallActivityWithRetryAsync` (C#) Lub `callActivityWithRetry` przyjmuje interfejsu API (JS) `RetryOptions` parametru. Suborchestration wywołań przy użyciu `CallSubOrchestratorWithRetryAsync` (C#) lub `callSubOrchestratorWithRetry` (JS) interfejsu API można używać tych tych samych zasad ponawiania prób.

Dostępnych jest kilka opcji dostosowywania zasady automatycznego ponawiania. Te kraje to:

* **Maksymalna liczba prób**: maksymalną liczbę ponownych prób.
* **Pierwszy interwał ponawiania próby**: ilość czasu oczekiwania przed pierwszym ponowieniem próby.
* **Współczynnik wycofywania**: współczynnik używany do określenia tempo wzrostu wycofywania. Wartość domyślna to 1.
* **Maksymalny interwał ponawiania**: maksymalną ilość czasu oczekiwania między ponownymi próbami.
* **Limitu czasu ponawiania próby**: maksymalną ilość czasu, które możesz wydać to ponawia próbę. Domyślnym zachowaniem jest aby ponowić próbę przez czas nieokreślony.
* **Obsługa**: można określić wywołanie zwrotne użytkownika określa, czy należy wykonać ponownie wywołania funkcji.

## <a name="function-timeouts"></a>Limity czasu — funkcja

Możesz chcieć abandon wywołania funkcji w ramach funkcji orkiestratora, jeśli trwa zbyt długo. Właściwy sposób, w tym celu już dziś jest utworzenie [trwałe czasomierza](durable-functions-timers.md) przy użyciu `context.CreateTimer` w połączeniu z `Task.WhenAny`, jak w poniższym przykładzie:

#### <a name="c"></a>C#

```csharp
public static async Task<bool> Run(DurableOrchestrationContext context)
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

#### <a name="javascript-functions-v2-only"></a>JavaScript (tylko funkcje v2)

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
> Ten mechanizm faktycznie kończy działanie w trakcie wykonywania funkcji. Przeciwnie po prostu umożliwia funkcję programu orchestrator, aby zignorować wynik i przejść. Aby uzyskać więcej informacji, zobacz [czasomierzy](durable-functions-timers.md#usage-for-timeout) dokumentacji.

## <a name="unhandled-exceptions"></a>Nieobsługiwane wyjątki

Jeśli funkcja orkiestratora zakończy się niepowodzeniem z powodu nieobsługiwanego wyjątku, szczegóły wyjątku są rejestrowane i zakończy się wystąpieniem `Failed` stanu.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak diagnozować problemy](durable-functions-diagnostics.md)
