---
title: Obsługa zdarzeń zewnętrznych w funkcjach trwałych — azure
description: Dowiedz się, jak obsługiwać zdarzenia zewnętrzne w rozszerzeniu Funkcje trwałe dla usługi Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 0877161f8d668141c8efb7c06b10643bf209341f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76262966"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Obsługa zdarzeń zewnętrznych w funkcjach trwałych (usługi Azure)

Funkcje orchestrator mają możliwość oczekiwania i nasłuchiwać zdarzeń zewnętrznych. Ta funkcja [funkcji trwałych](durable-functions-overview.md) jest często przydatna do obsługi interakcji z człowiekiem lub innych zewnętrznych wyzwalaczy.

> [!NOTE]
> Zdarzenia zewnętrzne są jednokierunkowe operacje asynchroniczne. Nie są one odpowiednie dla sytuacji, w których klient wysyłający zdarzenie wymaga synchronicznej odpowiedzi z funkcji koordynatora.

## <a name="wait-for-events"></a>Poczekaj na wydarzenia

Metody `WaitForExternalEvent` (.NET) `waitForExternalEvent` i (JavaScript) [powiązania wyzwalacza aranżacji](durable-functions-bindings.md#orchestration-trigger) umożliwia funkcji koordynatora asynchronicznie czekać i nasłuchiwać zdarzenia zewnętrznego. Funkcja koordynatora nasłuchiwania deklaruje *nazwę* zdarzenia i *kształt danych,* które oczekuje się odbierania.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> Poprzedni kod języka C# jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy używać `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext`. Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

W poprzednim przykładzie nasłuchuje określonego pojedynczego zdarzenia i podejmuje działania po jego odebraniu.

Można nasłuchiować wielu zdarzeń jednocześnie, jak w poniższym przykładzie, który czeka na jeden z trzech powiadomień o zdarzeniach możliwych.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> Poprzedni kod języka C# jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy używać `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext`. Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

W poprzednim przykładzie nasłuchuje *dowolnego* z wielu zdarzeń. Można również poczekać na *wszystkie* wydarzenia.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> Poprzedni kod jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy używać `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext`. Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

W .NET, jeśli ładunku zdarzenia nie można `T`przekonwertować na typ oczekiwany, wyjątek.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

`WaitForExternalEvent`czeka w nieskończoność na pewne dane wejściowe.  Aplikację funkcji można bezpiecznie zwolnić podczas oczekiwania. Jeśli i kiedy zdarzenie nadejdzie dla tego wystąpienia aranżacji, jest obudzony automatycznie i natychmiast przetwarza zdarzenie.

> [!NOTE]
> Jeśli aplikacja funkcji korzysta z planu zużycia, żadne opłaty rozliczeniowe nie są pobierane, gdy funkcja koordynatora oczekuje na zadanie z `WaitForExternalEvent` (.NET) lub `waitForExternalEvent` (JavaScript), bez względu na to, jak długo czeka.

## <a name="send-events"></a>Wysyłanie zdarzeń

Metoda `RaiseEventAsync` (.NET) `raiseEvent` lub (JavaScript) [powiązania klienta aranżacji](durable-functions-bindings.md#orchestration-client) wysyła zdarzenia, na które `WaitForExternalEvent` (.NET) lub `waitForExternalEvent` (JavaScript) czeka.  Metoda `RaiseEventAsync` przyjmuje *eventName* i *eventData* jako parametry. Dane zdarzenia muszą być możliwe do serializacji JSON.

Poniżej znajduje się przykładowa funkcja wyzwalana kolejką, która wysyła zdarzenie "Zatwierdzanie" do wystąpienia funkcji koordynatora. Identyfikator wystąpienia aranżacji pochodzi z treści komunikatu kolejki.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

> [!NOTE]
> Poprzedni kod języka C# jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy `OrchestrationClient` użyć `DurableClient` atrybutu zamiast atrybutu `DurableOrchestrationClient` i zamiast `IDurableOrchestrationClient`parametru . Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

---

Wewnętrznie `RaiseEventAsync` (.NET) lub `raiseEvent` (JavaScript) w kolejce wiadomość, która zostanie odebrana przez funkcję orkiestratora oczekiwania. Jeśli wystąpienie nie oczekuje na określoną *nazwę zdarzenia,* komunikat o zdarzeniu jest dodawany do kolejki w pamięci. Jeśli wystąpienie aranżacji później rozpocznie nasłuchiwanie tej *nazwy zdarzenia,* sprawdzi kolejkę dla komunikatów o zdarzeniach.

> [!NOTE]
> Jeśli nie ma wystąpienia aranżacji o określonym *identyfikatorze wystąpienia,* komunikat o zdarzeniu zostanie odrzucony.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak zaimplementować obsługę błędów](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Uruchom próbkę, która czeka na interakcję z człowiekiem](durable-functions-phone-verification.md)