---
title: Durable Functions publikowania do Azure Event Grid (wersja zapoznawcza)
description: Dowiedz się, jak skonfigurować automatyczne publikowanie Azure Event Grid dla Durable Functions.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: f0fbb46320b896008b6a1343357f016a9f57b0fe
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231445"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Durable Functions publikowania do Azure Event Grid (wersja zapoznawcza)

W tym artykule przedstawiono sposób konfigurowania Durable Functions publikowania zdarzeń cyklu życia aranżacji (takich jak utworzone, ukończone i niepowodzenie) w temacie niestandardowym [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).

Poniżej przedstawiono kilka scenariuszy, w których ta funkcja jest przydatna:

* **DevOps scenariusze, takie jak wdrożenia Blue/Green**: możesz chcieć wiedzieć, czy jakieś zadania są uruchomione przed implementacją [strategii wdrażania Side-by-Side](durable-functions-versioning.md#side-by-side-deployments).

* **Zaawansowana obsługa monitorowania i diagnostyki**: można śledzić informacje o stanie aranżacji w zewnętrznym magazynie zoptymalizowanym pod kątem zapytań, takich jak SQL Database lub CosmosDB.

* **Długotrwałe działanie w tle**: jeśli używasz Durable Functions do długotrwałego działania w tle, ta funkcja pomaga znać bieżący stan.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [Microsoft. Azure. WebJobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) w projekcie Durable Functions.
* Zainstaluj [emulator usługi Azure Storage](../../storage/common/storage-use-emulator.md).
* Instalowanie [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) lub korzystanie z [Azure Cloud Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>Tworzenie niestandardowego tematu siatki zdarzeń

Utwórz temat usługi Event Grid na potrzeby wysyłania zdarzeń z Durable Functions. Poniższe instrukcje przedstawiają sposób tworzenia tematu za pomocą interfejsu wiersza polecenia platformy Azure. Aby uzyskać informacje o tym, jak to zrobić za pomocą programu PowerShell lub Azure Portal, zapoznaj się z następującymi artykułami:

* [Przewodniki Szybki Start EventGrid: Tworzenie zdarzenia niestandardowego — PowerShell](../../event-grid/custom-event-quickstart-powershell.md)
* [Przewodniki Szybki Start EventGrid: Tworzenie zdarzenia niestandardowego — Azure Portal](../../event-grid/custom-event-quickstart-portal.md)

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia `az group create`. Obecnie Azure Event Grid nie obsługuje wszystkich regionów. Aby uzyskać informacje o obsługiwanych regionach, zobacz [omówienie Azure Event Grid](../../event-grid/overview.md).

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

W projekcie Durable Functions Znajdź plik `host.json`.

Dodaj `eventGridTopicEndpoint` i `eventGridKeySettingName` we właściwości `durableTask`.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

Możliwe właściwości konfiguracji Azure Event Grid można znaleźć w [dokumentacji pliku host. JSON](../functions-host-json.md#durabletask). Po skonfigurowaniu pliku `host.json` aplikacja funkcji wysyła zdarzenia cyklu życia do tematu w usłudze Event Grid. Działa to w przypadku uruchamiania aplikacji funkcji zarówno lokalnie, jak i na platformie Azure. ""

W aplikacja funkcji i `local.setting.json`Ustaw ustawienie aplikacji dla klucza tematu. Poniższy kod JSON jest przykładem `local.settings.json` na potrzeby debugowania lokalnego. Zastąp `<topic_key>` kluczem tematu.  

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

Upewnij się, że [emulator magazynu](../../storage/common/storage-use-emulator.md) działa. Dobrym pomysłem jest uruchomienie `AzureStorageEmulator.exe clear all` polecenia przed wykonaniem.

## <a name="create-functions-that-listen-for-events"></a>Tworzenie funkcji nasłuchujących zdarzeń

Utwórz aplikacja funkcji. Najlepiej znaleźć go w tym samym regionie, co w temacie Event Grid.

### <a name="create-an-event-grid-trigger-function"></a>Tworzenie funkcji wyzwalacza z siatką zdarzeń

Utwórz funkcję do odbierania zdarzeń cyklu życia. Wybierz pozycję **Funkcja niestandardowa**.

![Wybierz opcję Utwórz funkcję niestandardową.](./media/durable-functions-event-publishing/functions-portal.png)

Wybierz wyzwalacz Event Grid i wybierz pozycję `C#`.

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

Wybierz `Event Grid Topics` dla **typu tematu**. Wybierz grupę zasobów utworzoną dla tematu usługi Event Grid. Następnie wybierz wystąpienie tematu siatka zdarzeń. Naciśnij `Create`.

![Tworzy subskrypcję usługi Event Grid.](./media/durable-functions-event-publishing/eventsubscription.png)

Teraz możesz przystąpić do odbierania zdarzeń cyklu życia.

## <a name="create-durable-functions-to-send-the-events"></a>Utwórz Durable Functions, aby wysłać zdarzenia

W projekcie Durable Functions Rozpocznij debugowanie na komputerze lokalnym.  Poniższy kod jest taki sam jak kod szablonu dla Durable Functions. `host.json` i `local.settings.json` na komputerze lokalnym zostały już skonfigurowane.

### <a name="precompiled-c"></a>PrekompilowanegoC#

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] IDurableOrchestrationContext context)
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
            [DurableClient] IDurableOrchestrationClient starter,
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

> [!NOTE]
> Poprzedni kod jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext``OrchestrationClient` atrybutu zamiast atrybutu `DurableClient` i należy użyć `DurableOrchestrationClient` typu parametru zamiast `IDurableOrchestrationClient`. Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

Jeśli wywołasz `Sample_HttpStart` za pomocą programu Poster lub przeglądarki, funkcja trwała zacznie wysyłać zdarzenia cyklu życia. Punkt końcowy jest zwykle `http://localhost:7071/api/Sample_HttpStart` na potrzeby debugowania lokalnego.

Zapoznaj się z dziennikami z funkcji utworzonej w Azure Portal.

```
2019-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2019-04-20T09:28:21.104 [Info] {
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
    "eventTime": "2019-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2019-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2019-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2019-04-20T09:28:37.098 [Info] {
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
    "eventTime": "2019-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2019-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Schemat zdarzeń

Na poniższej liście opisano schemat zdarzeń cyklu życia:

* **`id`** : unikatowy identyfikator zdarzenia usługi Event Grid.
* **`subject`** : ścieżka do tematu zdarzenia. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` będzie `Running`, `Completed`, `Failed`i `Terminated`.  
* **`data`** : Durable Functions określone parametry.
  * **`hubName`** : nazwa [TaskHub](durable-functions-task-hubs.md) .
  * **`functionName`** : nazwa funkcji programu Orchestrator.
  * **`instanceId`** : Durable Functions identyfikator wystąpienia.
  * **`reason`** : dodatkowe dane skojarzone ze zdarzeniem śledzenia. Aby uzyskać więcej informacji, zobacz [Diagnostyka w Durable Functions (Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`** : stan środowiska uruchomieniowego aranżacji. Uruchomione, zakończone, zakończone niepowodzeniem, anulowane.
* **`eventType`** : "orchestratorEvent"
* **`eventTime`** : czas zdarzenia (UTC).
* **`dataVersion`** : wersja schematu zdarzenia cyklu życia.
* **`metadataVersion`** : wersja metadanych.
* **`topic`** : zasób tematu zdarzenia w usłudze Event Grid.

## <a name="how-to-test-locally"></a>Jak testować lokalnie

Aby przetestować lokalnie, użyj [ngrok](../functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj Zarządzanie wystąpieniami w Durable Functions](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Informacje o wersji w Durable Functions](durable-functions-versioning.md)