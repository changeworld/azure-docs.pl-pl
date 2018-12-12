---
title: Koncentratory zadań w funkcje trwałe - Azure
description: Dowiedz się, jakie Centrum zadanie trwa rozszerzenia funkcji trwałych dla usługi Azure Functions. Dowiedz się, jak skonfigurować konfigurowanie koncentratory zadań.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 68771362c1b3904453eb7c32f58d28122e8660c3
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52869471"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Koncentratory zadań w funkcje trwałe (usługi Azure Functions)

A *Centrum zadań* w [funkcje trwałe](durable-functions-overview.md) to kontener logiczny dla zasobów usługi Azure Storage, które są używane do aranżacji. Funkcje programu orchestrator i działanie tylko można ze sobą współdziałać, jeśli należą do tego samego Centrum zadania.

Każda aplikacja funkcji ma Centrum osobne zadanie. Jeśli wiele aplikacji funkcyjnych udostępniać konta magazynu, konto magazynu zawiera wiele centrów zadania. Na poniższym diagramie przedstawiono koncentratorze zadania na aplikację funkcji w ramach kont magazynu udostępnione i dedykowane.

![Diagram przedstawiający udostępnione i dedykowane konta magazynu.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Zasoby usługi Azure Storage

Koncentrator zadanie składa się z następującymi zasobami magazynu: 

* Jedną lub więcej kolejek kontroli.
* Jedna kolejka elementu roboczego.
* Historia jednej tabeli.
* Jedna tabela wystąpień.
* Jeden kontener magazynu zawierające co najmniej jednego obiektu blob dzierżawy.

Wszystkie te zasoby są tworzone automatycznie w domyślne konto usługi Azure Storage uruchamiania lub zaplanowane do uruchomienia programu orchestrator lub działanie funkcji. [Wydajności i skali](durable-functions-perf-and-scale.md) artykule wyjaśniono, jak te zasoby są używane.

## <a name="task-hub-names"></a>Nazwy Centrum zadań

Koncentratory zadań są identyfikowane przez nazwę, która jest zadeklarowana w *host.json* pliku, jak pokazano w poniższym przykładzie:

### <a name="hostjson-functions-v1"></a>Host.JSON (funkcje v1)
```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```
### <a name="hostjson-functions-v2"></a>Host.JSON (funkcje w wersji 2)
```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "HubName": "MyTaskHub"
    }
  }
}
```
Koncentratory zadań można również skonfigurować przy użyciu ustawień aplikacji, jak pokazano w następującym *host.json* przykładowy plik:

### <a name="hostjson-functions-v1"></a>Host.JSON (funkcje v1)
```json
{
  "durableTask": {
    "HubName": "%MyTaskHub%"
  }
}
```
### <a name="hostjson-functions-v2"></a>Host.JSON (funkcje w wersji 2)
```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "HubName": "%MyTaskHub%"
    }
  }
}
```
Nazwa koncentratora zadania zostanie ustawiona na wartość `MyTaskHub` ustawienia aplikacji. Następujące `local.settings.json` ilustruje sposób definiowania `MyTaskHub` jako `samplehubname`:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" :  "samplehubname" 
  }
}
```

Poniżej przedstawiono wstępnie skompilowanych C# przykład sposobu pisania funkcji, która używa [OrchestrationClientBinding](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) do pracy z Centrum zadania, który jest skonfigurowany jako ustawienia aplikacji:

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
I poniżej wymaganej konfiguracji dla języka JavaScript. Właściwość Centrum zadania w `function.json` plik jest ustawiony za pomocą ustawienia aplikacji:

```javascript
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

Nazwy Centrum zadań musi rozpoczynać się literą i składać się wyłącznie z liter i cyfr. Jeśli nie zostanie określony, domyślną nazwą jest **DurableFunctionsHub**.

> [!NOTE]
> Nazwa to, co odróżnia koncentratorze zadania z innego gdy wiele centrów zadań znajduje się na koncie magazynu udostępnionego. Jeśli masz wiele aplikacji funkcji udostępniania na koncie magazynu udostępnionego, należy skonfigurować różne nazwy dla każdego zadania Centrum *host.json* plików.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Informacje o sposobie obsługi przechowywania wersji](durable-functions-versioning.md)
