---
title: Eternal aranżacje w Durable Functions — Azure
description: Dowiedz się, jak wdrożyć aranżacje Eternal przy użyciu rozszerzenia Durable Functions Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 8d28ae18c44c434dba053b23a60eb78728f8d8e0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232907"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Eternal aranżacji w Durable Functions (Azure Functions)

*Eternal aranżacje* to funkcje programu Orchestrator, które nigdy nie kończą się. Są one przydatne, gdy chcesz używać [Durable Functions](durable-functions-overview.md) do agregowania i scenariusza, który wymaga pętli nieskończonej.

## <a name="orchestration-history"></a>Historia aranżacji

Zgodnie z opisem w temacie [historia aranżacji](durable-functions-orchestrations.md#orchestration-history) , trwała struktura zadań śledzi historię każdej aranżacji funkcji. Ta historia stale rośnie, o ile funkcja programu Orchestrator nadal planuje nową pracę. Jeśli funkcja programu Orchestrator przejdzie w nieskończoną pętlę i ciągle planuje prace, ta historia może zwiększyć wydajność krytyczną i spowodować znaczne problemy z wydajnością. Koncepcja *aranżacji Eternal* została zaprojektowana w celu ograniczenia tego rodzaju problemów dla aplikacji, które wymagają nieskończonych pętli.

## <a name="resetting-and-restarting"></a>Resetowanie i ponowne uruchamianie

Zamiast używać nieskończonych pętli, funkcja programu Orchestrator resetuje swój stan przez wywołanie metody `ContinueAsNew` (.NET) lub `continueAsNew` (JavaScript) [powiązania wyzwalacza aranżacji](durable-functions-bindings.md#orchestration-trigger). Ta metoda przyjmuje jeden parametr możliwy do serializacji JSON, który staje się nowym danymi wejściowymi dla następnej generacji funkcji programu Orchestrator.

Gdy `ContinueAsNew` jest wywoływana, wystąpienie enqueues komunikat do samego siebie przed opuszczeniem. Komunikat uruchamia ponownie wystąpienie z nową wartością wejściową. Ten sam identyfikator wystąpienia jest zachowywany, ale historia funkcji programu Orchestrator jest efektywnie obcinana.

> [!NOTE]
> Usługa trwałych zadań obsługuje ten sam identyfikator wystąpienia, ale wewnętrznie tworzy nowy *Identyfikator wykonania* dla funkcji programu Orchestrator, która jest resetowana przez `ContinueAsNew`. Ten identyfikator wykonania zwykle nie jest ujawniany zewnętrznie, ale może być przydatne podczas debugowania wykonywania aranżacji.

## <a name="periodic-work-example"></a>Przykład pracy okresowej

Jeden przypadek użycia dla aranżacji Eternal to kod, który musi wykonywać okresowe zadania na czas nieokreślony.

### <a name="c"></a>C#

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

> [!NOTE]
> Poprzedni C# przykład dotyczy Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext`. Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

### <a name="javascript-functions-20-only"></a>JavaScript (tylko funkcje 2,0)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup.toDate());

    context.df.continueAsNew(undefined);
});
```

Różnica między tym przykładem a funkcją wyzwalaną czasomierzem oznacza, że czasy wyzwalacza czyszczenia nie są oparte na harmonogramie. Na przykład harmonogram firmy CRONUS, który wykonuje funkcję co godzinę, będzie wykonywał ją na 1:00, 2:00, 3:00 itd. i może być potencjalnie niezależny. Jeśli jednak oczyszczanie trwa 30 minut, zostanie zaplanowane o godzinie 1:00, 2:30, 4:00 itd. i nie ma możliwości nakładania się.

## <a name="starting-an-eternal-orchestration"></a>Rozpoczynanie aranżacji Eternal

Użyj metody `StartNewAsync` (.NET) lub `startNew` (JavaScript), aby rozpocząć aranżację Eternal, podobnie jak w przypadku każdej innej funkcji aranżacji.  

> [!NOTE]
> Jeśli musisz upewnić się, że Eternal aranżacja jest uruchomiona, ważne jest, aby zachować to samo wystąpienie `id` podczas uruchamiania aranżacji. Aby uzyskać więcej informacji, zobacz [Zarządzanie wystąpieniami](durable-functions-instance-management.md).

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";
    // Null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId, null); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> Poprzedni kod jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć atrybutu `OrchestrationClient` zamiast atrybutu `DurableClient` i należy użyć typu parametru `DurableOrchestrationClient` zamiast `IDurableOrchestrationClient`. Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

## <a name="exit-from-an-eternal-orchestration"></a>Wyjdź z aranżacji Eternal

Jeśli funkcja programu Orchestrator musi zostać zakończona, wystarczy, że *nie* wywołaj `ContinueAsNew` i pozwól na zakończenie działania funkcji.

Jeśli funkcja programu Orchestrator jest w pętli nieskończonej i musi zostać zatrzymana, należy użyć metody `TerminateAsync` (.NET) lub `terminate` (JavaScript) [powiązania klienta aranżacji](durable-functions-bindings.md#orchestration-client) , aby ją zatrzymać. Aby uzyskać więcej informacji, zobacz [Zarządzanie wystąpieniami](durable-functions-instance-management.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak zaimplementować pojedyncze aranżacje](durable-functions-singletons.md)
