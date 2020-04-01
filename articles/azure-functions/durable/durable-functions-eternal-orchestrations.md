---
title: Wieczne aranżacji w funkcji trwałych — Azure
description: Dowiedz się, jak zaimplementować wieczne aranżacji przy użyciu rozszerzenia funkcje trwałe dla funkcji platformy Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d55e08fecbd1338284607ac59fe354c6fa8cb1ea
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478823"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Wieczne aranżacji w funkcji trwałych (usługi Azure)

*Wieczne aranżacje* są funkcjami orkiestratora, które nigdy się nie kończą. Są one przydatne, gdy chcesz użyć [funkcji trwałych](durable-functions-overview.md) dla agregatorów i każdy scenariusz, który wymaga nieskończonej pętli.

## <a name="orchestration-history"></a>Historia aranżacji

Jak wyjaśniono w temacie [historii aranżacji,](durable-functions-orchestrations.md#orchestration-history) trwała struktura zadań śledzi historię każdej aranżacji funkcji. Ta historia stale rośnie tak długo, jak funkcja koordynatora nadal planuje nowe prace. Jeśli funkcja koordynatora przechodzi w nieskończoną pętlę i stale planuje pracę, ta historia może wzrosnąć krytycznie duże i powodować znaczące problemy z wydajnością. Koncepcja *wiecznej aranżacji* została zaprojektowana w celu złagodzenia tego rodzaju problemów dla aplikacji, które wymagają nieskończonych pętli.

## <a name="resetting-and-restarting"></a>Resetowanie i ponowne uruchamianie

Zamiast używać nieskończonych pętli, funkcje aranżatora resetują swój stan, wywołując metodę `ContinueAsNew` (.NET) lub `continueAsNew` (JavaScript) powiązania [wyzwalacza aranżacji](durable-functions-bindings.md#orchestration-trigger). Ta metoda przyjmuje pojedynczy parametr serializable JSON, który staje się nowym wejściem dla następnego generowania funkcji orkiestry.

Po `ContinueAsNew` wywołaniu wystąpienia w kolejce wiadomość do siebie przed zamknięciem. Komunikat uruchamia ponownie wystąpienie z nową wartością wejściową. Ten sam identyfikator wystąpienia jest zachowywany, ale historia funkcji koordynatora jest skutecznie obcinana.

> [!NOTE]
> Struktura trwałych zadań zachowuje ten sam identyfikator wystąpienia, ale wewnętrznie tworzy nowy *identyfikator* wykonania `ContinueAsNew`dla funkcji koordynatora, która zostanie zresetowana przez program . Ten identyfikator wykonania zazwyczaj nie jest narażony na zewnątrz, ale może być przydatne wiedzieć o podczas debugowania wykonanie aranżacji.

## <a name="periodic-work-example"></a>Przykład pracy okresowego

Jednym z przypadków użycia dla wiecznych aranżacji jest kod, który musi wykonywać okresową pracę przez czas nieokreślony.

# <a name="c"></a>[C #](#tab/csharp)

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
> Poprzedni przykład języka C# jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy używać `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext`. Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

---

Różnica między tym przykładem a funkcją wyzwalaną przez czasomierza polega na tym, że czasy wyzwalania oczyszczania w tym miejscu nie są oparte na harmonogramie. Na przykład harmonogram CRON, który wykonuje funkcję co godzinę będzie wykonywać go o 1:00, 2:00, 3:00 itp. W tym przykładzie jednak, jeśli oczyszczanie trwa 30 minut, a następnie zostanie zaplanowane na 1:00, 2:30, 4:00, itp.

## <a name="starting-an-eternal-orchestration"></a>Rozpoczynanie wiecznej aranżacji

Użyj `StartNewAsync` metody (.NET) `startNew` lub (JavaScript), aby rozpocząć wieczną aranżację, tak jak w przypadku każdej innej funkcji aranżacji.  

> [!NOTE]
> Jeśli musisz upewnić się, że pojedyncza wieczna aranżacja `id` jest uruchomiona, ważne jest, aby zachować to samo wystąpienie podczas uruchamiania aranżacji. Aby uzyskać więcej informacji, zobacz [Zarządzanie wystąpieniami](durable-functions-instance-management.md).

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";

    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> Poprzedni kod jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy `OrchestrationClient` użyć `DurableClient` atrybutu zamiast atrybutu `DurableOrchestrationClient` i zamiast `IDurableOrchestrationClient`parametru . Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = "StaticId";
    
    // null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.startNew("Periodic_Cleanup_Loop", instanceId, null);

    context.log(`Started orchestration with ID = '${instanceId}'.`);
    return client.createCheckStatusResponse(context.bindingData.req, instanceId);
};
```

---

## <a name="exit-from-an-eternal-orchestration"></a>Wyjście z wiecznej aranżacji

Jeśli funkcja koordynatora musi ostatecznie zakończyć, to wszystko, `ContinueAsNew` co musisz zrobić, to *nie* wywołać i pozwolić na zakończenie funkcji.

Jeśli funkcja aranżatora jest w nieskończonej pętli `TerminateAsync` i musi `terminate` zostać zatrzymana, użyj metody (.NET) lub (JavaScript) [powiązania klienta aranżacji,](durable-functions-bindings.md#orchestration-client) aby go zatrzymać. Aby uzyskać więcej informacji, zobacz [Zarządzanie wystąpieniami](durable-functions-instance-management.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak zaimplementować aranżacji singleton](durable-functions-singletons.md)
