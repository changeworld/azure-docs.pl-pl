---
title: Wzorce Singleton dla funkcji trwałe - Azure
description: Jak używać pojedynczych wystąpień w rozszerzeniu Functons trwałe dla usługi Azure Functions.
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
ms.openlocfilehash: ea8b5db946d6b35ea4583d9170ec36e5f95e16cd
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/17/2018
ms.locfileid: "29972555"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Pojedyncze orchestrators w funkcji trwałe (funkcje platformy Azure)

Dla zadań w tle lub orchestrations aktora stylu, często wymaga upewnić się, że tylko jedno wystąpienie określonego programu orchestrator jest uruchamiany w czasie. Można to zrobić [trwałe funkcje](durable-functions-overview.md) przypisując konkretnego wystąpienia identyfikator programu orchestrator, podczas jej tworzenia.

## <a name="singleton-example"></a>Przykład pojedynczego wystąpienia

W poniższym przykładzie C# zawiera funkcję wyzwalacza HTTP, która tworzy aranżacji zadania tła singleton. Kod stanowi gwarancję, że istnieje tego tylko jedno wystąpienie określonego wystąpienia identyfikatora.

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    TraceWriter log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.Info($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

Domyślnie wystąpienie, które identyfikatory są losowo wygenerowane identyfikatorów GUID. Jednak w takim przypadku Identyfikatora wystąpienia jest przekazywany w danych trasy z podanego adresu URL. Kod wywołuje [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) do sprawdzenia, czy wystąpienie o określonym identyfikatorze jest już uruchomiony. Jeśli nie, tworzone jest wystąpienie o tym identyfikatorze.

Szczegóły implementacji funkcji programu orchestrator nie faktycznie znaczenia. Może to być funkcja regularne orchestrator momencie rozpoczęcia i zakończenia lub może być taki, który działa w nieskończoność (to znaczy [Eternal aranżacji](durable-functions-eternal-orchestrations.md)). Istotne jest tylko kiedykolwiek jednego działania w czasie wystąpienia.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak wywołać orchestrations podrzędne](durable-functions-sub-orchestrations.md)
