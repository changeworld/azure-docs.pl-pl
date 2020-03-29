---
title: Podorganizacje dla trwałych funkcji — Azure
description: Jak wywołać aranżacji z aranżacji w rozszerzenie funkcje trwałe dla usługi Azure Functions.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: d4d599063f727510cbf504ea3d121bdabfe001c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261521"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Podaranżacji w funkcji trwałych (usługi Azure)

Oprócz wywoływania funkcji działania, funkcje orkiestratora można wywołać inne funkcje orkiestratora. Na przykład można utworzyć większą aranżację z biblioteki mniejszych funkcji aranżatora. Lub można uruchomić wiele wystąpień funkcji orkiestratora równolegle.

Funkcja koordynatora może wywołać inną `CallSubOrchestratorAsync` funkcję `CallSubOrchestratorWithRetryAsync` koordynatora przy użyciu `callSubOrchestrator` lub `callSubOrchestratorWithRetry` metod w .NET lub lub metody w języku JavaScript. W artykule [& compensation obsługi błędów](durable-functions-error-handling.md#automatic-retry-on-failure) zawiera więcej informacji na temat automatycznego ponawiania prób.

Funkcje podaranżatora zachowują się podobnie jak funkcje działania z perspektywy wywołującego. Mogą zwracać wartość, zgłaszać wyjątek i mogą być oczekiwane przez nadrzędną funkcję koordynatora. 
## <a name="example"></a>Przykład

Poniższy przykład ilustruje scenariusz IoT ("Internet rzeczy"), w którym istnieje wiele urządzeń, które muszą być aprowizacji. Następująca funkcja reprezentuje przepływ pracy inicjowania obsługi administracyjnej, który musi zostać wykonany dla każdego urządzenia:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

Ta funkcja koordynatora może być używana jako — jest dla jednorazowego inicjowania obsługi administracyjnej urządzenia lub może być częścią większej aranżacji. W tym ostatnim przypadku nadrzędna funkcja koordynatora `DeviceProvisioningOrchestration` może `CallSubOrchestratorAsync` planować wystąpienia `callSubOrchestrator` korzystania z interfejsu API (.NET) lub (JavaScript).

Oto przykład, który pokazuje, jak uruchomić wiele funkcji koordynatora równolegle.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> Poprzednie przykłady języka C# są dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy używać `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext`. Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    var id = 0;
    for (const deviceId of deviceIds) {
        const child_id = context.df.instanceId+`:${id}`;
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId, child_id);
        provisioningTasks.push(provisionTask);
        id++;
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

---

> [!NOTE]
> Podaranżacji muszą być zdefiniowane w tej samej aplikacji funkcji jako nadrzędnej aranżacji. Jeśli chcesz wywołać i czekać na aranżacji w innej aplikacji funkcji, należy rozważyć użycie wbudowanej obsługi interfejsów API HTTP i http 202 sondowania wzorca konsumenta. Aby uzyskać więcej informacji, zobacz temat [Funkcje HTTP.](durable-functions-http-features.md)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak ustawić niestandardowy stan aranżacji](durable-functions-custom-orchestration-status.md)