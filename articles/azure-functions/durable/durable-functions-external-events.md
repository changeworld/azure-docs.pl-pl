---
title: Obsługa zewnętrznych zdarzeń w funkcje trwałe - Azure
description: Dowiedz się, jak obsługiwać zdarzenia zewnętrzne w rozszerzenia funkcji trwałych dla usługi Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: eb024e11b78d13d5ab4544c634acef2ade8141c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60730790"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Obsługa zdarzeń zewnętrznych w funkcje trwałe (usługi Azure Functions)

Funkcje programu orchestrator może poczekać i nasłuchiwać zdarzeń zewnętrznych. Ta funkcja [funkcje trwałe](durable-functions-overview.md) jest często przydatny w przypadku obsługi z reakcji człowieka lub innych zewnętrznych wyzwalaczy.

> [!NOTE]
> Wydarzenia zewnętrzne, które są jednokierunkowe operacji asynchronicznych. Nie są one odpowiednie w sytuacjach, w których klient wysyła zdarzenia wymaga synchronicznej odpowiedzi z funkcji programu orchestrator.

## <a name="wait-for-events"></a>Oczekiwanie na zdarzenia

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) metoda umożliwia korzystanie z funkcji programu orchestrator asynchronicznie poczekać i nasłuchiwania na zdarzenie zewnętrzne. Deklaruje nasłuchiwania funkcja orkiestratora *nazwa* zdarzenia i *kształtu danych* oczekuje otrzymać.

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) przez czas nieokreślony oczekuje na dane wejściowe.  Aplikacja funkcji może być bezpiecznie zwolniona podczas oczekiwania. Jeśli odebraniu zdarzenia dla tego wystąpienia orchestration jest wznowione automatycznie i natychmiast przetwarza zdarzenia.

> [!NOTE]
> Jeśli aplikacja funkcji korzysta z Plan zużycie, nie rozliczeń są naliczane opłaty, gdy funkcja orkiestratora oczekuje na zadanie z `WaitForExternalEvent` (.NET) lub `waitForExternalEvent` (JavaScript), niezależnie od tego, jak długo czekać.

Na platformie .NET, jeśli ładunek zdarzenia nie można przekonwertować na typ oczekiwany `T`, zgłaszany jest wyjątek.

## <a name="send-events"></a>Wysyłanie zdarzeń

[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) metody [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klasy wysyła zdarzenia `WaitForExternalEvent` (.NET) lub `waitForExternalEvent` czeka (JavaScript).  `RaiseEventAsync` Metoda przyjmuje *eventName* i *eventData* jako parametry. Dane zdarzenia musi być możliwy do serializacji JSON.

Poniżej przedstawiono przykład wyzwalanej przez kolejkę funkcja, która wysyła zdarzenie "Zatwierdzenie" na wystąpienie funkcji programu orchestrator. Identyfikator wystąpienia aranżacji pochodzą z treści komunikatu w kolejce.

### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

Wewnętrznie `RaiseEventAsync` (.NET) lub `raiseEvent` umieszczeniu (JavaScript) komunikat, który pobiera pobierane przez funkcję programu orchestrator oczekiwania. Jeśli wystąpienie nie oczekuje na określonym *Nazwa zdarzenia* komunikat o zdarzeniu zostanie dodany do kolejki w pamięci. Jeśli wystąpienie aranżacji później zaczyna nasłuchiwanie, *Nazwa zdarzenia* będzie sprawdzał, kolejki komunikatów o zdarzeniach.

> [!NOTE]
> Jeśli żadne wystąpienie aranżacji z określonym *identyfikator wystąpienia*, komunikat o zdarzeniu zostanie odrzucony. Aby uzyskać więcej informacji na temat tego zachowania, zobacz [problem w usłudze GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29). 

> [!WARNING]
> Wdrażając aplikacje lokalnie w języku JavaScript, musisz ustawić zmienną środowiskową `WEBSITE_HOSTNAME` do `localhost:<port>`, np. `localhost:7071` na korzystanie z metod `DurableOrchestrationClient`. Aby uzyskać więcej informacji na temat tego wymagania, zobacz [problem w usłudze GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak skonfigurować orkiestracje nieustanne](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Uruchamianie przykładu, który czeka, aż zdarzenia zewnętrzne](durable-functions-phone-verification.md)

> [!div class="nextstepaction"]
> [Uruchamianie przykładu, która czeka z reakcji człowieka](durable-functions-phone-verification.md)