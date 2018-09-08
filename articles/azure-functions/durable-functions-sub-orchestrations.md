---
title: Podrzędne mechanizmów dla funkcje trwałe - Azure
description: Jak wywołać aranżacji z mechanizmów w rozszerzenia funkcji trwałych dla usługi Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 59e8eb41b7e9fe3d57196f6844d1a768c3ef598b
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094443"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Podrzędne aranżacji w funkcje trwałe (usługa Azure Functions)

Oprócz wywoływanie funkcji działania, funkcje programu orchestrator może wywoływać innych funkcji programu orchestrator. Można na przykład, utworzyć większy aranżacji poza bibliotekę funkcji programu orchestrator. Lub można uruchomić wiele wystąpień funkcji orkiestratora równolegle.

Funkcja programu orchestrator może wywołać inną funkcję programu orchestrator, wywołując [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) lub [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) metody. [Obsługa błędów i rekompensaty](durable-functions-error-handling.md#automatic-retry-on-failure) artykuł zawiera więcej informacji na temat automatycznego ponawiania prób.

Podrzędne orchestrator funkcje zachowują się podobnie jak funkcje działań z punktu widzenia obiektu wywołującego. Funkcje te mogą zwracać wartości, zgłoszenie wyjątku i może być oczekiwana przez nadrzędne funkcję programu orchestrator.

> [!NOTE]
> `CallSubOrchestratorAsync` i `CallSubOrchestratorWithRetryAsync` metod nie są jeszcze dostępne w języku JavaScript.

## <a name="example"></a>Przykład

W poniższym przykładzie pokazano scenariusz IoT ("Internet rzeczy") w przypadku, gdy istnieje wiele urządzeń, które należy aprowizować. Brak określonego aranżacji, który musi zostać przeprowadzona dla wszystkich urządzeń, które może wyglądać następująco:

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

Ta funkcja programu orchestrator może służyć jako — jest dla urządzenia jednorazowe Inicjowanie obsługi administracyjnej lub może być częścią większej aranżacji. W tym ostatnim przypadku funkcji programu orchestrator nadrzędnej można zaplanować wystąpień `DeviceProvisioningOrchestration` przy użyciu `CallSubOrchestratorAsync` interfejsu API.

Oto przykład pokazujący sposób równoległego uruchamiania wielu funkcji programu orchestrator.

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
> [Dowiedz się, czym są koncentratory zadań i sposobach ich konfigurowania](durable-functions-task-hubs.md)
