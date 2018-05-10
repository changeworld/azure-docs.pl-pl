---
title: Podrzędne orchestrations dla funkcji trwałe - Azure
description: Jak wywołać orchestrations z orchestrations w rozszerzeniu trwałe funkcji dla usługi Azure Functions.
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
ms.openlocfilehash: 7545a371749ed9af88f08af23cce3a513f494374
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Podrzędne orchestrations w funkcji trwałe (funkcje platformy Azure)

Oprócz wywoływanie funkcji działania, funkcje programu orchestrator mogą wywoływać inne funkcje programu orchestrator. Na przykład można utworzyć większą aranżacji poza bibliotekę funkcji programu orchestrator. Lub może uruchamiać wiele wystąpień funkcji orchestrator równolegle.

Funkcja programu orchestrator można wywołać inną funkcję orchestrator przez wywołanie metody [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) lub [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) metody. [Obsługa błędów i kompensacji](durable-functions-error-handling.md#automatic-retry-on-failure) artykuł zawiera więcej informacji na temat automatycznego ponów próbę.

Funkcje programu orchestrator podrzędne działają tak samo jak działanie funkcji względem obiektu wywołującego. Funkcje te mogą zwracać wartości, Zgłoś wyjątek i jest oczekiwane przez funkcję orchestrator nadrzędnej.

> [!NOTE]
> `CallSubOrchestratorAsync` i `CallSubOrchestratorWithRetryAsync` metody nie są jeszcze dostępne w języku JavaScript.

## <a name="example"></a>Przykład

Poniższy przykład przedstawia scenariusz IoT ("Internet rzeczy") w przypadku, gdy istnieje wiele urządzeń, które muszą być udostępniane. Brak określonego aranżacji, który musi zostać przeprowadzona dla wszystkich urządzeń, które może wyglądać jak poniżej:

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    string deviceId = ctx.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await ctx.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await ctx.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await ctx.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

Ta funkcja orchestrator może służyć jako — jest dla urządzenia jednorazowe Inicjowanie obsługi administracyjnej lub może być częścią większej aranżacji. W drugim przypadku funkcja orchestrator nadrzędnej można zaplanować wystąpienia `DeviceProvisioningOrchestration` przy użyciu `CallSubOrchestratorAsync` interfejsu API.

Oto przykład pokazujący sposób równolegle wielu funkcji programu orchestrator.

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    string[] deviceIds = await ctx.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = ctx.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, co to są koncentratory zadań i sposobach ich konfigurowania](durable-functions-task-hubs.md)
