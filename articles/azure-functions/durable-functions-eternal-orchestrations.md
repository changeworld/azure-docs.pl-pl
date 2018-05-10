---
title: Eternal orchestrations w funkcjach trwałe - Azure
description: Dowiedz się, jak wdrożyć eternal orchestrations przy użyciu funkcji trwałe rozszerzenie dla usługi Azure Functions.
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
ms.openlocfilehash: f42526430599e47e673d359433e91b4687cbeb9e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Eternal orchestrations w funkcji trwałe (funkcje platformy Azure)

*Eternal orchestrations* są funkcje programu orchestrator, które nigdy nie zakończyć. Są przydatne, gdy chcesz użyć [trwałe funkcje](durable-functions-overview.md) agregatora i sytuacja, która wymaga nieskończoną pętlę.

## <a name="orchestration-history"></a>Historia aranżacji

Zgodnie z objaśnieniem w [punkty kontrolne i powtarzania](durable-functions-checkpointing-and-replay.md), trwałe Framework zadań przechowuje informacje o historii aranżacji każdej funkcji. Ta historia rozwoju stale tak długo, jak funkcja programu orchestrator w dalszym ciągu harmonogramu pracy nowe. Jeśli funkcja orchestrator przechodzi w nieskończonej pętli i stale Planowanie zadań, to historii można wzrostu wyczerpaniu dużych i spowodować problemy z wydajnością znaczące. *Eternal aranżacji* koncepcji została zaprojektowana w celu ograniczenia tego rodzaju problemy w przypadku aplikacji wymagających nieskończone pętle.

## <a name="resetting-and-restarting"></a>Resetowanie i ponowne uruchamianie

Zamiast nieskończone pętle funkcje programu orchestrator zresetowania stanu przez wywołanie metody [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) metody. Ta metoda przyjmuje jeden parametr do serializacji JSON, która staje się nowe dane wejściowe następnej generacji funkcji programu orchestrator.

Gdy `ContinueAsNew` jest nazywany enqueues wystąpienia wiadomości do samego siebie przed kończy działanie. Komunikat powoduje ponowne uruchomienie wystąpienia przy użyciu nowej wartości wejściowych. Ten sam identyfikator wystąpienia jest zachowana, ale funkcja orchestrator historii skutecznie został obcięty.

> [!NOTE]
> Trwałe Framework zadań przechowuje ten sam identyfikator wystąpienia, ale wewnętrznie tworzy nowy *Identyfikatora wykonania* dla funkcji programu orchestrator, który pobiera zresetowane przez `ContinueAsNew`. Ten identyfikator wykonywania zazwyczaj nie jest uwidaczniana zewnętrznie, ale warto wiedzieć o podczas debugowania wykonywania aranżacji.

> [!NOTE]
> `ContinueAsNew` — Metoda nie jest jeszcze dostępna w języku JavaScript.

## <a name="periodic-work-example"></a>Przykład okresowe pracy

Jeden przypadek użycia dla eternal orchestrations jest kod, który musi wykonywać okresowe zadania przez nieograniczony czas.

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup");

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

Różnica między w tym przykładzie i funkcję wyzwalany przez czasomierz jest tutaj razy wyzwalacza oczyszczania nie są oparte na podstawie harmonogramu. Na przykład CRON harmonogram, który wykonuje funkcję co godzinę wykona go 1:00, 2:00, 3:00 itp. i może potencjalnie wystąpiły problemy nakładają się na siebie. W tym przykładzie, jeśli oczyszczanie trwa 30 minut, następnie go zostanie zaplanowana z 1:00, 2:30, 4:00, itp. i nie ma żadnych szansy nakładają się na siebie.

## <a name="counter-example"></a>Przykład licznika

Oto przykład uproszczony *licznika* funkcja, która nasłuchuje *przyrostu* i *dekrementacji* eternally zdarzenia.

```csharp
[FunctionName("SimpleCounter")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    int counterState = context.GetInput<int>();

    string operation = await context.WaitForExternalEvent<string>("operation");

    if (operation == "incr")
    {
        counterState++;
    }
    else if (operation == "decr")
    {
        counterState--;
    }
    
    context.ContinueAsNew(counterState);
}
```

## <a name="exit-from-an-eternal-orchestration"></a>Wyjść z eternal aranżacji

Jeśli funkcja orchestrator musi wykonać po pewnym czasie, a następnie wszystko co należy zrobić to *nie* wywołać `ContinueAsNew` i pozwolić, aby zakończyć działanie funkcji.

Jeśli funkcja programu orchestrator w nieskończonej pętli i musi zostać zatrzymana, użyj [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) metody, zatrzymaj ją. Aby uzyskać więcej informacji, zobacz [Zarządzanie wystąpieniami](durable-functions-instance-management.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak zaimplementować orchestrations pojedynczego wystąpienia](durable-functions-singletons.md)
