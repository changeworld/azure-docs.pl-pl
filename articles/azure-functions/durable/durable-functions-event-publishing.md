---
title: Durable Functions publikowania do Azure Event Grid (wersja zapoznawcza)
description: Dowiedz się, jak skonfigurować automatyczne publikowanie Azure Event Grid dla Durable Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: glenga
ms.openlocfilehash: f3fd59c0d17bd9094f6887aa5ec088f9fdcdd979
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734433"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Durable Functions publikowania do Azure Event Grid (wersja zapoznawcza)

W tym artykule przedstawiono sposób konfigurowania Durable Functions publikowania zdarzeń cyklu życia aranżacji (takich jak utworzone, ukończone i niepowodzenie) w temacie niestandardowym [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).

Poniżej przedstawiono kilka scenariuszy, w których ta funkcja jest przydatna:

* **Scenariusze DevOps, takie jak wdrożenia Blue/Green**: Przed wdrożeniem [strategii wdrażania równoległego](durable-functions-versioning.md#side-by-side-deployments)warto wiedzieć, czy jakieś zadania są uruchomione.

* **Zaawansowana obsługa monitorowania i diagnostyki**: Informacje o stanie aranżacji można śledzić w zewnętrznym magazynie zoptymalizowanym pod kątem zapytań, takich jak SQL Database lub CosmosDB.

* **Długotrwałe działanie w tle**: Jeśli używasz Durable Functions do długotrwałego działania w tle, ta funkcja pomaga znać bieżący stan.

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [Microsoft. Azure. WebJobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-RC lub nowszy w projekcie Durable Functions.
* Zainstaluj [emulator usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-use-emulator).
* Instalowanie [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) lub korzystanie z [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)

## <a name="create-a-custom-event-grid-topic"></a>Tworzenie niestandardowego tematu siatki zdarzeń

Utwórz temat usługi Event Grid na potrzeby wysyłania zdarzeń z Durable Functions. Poniższe instrukcje przedstawiają sposób tworzenia tematu za pomocą interfejsu wiersza polecenia platformy Azure. Aby uzyskać informacje o tym, jak to zrobić za pomocą programu PowerShell lub Azure Portal, zapoznaj się z następującymi artykułami:

* [Przewodniki Szybki Start EventGrid: Tworzenie zdarzenia niestandardowego — PowerShell](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-powershell)
* [Przewodniki Szybki Start EventGrid: Tworzenie zdarzenia niestandardowego — Azure Portal](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia `az group create`. Obecnie Azure Event Grid nie obsługuje wszystkich regionów. Aby uzyskać informacje o obsługiwanych regionach, zobacz [omówienie Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Tworzenie tematu niestandardowego

Temat GridY zdarzeń zawiera zdefiniowany przez użytkownika punkt końcowy, w którym jest ogłaszane wydarzenie. Zamień `<topic_name>` na unikatową nazwę tematu. Nazwa tematu musi być unikatowa, ponieważ jest wpisem DNS.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Pobieranie punktu końcowego i klucza

Pobierz punkt końcowy tematu. Zamień `<topic_name>` na wybraną nazwę.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Pobierz klucz tematu. Zamień `<topic_name>` na wybraną nazwę.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Teraz możesz wysyłać zdarzenia do tematu.

## <a name="configure-azure-event-grid-publishing"></a>Konfigurowanie publikowania Azure Event Grid

W projekcie Durable Functions Znajdź `host.json` plik.

Dodaj `eventGridTopicEndpoint` i `eventGridKeySettingName` we`durableTask` właściwości.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

Możliwe właściwości konfiguracji Azure Event Grid można znaleźć w [dokumentacji pliku host. JSON](../functions-host-json.md#durabletask). Po skonfigurowaniu `host.json` pliku aplikacja funkcji wysyła zdarzenia cyklu życia do tematu w usłudze Event Grid. Działa to w przypadku uruchamiania aplikacji funkcji zarówno lokalnie, jak i na platformie Azure. ""

Ustaw ustawienie aplikacji dla klucza tematu w aplikacja funkcji i `local.setting.json`. Poniższy kod JSON jest przykładem `local.settings.json` dla debugowania lokalnego. Zamień `<topic_key>` na klucz tematu.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

Upewnij się, że [emulator magazynu](https://docs.microsoft.com/azure/storage/common/storage-use-emulator) działa. Dobrym pomysłem jest uruchomienie `AzureStorageEmulator.exe clear all` polecenia przed wykonaniem.

## <a name="create-functions-that-listen-for-events"></a>Tworzenie funkcji nasłuchujących zdarzeń

Utwórz aplikacja funkcji. Najlepiej znaleźć go w tym samym regionie, co w temacie Event Grid.

### <a name="create-an-event-grid-trigger-function"></a>Tworzenie funkcji wyzwalacza z siatką zdarzeń

Utwórz funkcję do odbierania zdarzeń cyklu życia. Wybierz pozycję **Funkcja niestandardowa**.

![Wybierz opcję Utwórz funkcję niestandardową.](./media/durable-functions-event-publishing/functions-portal.png)

Wybierz wyzwalacz Event Grid i wybierz opcję `C#`.

![Wybierz wyzwalacz Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Wprowadź nazwę funkcji, a następnie wybierz pozycję `Create`.

![Utwórz wyzwalacz Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Zostanie utworzona funkcja o następującym kodzie:

#### <a name="precompiled-c"></a>PrekompilowanegoC#
```csharp
public static void Run([HttpTrigger] JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

#### <a name="c-script"></a>C#Napisy

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

public static void Run(JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

Wybierz pozycję `Add Event Grid Subscription`. Ta operacja dodaje subskrypcję usługi Event Grid dla utworzonego tematu usługi Event Grid. Aby uzyskać więcej informacji, zobacz [pojęcia w Azure Event Grid](https://docs.microsoft.com/azure/event-grid/concepts)

![Wybierz łącze wyzwalacz Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Wybierz `Event Grid Topics` dla **typu tematu**. Wybierz grupę zasobów utworzoną dla tematu usługi Event Grid. Następnie wybierz wystąpienie tematu siatka zdarzeń. Naciśnij `Create`klawisz.

![Tworzy subskrypcję usługi Event Grid.](./media/durable-functions-event-publishing/eventsubscription.png)

Teraz możesz przystąpić do odbierania zdarzeń cyklu życia.

## <a name="create-durable-functions-to-send-the-events"></a>Utwórz Durable Functions, aby wysłać zdarzenia

W projekcie Durable Functions Rozpocznij debugowanie na komputerze lokalnym.  Poniższy kod jest taki sam jak kod szablonu dla Durable Functions. Masz już `host.json` skonfigurowaną `local.settings.json` i na komputerze lokalnym.

### <a name="precompiled-c"></a>PrekompilowanegoC#

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] DurableOrchestrationContext context)
        {
            var outputs = new List<string>();

            // Replace "hello" with the name of your Durable Activity Function.
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("Sample_Hello")]
        public static string SayHello([ActivityTrigger] string name, ILogger log)
        {
            log.LogInformation($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestMessage req,
            [OrchestrationClient] DurableOrchestrationClient starter,
            ILogger log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

Jeśli wywołasz polecenie `Sample_HttpStart` za pomocą programu Poster lub przeglądarki, funkcja trwała zacznie wysyłać zdarzenia cyklu życia. Punkt końcowy jest zazwyczaj `http://localhost:7071/api/Sample_HttpStart` przeznaczony do debugowania lokalnego.

Zapoznaj się z dziennikami z funkcji utworzonej w Azure Portal.

```
2018-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2018-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2018-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2018-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2018-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2018-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2018-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2018-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Schemat zdarzenia

Na poniższej liście opisano schemat zdarzeń cyklu życia:

* **`id`** : Unikatowy identyfikator zdarzenia usługi Event Grid.
* **`subject`** : Ścieżka do tematu zdarzenia. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}``Running`to ,`Completed`,,i. `Failed` `Terminated`  
* **`data`** : Durable Functions określone parametry.
  * **`hubName`** : Nazwa [TaskHub](durable-functions-task-hubs.md) .
  * **`functionName`** : Nazwa funkcji programu Orchestrator.
  * **`instanceId`** : Durable Functions identyfikator wystąpienia.
  * **`reason`** : Dodatkowe dane skojarzone ze zdarzeniem śledzenia. Aby uzyskać więcej informacji, zobacz [Diagnostyka w Durable Functions (Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`** : Stan środowiska uruchomieniowego aranżacji. Uruchomione, zakończone, zakończone niepowodzeniem, anulowane.
* **`eventType`** : "orchestratorEvent"
* **`eventTime`** : Czas zdarzenia (UTC).
* **`dataVersion`** : Wersja schematu zdarzeń cyklu życia.
* **`metadataVersion`** :  Wersja metadanych.
* **`topic`** : Zasób tematu w usłudze Event Grid.

## <a name="how-to-test-locally"></a>Jak testować lokalnie

Aby przetestować lokalnie, użyj [ngrok](../functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj Zarządzanie wystąpieniami w Durable Functions](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Informacje o wersji w Durable Functions](durable-functions-versioning.md)