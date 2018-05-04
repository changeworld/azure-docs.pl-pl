---
title: Obsługa błędów w funkcjach trwałe - Azure
description: Informacje o sposobie obsługi błędów w rozszerzeniu trwałe funkcji dla usługi Azure Functions.
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
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 108c6020956b398effb8ba9dd4471190362359d6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Obsługa błędów w funkcjach trwałe (usługi Azure Functions)

Trwałe orchestrations funkcja zaimplementowano w kodzie i skorzystać z możliwości obsługi błędów języka programowania. Pamiętając o tym że naprawdę nie ma nowych pojęć, które należy poznać, gdy włączenie obsługi błędów i kompensacji orchestrations Twojego. Istnieje jednak kilka zachowania, które należy zwrócić uwagę.

## <a name="errors-in-activity-functions"></a>Błędy w funkcjach działania

Każdy wyjątek zgłaszany w funkcji działania jest skierowany do funkcji programu orchestrator i zgłoszony jako `FunctionFailedException`. Można napisać kod i obsługi kompensacji błąd, który odpowiada Twoim potrzebom w funkcji programu orchestrator.

Rozważmy na przykład następująca funkcja programu orchestrator, który przenosi funduszy z jednego konta:

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

Jeśli wywołanie **CreditAccount** funkcji nie powiedzie się dla docelowego konta, funkcja orchestrator istotne to kredytowanie funduszy konta źródła.

## <a name="automatic-retry-on-failure"></a>Automatyczne ponowienie dotyczące błędu

Podczas wywołania funkcji działania lub funkcji podrzędnych orchestration można określić automatyczne zasady ponawiania. Podejmuje próbę wywołania funkcji maksymalnie 3 razy, oczekuje 5 sekund między kolejnymi próbami w następującym przykładzie:

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

`CallActivityWithRetryAsync` Przyjmuje interfejsu API `RetryOptions` parametru. Wywołuje aranżacji podrzędnych za pomocą `CallSubOrchestratorWithRetryAsync` interfejs API może korzystać te same zasady ponawiania.

Dostępnych jest kilka opcji dostosowywania zasady automatycznego ponawiania. Te kraje to:

* **Maksymalna liczba prób**: Maksymalna liczba ponownych prób.
* **Pierwszy interwał ponawiania próby**: ilość czasu oczekiwania przed pierwszym ponawiania próby.
* **Współczynnik wycofywania**: współczynnik używany do określenia tempo wzrostu wycofywania. Wartość domyślna to 1.
* **Maksymalny interwał ponawiania prób**: maksymalną ilość czasu oczekiwania między ponownymi próbami.
* **Ponów limitu czasu**: maksymalną ilość czasu poświęcanego na wykonanie tej ponownych prób. Domyślnym zachowaniem jest aby ponowić próbę nieskończoność.
* **Niestandardowe**: można określić wywołanie zwrotne użytkownika określa, czy należy wykonać ponownie wywołania funkcji.

## <a name="function-timeouts"></a>Funkcja limitów czasu

Można porzucić wywołanie funkcji w funkcji programu orchestrator, jeśli to trwa za długo. Jest właściwy sposób, w tym celu dzisiaj, tworząc [trwałe czasomierza](durable-functions-timers.md) przy użyciu `context.CreateTimer` w połączeniu z `Task.WhenAny`, jak w poniższym przykładzie:

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

## <a name="unhandled-exceptions"></a>Nieobsługiwane wyjątki

Jeśli funkcja programu orchestrator nie powiedzie się z powodu nieobsługiwanego wyjątku, szczegóły wyjątku są rejestrowane i zakończeniu wystąpienia z `Failed` stanu.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak diagnozować problemy](durable-functions-diagnostics.md)
