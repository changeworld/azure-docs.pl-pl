---
title: Wzorce Singleton dla funkcje trwałe - Azure
description: Jak używać pojedynczych elementów w rozszerzenia funkcji trwałych dla usługi Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: c032ba046668310ff71d067d22a805fc6446667c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683804"
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

Poniżej przedstawiono plik function.json:
```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Poniżej przedstawiono kod JavaScript:
```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

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
> W tym przykładzie jest potencjalnych sytuacji wyścigu. Jeśli dwa wystąpienia **HttpStartSingle** wykonania jednocześnie, oba wywołania funkcji zakomunikuje sukces, ale faktycznie rozpocznie aranżacji tylko jedno wystąpienie. W zależności od wymagań może mieć niepożądane skutki uboczne. Z tego powodu należy upewnić się, że ma dwóch żądań można wykonać tej funkcji wyzwalacza jednocześnie.

Szczegóły implementacji funkcji programu orchestrator nie faktycznie znaczenia. Może to być funkcja regularne programu orchestrator, która zostanie uruchomione i zakończy, lub może być taki, który działa w nieskończoność (czyli [Eternal aranżacji](durable-functions-eternal-orchestrations.md)). Istotną kwestią jest dostępne tylko w przypadku kiedykolwiek jedno wystąpienie, które działa w danym momencie.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak wywołać podrzędnych aranżacji](durable-functions-sub-orchestrations.md)
