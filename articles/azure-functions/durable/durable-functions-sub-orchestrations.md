---
title: Podaranżacje dla Durable Functions — Azure
description: Jak wywoływać aranżacje z aranżacji w rozszerzeniu Durable Functions Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 868efad58e14fd817729f0aa9ac785bc0f960867
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087033"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Organizowanie podrzędne w Durable Functions (Azure Functions)

Oprócz wywoływania funkcji działania, funkcje programu Orchestrator mogą wywoływać inne funkcje programu Orchestrator. Można na przykład utworzyć większą organizację poza biblioteką funkcji programu Orchestrator. Można też uruchomić wiele wystąpień funkcji programu Orchestrator równolegle.

Funkcja programu Orchestrator może wywoływać inną funkcję programu Orchestrator, wywołując metody [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) lub [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) w programie .NET albo `callSubOrchestrator` metody lub `callSubOrchestratorWithRetry` w języku JavaScript. W artykule dotyczącym [obsługi błędów & wynagrodzenie](durable-functions-error-handling.md#automatic-retry-on-failure) zawiera więcej informacji na temat automatycznego ponawiania próby.

Funkcje programu sub-Orchestrator zachowują się podobnie jak funkcje działania z perspektywy obiektu wywołującego. Mogą zwrócić wartość, zgłosić wyjątek i może oczekiwać przez nadrzędną funkcję programu Orchestrator.

## <a name="example"></a>Przykład

Poniższy przykład ilustruje scenariusz IoT ("Internet rzeczy"), w którym istnieje wiele urządzeń, które muszą być obsługiwane. Istnieje określona aranżacja, która musi mieć miejsce dla każdego z urządzeń, co może wyglądać następująco:

### <a name="c"></a>C#

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

Ta funkcja programu Orchestrator może być używana jako — dla jednorazowej aprowizacji urządzeń lub może być częścią większej aranżacji. W tym drugim przypadku nadrzędna funkcja programu Orchestrator może planować wystąpienia `DeviceProvisioningOrchestration` `CallSubOrchestratorAsync` przy użyciu interfejsuC#API ( `callSubOrchestrator` ) lub (JavaScript).

Oto przykład, w którym pokazano, jak uruchomić wiele funkcji programu Orchestrator równolegle.

### <a name="c"></a>C#

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    for (const deviceId of deviceIds) {
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.push(provisionTask);
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jakie centra zadań są i jak je skonfigurować](durable-functions-task-hubs.md)
