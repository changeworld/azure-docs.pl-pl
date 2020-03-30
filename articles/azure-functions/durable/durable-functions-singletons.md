---
title: Singletons dla trwałych funkcji — Azure
description: Jak używać singletons w rozszerzenie funkcje trwałe dla usługi Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 4eff7c4c91ed664fcf1f4fc7a8be2d43d24e5c6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76262813"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Koordynatorzy singleton w funkcjach trwałych (usługi Azure)

W przypadku zadań w tle często należy upewnić się, że tylko jedno wystąpienie określonego koordynatora działa w danym momencie. Można zapewnić tego rodzaju zachowanie singleton w [funkcji trwałych,](durable-functions-overview.md) przypisując identyfikator określonego wystąpienia do koordynatora podczas tworzenia go.

## <a name="singleton-example"></a>Przykład Singleton

W poniższym przykładzie przedstawiono funkcję wyzwalacza HTTP, która tworzy aranżację zadania w tle singleton. Kod zapewnia, że istnieje tylko jedno wystąpienie dla określonego identyfikatora wystąpienia.

# <a name="c"></a>[C #](#tab/csharp)

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
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

> [!NOTE]
> Poprzedni kod języka C# jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy `OrchestrationClient` użyć `DurableClient` atrybutu zamiast atrybutu `DurableOrchestrationClient` i zamiast `IDurableOrchestrationClient`parametru . Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[Javascript](#tab/javascript)

**funkcja.json**

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

**indeks.js**

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

---

Domyślnie identyfikatory wystąpień są losowo generowanymi identyfikatorami GUID. W poprzednim przykładzie identyfikator wystąpienia jest jednak przekazywany w danych trasy z adresu URL. Wywołanie `GetStatusAsync`kodu (C#) lub `getStatus` (JavaScript), aby sprawdzić, czy wystąpienie o określonym identyfikatorze jest już uruchomione. Jeśli takie wystąpienie nie jest uruchomione, nowe wystąpienie jest tworzony z tym identyfikatorem.

> [!NOTE]
> Istnieje potencjalna kondycja wyścigu w tej próbce. Jeśli dwa wystąpienia **HttpStartSingle** wykonać jednocześnie, oba wywołania funkcji zgłosi sukces, ale tylko jedno wystąpienie aranżacji faktycznie rozpocznie. W zależności od wymagań, może to mieć niepożądane skutki uboczne. Z tego powodu ważne jest, aby upewnić się, że żadne dwa żądania można wykonać tę funkcję wyzwalacza jednocześnie.

Szczegóły implementacji funkcji koordynatora w rzeczywistości nie mają znaczenia. Może to być regularna funkcja aranżatora, która uruchamia się i uzupełnia, lub może to być taka, która działa wiecznie (czyli [Wieczna Orkiestracja).](durable-functions-eternal-orchestrations.md) Ważne jest to, że istnieje tylko jeden przypadek uruchomiony w czasie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o natywnych funkcjach HTTP aranżacji](durable-functions-http-features.md)
