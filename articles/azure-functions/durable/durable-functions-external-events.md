---
title: Obsługa zdarzeń zewnętrznych w Durable Functions — Azure
description: Dowiedz się, jak obsługiwać zdarzenia zewnętrzne w rozszerzeniu Durable Functions Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: d9c546064589e82cfef367978ebea98c2c202307
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087302"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Obsługa zdarzeń zewnętrznych w Durable Functions (Azure Functions)

Funkcje programu Orchestrator mają możliwość oczekiwania i nasłuchiwania zdarzeń zewnętrznych. Ta funkcja [Durable Functions](durable-functions-overview.md) jest często przydatna do obsługi interakcji człowieka lub innych zewnętrznych wyzwalaczy.

> [!NOTE]
> Zdarzenia zewnętrzne to jednokierunkowe operacje asynchroniczne. Nie są odpowiednie do sytuacji, w których klient wysyłający zdarzenie potrzebuje synchronicznej odpowiedzi z funkcji programu Orchestrator.

## <a name="wait-for-events"></a>Zaczekaj na zdarzenia

Metoda [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) umożliwia asynchroniczne oczekiwanie i nasłuchiwanie zdarzeń zewnętrznych przez funkcję programu Orchestrator. Funkcja programu Orchestrator nasłuchiwanie deklaruje *nazwę* zdarzenia i *kształt danych* , które oczekuje na odebranie.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

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

Poprzedni przykład nasłuchuje określonego pojedynczego zdarzenia i podejmuje akcję po odebraniu.

Można nasłuchiwać wielu zdarzeń współbieżnie, takich jak w poniższym przykładzie, które czeka na jedno z trzech możliwych powiadomień o zdarzeniach.

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

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

Poprzedni przykład nasłuchuje dla *dowolnego* z wielu zdarzeń. Istnieje również możliwość poczekania na *wszystkie* zdarzenia.

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

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

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) czeka na nieokreślony czas w przypadku niektórych danych wejściowych.  Aplikacja funkcji może zostać bezpiecznie zwolniona z ładowania podczas oczekiwania. Jeśli i po nadejściu zdarzenia dla tego wystąpienia aranżacji zostanie ono automatycznie wznowione i natychmiast przetworzy zdarzenie.

> [!NOTE]
> Jeśli aplikacja funkcji korzysta z planu zużycia, nie są naliczane opłaty za rozliczanie, gdy funkcja programu Orchestrator oczekuje na `WaitForExternalEvent` zadanie z (.NET `waitForExternalEvent` ) lub (JavaScript), niezależnie od tego, jak długo czeka.

W programie .NET, jeśli nie można przekonwertować ładunku zdarzenia na oczekiwany typ `T`, zostanie zgłoszony wyjątek.

## <a name="send-events"></a>Wysyłanie zdarzeń

Metoda [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) klasy [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) wysyła zdarzenia, które `WaitForExternalEvent` (.NET) lub `waitForExternalEvent` (JavaScript) czeka na.  Metoda `RaiseEventAsync` przyjmuje wartość *EventName* i *eventData* jako parametry. Dane zdarzenia muszą być serializowane w formacie JSON.

Poniżej znajduje się przykładowa funkcja wyzwalana przez kolejki, która wysyła zdarzenie "zatwierdzenie" do wystąpienia funkcji programu Orchestrator. Identyfikator wystąpienia aranżacji pochodzi z treści komunikatu w kolejce.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

Wewnętrznie, `RaiseEventAsync` (.NET) lub `raiseEvent` (JavaScript) enqueues komunikat, który jest pobierany przez oczekiwaną funkcję programu Orchestrator. Jeśli wystąpienie nie oczekuje na określoną *nazwę zdarzenia,* komunikat o zdarzeniu zostanie dodany do kolejki w pamięci. Jeśli wystąpienie aranżacji rozpocznie nasłuchiwanie dla tej *nazwy zdarzenia,* sprawdza kolejkę komunikatów o zdarzeniach.

> [!NOTE]
> Jeśli nie istnieje wystąpienie aranżacji o określonym IDENTYFIKATORze *wystąpienia*, komunikat o zdarzeniu zostanie odrzucony. Aby uzyskać więcej informacji na temat tego zachowania, zobacz problem z usługą [GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29). 

> [!WARNING]
> Podczas tworzenia lokalnie w języku JavaScript należy ustawić zmienną `WEBSITE_HOSTNAME` środowiskową na `localhost:<port>`, np. `localhost:7071`Aby użyć metod w `DurableOrchestrationClient`. Aby uzyskać więcej informacji na temat tego wymagania, zobacz artykuł dotyczący usługi [GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak skonfigurować aranżacje Eternal](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Uruchamianie przykładu, który czeka na zdarzenia zewnętrzne](durable-functions-phone-verification.md)

> [!div class="nextstepaction"]
> [Uruchamianie przykładu, który czeka na interakcję człowieka](durable-functions-phone-verification.md)