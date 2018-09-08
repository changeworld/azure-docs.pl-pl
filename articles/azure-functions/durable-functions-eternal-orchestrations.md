---
title: Orkiestracje nieustanne w funkcje trwałe - Azure
description: Dowiedz się, jak zaimplementować orkiestracje przy użyciu rozszerzenia funkcji trwałych dla usługi Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 98504534332b6faa7a7019aea9ab7b534d4c3faa
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094454"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Orkiestracje nieustanne w funkcje trwałe (usługi Azure Functions)

*Orkiestracje nieustanne* funkcji programu orchestrator, które nigdy nie zakończyć. Są one przydatne, jeśli chcesz użyć [funkcje trwałe](durable-functions-overview.md) agregatorów i dowolnego scenariusza, który wymaga wejścia w nieskończoną pętlę.

## <a name="orchestration-history"></a>Historia aranżacji

Jak wyjaśniono w [punkt kontrolny i powtarzanie](durable-functions-checkpointing-and-replay.md), trwałe Framework zadań śledzi informacje o historii poszczególnych aranżacji funkcji. Ta historia stale tak długo, jak funkcja programu orchestrator w dalszym ciągu zaplanować pracę nad nowym. Jeśli funkcja orkiestratora przechodzi w pętli nieskończonej, ciągłe Planowanie zadań historię, można powiększać niezwykle dużych i spowodować problemy z wydajnością znaczące. *Eternal aranżacji* koncepcji zaprojektowano tak, aby uniknąć tego rodzaju problemy w przypadku aplikacji wymagających pętli nieskończonej.

## <a name="resetting-and-restarting"></a>Resetowanie i ponowne uruchamianie

Zamiast używać pętli nieskończonej, funkcje programu orchestrator resetowania ich stanu, wywołując [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) metody. Ta metoda przyjmuje jeden parametr serializacji JSON, która staje się nowe dane wejściowe następnej generacji funkcji programu orchestrator.

Gdy `ContinueAsNew` nosi umieszczeniu wystąpienia komunikatów w samej przed kończy działanie. Komunikat powoduje ponowne uruchomienie wystąpienia przy użyciu nowych wartości wejściowej. Ten sam identyfikator wystąpienia są przechowywane, ale Historia funkcji programu orchestrator skutecznie został obcięty.

> [!NOTE]
> Trwałe Framework zadań utrzymuje ten sam identyfikator wystąpienia, ale wewnętrznie tworzy nową *Identyfikatora wykonania* dla funkcji programu orchestrator, która zostanie zresetowany przez `ContinueAsNew`. Ten identyfikator wykonania ogólnie nie jest uwidaczniana zewnętrznie, ale warto wiedzieć o podczas wykonywania aranżacji debugowania.

> [!NOTE]
> `ContinueAsNew` Metoda nie jest jeszcze dostępna w języku JavaScript.

## <a name="periodic-work-example"></a>Przykład okresowe pracy

Orkiestracje nieustanne jeden przypadek użycia jest kod, który potrzebuje do wykonywania pracy okresowe przez czas nieokreślony.

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

Różnią się w tym przykładzie i funkcji wyzwalanej przez czasomierz jest oczyszczania razy wyzwalacz w tym miejscu nie są oparte na podstawie harmonogramu. Na przykład harmonogramu wyrażenia CRON, która wykonuje funkcję, co godzinę będzie wykonywać go o 1:00, 2:00, 3:00 itd. oraz potencjalnie może uruchomić nakładania się problemy. W tym przykładzie, jeśli oczyszczanie trwa 30 minut, następnie go zostanie zaplanowana 1:00, 2:30, 4:00 itp. i nie ma możliwości nakładają się na siebie.

## <a name="exit-from-an-eternal-orchestration"></a>Opuszczenie eternal aranżacji

Jeśli funkcja orkiestratora musi ostatecznie zakończona, a następnie wszystko, czego potrzebujesz, aby zrobić to *nie* wywołania `ContinueAsNew` , a funkcja wyjść.

Jeśli funkcja orkiestratora znajduje się w nieskończonej pętli i musi zostać zatrzymana, należy użyć [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) metody, aby ją wyłączyć. Aby uzyskać więcej informacji, zobacz [Zarządzanie wystąpieniami](durable-functions-instance-management.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak zaimplementować orkiestracje pojedyncze](durable-functions-singletons.md)
