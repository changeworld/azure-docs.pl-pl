---
title: Wzorce Singleton dla funkcje trwałe - Azure
description: Jak używać pojedynczych elementów w rozszerzeniu Functons trwałe dla usługi Azure Functions.
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
ms.openlocfilehash: 71c0cebf676d29308fe9f4942350ae96d3bedcf6
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37340835"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Pojedyncze orkiestratorów w funkcje trwałe (usługi Azure Functions)

Dla zadania w tle lub aranżacji stylu aktora, często musisz upewnij się, że tylko jedno wystąpienie określonego programu orchestrator jest uruchamiany w danym momencie. Można to zrobić [funkcje trwałe](durable-functions-overview.md) , przypisując określony identyfikator wystąpienia do programu orchestrator po jej utworzeniu.

## <a name="singleton-example"></a>Przykład pojedyncze

C# poniższy kod przedstawia funkcję wyzwalacza HTTP, która tworzy pojedyncze tła zadania aranżacji. Kod daje pewność, że to tylko jedno wystąpienie istnieje dla identyfikatora określonego wystąpienia.

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

Domyślnie wystąpienie, które identyfikatory są losowo generowany identyfikatorów GUID. Ale w tym przypadku identyfikator wystąpienia jest przekazywany w danych trasy z adresu URL. Kod wywołuje [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) do sprawdzenia, czy wystąpienie o określonym identyfikatorze jest już uruchomiona. Jeśli nie, wystąpienie jest tworzone za pomocą tego identyfikatora.

> [!NOTE]
> W tym przykładzie jest potencjalnych sytuacji wyścigu. Jeśli dwa wystąpienia **HttpStartSingle** współbieżnie, wyniku wykonywania dwóch różnych utworzeniem wystąpienia Singleton, jeden z nich, które powoduje zastąpienie innych. W zależności od wymagań może mieć niepożądane skutki uboczne. Z tego powodu należy upewnić się, że ma dwóch żądań można wykonać tej funkcji wyzwalacza jednocześnie.

Szczegóły implementacji funkcji programu orchestrator nie faktycznie znaczenia. Może to być funkcja regularne programu orchestrator, która zostanie uruchomione i zakończy, lub może być taki, który działa w nieskończoność (czyli [Eternal aranżacji](durable-functions-eternal-orchestrations.md)). Istotną kwestią jest dostępne tylko w przypadku kiedykolwiek jedno wystąpienie, które działa w danym momencie.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak wywołać podrzędnych aranżacji](durable-functions-sub-orchestrations.md)
