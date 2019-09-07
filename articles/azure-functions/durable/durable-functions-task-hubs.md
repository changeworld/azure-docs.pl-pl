---
title: Centra zadań w Durable Functions — Azure
description: Dowiedz się, co to jest centrum zadań w Durable Functions rozszerzeniu Azure Functions. Informacje na temat konfigurowania centrów zadań.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: b0a58251530467d788710b0584b15715a207e20f
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734322"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Centra zadań w Durable Functions (Azure Functions)

*Centrum zadań* w [Durable Functions](durable-functions-overview.md) jest kontenerem logicznym dla zasobów usługi Azure Storage, które są używane dla aranżacji. Funkcje programu Orchestrator i Activity mogą współdziałać ze sobą tylko wtedy, gdy należą do tego samego centrum zadań.

Jeśli wiele aplikacji funkcji udostępnia konto magazynu, każda aplikacja funkcji *musi* być skonfigurowana przy użyciu oddzielnej nazwy centrum zadań. Konto magazynu może zawierać wiele centrów zadań. Na poniższym diagramie przedstawiono jeden centrum zadań dla każdej aplikacji funkcji na udostępnionych i dedykowanych kontach magazynu.

![Diagram przedstawiający udostępnione i dedykowane konta magazynu.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Zasoby usługi Azure Storage

Centrum zadań składa się z następujących zasobów magazynu:

* Jedna lub więcej kolejek kontroli.
* Jedna kolejka elementów roboczych.
* Jedna tabela historii.
* Jedna tabela wystąpień.
* Jeden kontener magazynu zawierający co najmniej jeden obiekt BLOB dzierżawy.

Wszystkie te zasoby są tworzone automatycznie w domyślnym koncie usługi Azure Storage, gdy funkcje programu Orchestrator lub Activity są uruchamiane lub zaplanowane do uruchomienia. W artykule [wydajność i skalowanie](durable-functions-perf-and-scale.md) wyjaśniono, jak te zasoby są używane.

## <a name="task-hub-names"></a>Nazwy centrów zadań

Centra zadań są identyfikowane za pomocą nazwy zadeklarowanej w pliku *host. JSON* , jak pokazano w następującym przykładzie:

### <a name="hostjson-functions-1x"></a>Host. JSON (funkcje 1. x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

### <a name="hostjson-functions-2x"></a>plik host. JSON (Functions 2. x)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

Centra zadań można również skonfigurować za pomocą ustawień aplikacji, jak pokazano w następującym przykładowym pliku *host. JSON* :

### <a name="hostjson-functions-1x"></a>Host. JSON (funkcje 1. x)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-2x"></a>plik host. JSON (Functions 2. x)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

Nazwa centrum zadań zostanie ustawiona na wartość `MyTaskHub` ustawienia aplikacji. Poniżej `local.settings.json` przedstawiono sposób `MyTaskHub` definiowania ustawienia jako `samplehubname`:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

Oto wstępnie skompilowany C# przykład sposobu pisania funkcji korzystającej z [OrchestrationClientBinding](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) do pracy z centrum zadań, które jest skonfigurowane jako ustawienie aplikacji:

### <a name="c"></a>C#

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] DurableOrchestrationClientBase starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

### <a name="javascript"></a>JavaScript

Właściwość centrum zadań w `function.json` pliku jest ustawiana za pośrednictwem ustawienia aplikacji:
```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

Nazwy centrów zadań muszą zaczynać się literą i składać się tylko z liter i cyfr. Jeśli nie zostanie określony, nazwa domyślna to **DurableFunctionsHub**.

> [!NOTE]
> Nazwa różni się od jednego centrum zadań, gdy istnieje wiele centrów zadań na koncie magazynu udostępnionego. Jeśli masz wiele aplikacji funkcji współużytkujących udostępnione konto magazynu, musisz jawnie skonfigurować różne nazwy dla każdego centrum zadań w plikach *host. JSON* . W przeciwnym razie wiele aplikacji funkcji będzie współdziałać ze sobą dla komunikatów, co może spowodować niezdefiniowane zachowanie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak obsługiwać przechowywanie wersji](durable-functions-versioning.md)
