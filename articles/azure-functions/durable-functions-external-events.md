---
title: Obsługa zewnętrznych zdarzeń w funkcje trwałe - Azure
description: Dowiedz się, jak obsługiwać zdarzenia zewnętrzne w rozszerzenia funkcji trwałych dla usługi Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 728ed892b4be4334574a04c9794bf3ea549944d4
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44093987"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Obsługa zdarzeń zewnętrznych w funkcje trwałe (usługi Azure Functions)

Funkcje programu orchestrator może poczekać i nasłuchiwać zdarzeń zewnętrznych. Ta funkcja [funkcje trwałe](durable-functions-overview.md) jest często przydatny w przypadku obsługi z reakcji człowieka lub innych zewnętrznych wyzwalaczy.

## <a name="wait-for-events"></a>Oczekiwanie na zdarzenia

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) metoda umożliwia korzystanie z funkcji programu orchestrator asynchronicznie poczekać i nasłuchiwania na zdarzenie zewnętrzne. Deklaruje nasłuchiwania funkcja orkiestratora *nazwa* zdarzenia i *kształtu danych* oczekuje otrzymać.

#### <a name="c"></a>C#

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (tylko funkcje v2)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

Poprzedni przykład nasłuchuje określonej pojedyncze zdarzenie i podejmuje działania, po odebraniu.

Może nasłuchiwać wielu zdarzeń jednocześnie, jak pokazano w poniższym przykładzie czeka na jedno z trzech powiadomienia o zdarzeniach możliwe.

#### <a name="c"></a>C#

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (tylko funkcje v2)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

Poprzedni przykład nasłuchuje *wszelkie* wielu zdarzeń. Istnieje również możliwość oczekiwania *wszystkich* zdarzenia.

#### <a name="c"></a>C#

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (tylko funkcje v2)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivityAsync("IssueBuildingPermit", applicationId);
});
```

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) przez czas nieokreślony oczekuje na dane wejściowe.  Aplikacja funkcji może być bezpiecznie zwolniona podczas oczekiwania. Jeśli odebraniu zdarzenia dla tego wystąpienia orchestration jest wznowione automatycznie i natychmiast przetwarza zdarzenia.

> [!NOTE]
> Jeśli aplikacja funkcji korzysta z Plan zużycie, nie rozliczeń są naliczane opłaty, gdy funkcja orkiestratora oczekuje na zadanie z `WaitForExternalEvent`, niezależnie od tego, jak długo czekać.

Na platformie .NET, jeśli ładunek zdarzenia nie można przekonwertować na typ oczekiwany `T`, zgłaszany jest wyjątek.

## <a name="send-events"></a>Wysyłanie zdarzeń

[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) metody [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klasy wysyła zdarzenia `WaitForExternalEvent` oczekuje.  `RaiseEventAsync` Metoda przyjmuje *eventName* i *eventData* jako parametry. Dane zdarzenia musi być możliwy do serializacji JSON.

Poniżej przedstawiono przykład wyzwalanej przez kolejkę funkcja, która wysyła zdarzenie "Zatwierdzenie" na wystąpienie funkcji programu orchestrator. Identyfikator wystąpienia aranżacji pochodzą z treści komunikatu w kolejce.

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

Wewnętrznie `RaiseEventAsync` umieszczeniu komunikat, który pobiera pobierane przez funkcję programu orchestrator oczekiwania.

> [!WARNING]
> Jeśli żadne wystąpienie aranżacji z określonym *identyfikator wystąpienia* lub jeśli wystąpienie nie oczekuje na określonym *Nazwa zdarzenia*, komunikat o zdarzeniu zostanie odrzucony. Aby uzyskać więcej informacji na temat tego zachowania, zobacz [problem w usłudze GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak skonfigurować orkiestracje nieustanne](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Uruchamianie przykładu, który czeka, aż zdarzenia zewnętrzne](durable-functions-phone-verification.md)

> [!div class="nextstepaction"]
> [Uruchamianie przykładu, która czeka z reakcji człowieka](durable-functions-phone-verification.md)

