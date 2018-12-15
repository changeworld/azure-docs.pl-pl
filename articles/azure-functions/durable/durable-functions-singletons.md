---
title: Wzorce Singleton dla funkcje trwałe - Azure
description: Jak używać pojedynczych elementów w rozszerzeniu Functons trwałe dla usługi Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 4832a48489a043493639bdedd6c6adf3c828de11
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2018
ms.locfileid: "53434702"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Pojedyncze orkiestratorów w funkcje trwałe (usługi Azure Functions)

Zadania w tle, które często zachodzi potrzeba upewnij się, że tylko jedno wystąpienie określonego programu orchestrator uruchamiane w danym momencie. Można to zrobić [funkcje trwałe](durable-functions-overview.md) , przypisując określony identyfikator wystąpienia do programu orchestrator po jej utworzeniu.

## <a name="singleton-example"></a>Przykład pojedyncze

Następujące C# i JavaScript przykładach funkcję wyzwalacza HTTP, która tworzy pojedyncze tła zadania aranżacji. Kod daje pewność, że to tylko jedno wystąpienie istnieje dla identyfikatora określonego wystąpienia.

### <a name="c"></a>C#

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
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

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionsName;

    // Check if an instance with the specified ID already exists.
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance) {
        // An instance with the specified ID doesn't exist, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

Domyślnie wystąpienie, które identyfikatory są losowo generowany identyfikatorów GUID. Ale w tym przypadku identyfikator wystąpienia jest przekazywany w danych trasy z adresu URL. Kod wywołuje [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) (C#) lub `getStatus` (JavaScript), aby sprawdzić, czy wystąpienie o określonym identyfikatorze jest już uruchomiona. Jeśli nie, wystąpienie jest tworzone za pomocą tego identyfikatora.

> [!WARNING]
> Wdrażając aplikacje lokalnie w języku JavaScript, musisz ustawić zmienną środowiskową `WEBSITE_HOSTNAME` do `localhost:<port>`, np. `localhost:7071` na korzystanie z metod `DurableOrchestrationClient`. Aby uzyskać więcej informacji na temat tego wymagania, zobacz [problem w usłudze GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

> [!NOTE]
> W tym przykładzie jest potencjalnych sytuacji wyścigu. Jeśli dwa wystąpienia **HttpStartSingle** współbieżnie, wyniku wykonywania dwóch różnych utworzeniem wystąpienia Singleton, jeden z nich, które powoduje zastąpienie innych. W zależności od wymagań może mieć niepożądane skutki uboczne. Z tego powodu należy upewnić się, że ma dwóch żądań można wykonać tej funkcji wyzwalacza jednocześnie.

Szczegóły implementacji funkcji programu orchestrator nie faktycznie znaczenia. Może to być funkcja regularne programu orchestrator, która zostanie uruchomione i zakończy, lub może być taki, który działa w nieskończoność (czyli [Eternal aranżacji](durable-functions-eternal-orchestrations.md)). Istotną kwestią jest dostępne tylko w przypadku kiedykolwiek jedno wystąpienie, które działa w danym momencie.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak wywołać podrzędnych aranżacji](durable-functions-sub-orchestrations.md)
