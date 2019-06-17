---
title: Durable Functions publikowanie do usługi Azure Event Grid (wersja zapoznawcza)
description: Dowiedz się, jak skonfigurować publikowanie automatyczne usługi Azure Event Grid dla funkcje trwałe.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: glenga
ms.openlocfilehash: c07a42349fbd81a46b1b7cd9bcad1978f891a6b2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60733773"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Durable Functions publikowanie do usługi Azure Event Grid (wersja zapoznawcza)

W tym artykule pokazano, jak skonfigurować funkcje trwałe do publikowania zdarzeń cyklu życia aranżacji (takie jak utworzone, ukończone i zakończone niepowodzeniem) niestandardowego [tematu usługi Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).

Poniżej przedstawiono kilka scenariuszy, w których ta funkcja jest przydatna:

* **Scenariuszy DevOps, takich jak wdrażanie niebieskie/zielone**: Warto wiedzieć, czy wszystkie zadania podrzędne są uruchomione przed wdrożeniem [strategii wdrażania side-by-side](durable-functions-versioning.md#side-by-side-deployments).

* **Zaawansowana obsługa monitorowania i diagnostyki**: Użytkownik może zachować informacje o informacje o stanie aranżacji w magazynie zewnętrznych zoptymalizowanej pod kątem zapytań, takich jak bazy danych SQL lub bazy danych cosmos DB.

* **Długotrwałe operacje w tle**: Jeśli używasz funkcje trwałe długotrwałe operacje w tle, ta funkcja pomaga znać bieżący stan.

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-rc lub nowszym w projekcie funkcje trwałe.
* Zainstaluj [emulatora usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-use-emulator).
* Zainstaluj [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) lub użyj [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)

## <a name="create-a-custom-event-grid-topic"></a>Tworzenie zdarzenia niestandardowego tematu siatki

Tworzenie tematu usługi event grid związane z przesyłaniem zdarzeń przez funkcje trwałe. Poniższe instrukcje przedstawiają sposób tworzenia tematu przy użyciu wiersza polecenia platformy Azure. Informacje o tym, jak to zrobić przy użyciu programu PowerShell lub witryny Azure portal można znaleźć w następujących artykułach:

* [EventGrid przewodników Szybki Start: Tworzenie zdarzenia niestandardowego — program PowerShell](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-powershell)
* [EventGrid przewodników Szybki Start: Tworzenie zdarzenia niestandardowego — witryna Azure portal](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia `az group create`. Obecnie usługa Azure Event Grid nie obsługuje we wszystkich regionach. Aby uzyskać informacje o tym, jakie regiony są obsługiwane, zobacz [Omówienie usługi Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Tworzenie tematu niestandardowego

Temat usługi event grid udostępnia zdefiniowany przez użytkownika punkt końcowy, gdy opublikujesz zdarzenie w celu. Zamień `<topic_name>` na unikatową nazwę tematu. Nazwa tematu musi być unikatowa, ponieważ staje się wpis DNS.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Pobierz klucz i punkt końcowy

Pobierz punkt końcowy tego tematu. Zastąp `<topic_name>` z wybraną nazwę.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Pobierz klucz tematu. Zastąp `<topic_name>` z wybraną nazwę.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Teraz możesz wysłać zdarzenia do tematu.

## <a name="configure-azure-event-grid-publishing"></a>Skonfiguruj publikowanie w usłudze Azure Event Grid

W projekcie funkcje trwałe znaleźć `host.json` pliku.

Dodaj `eventGridTopicEndpoint` i `eventGridKeySettingName` w `durableTask` właściwości.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

Możliwych właściwości konfiguracji usługi Azure Event Grid można znaleźć w [dokumentacji host.json](../functions-host-json.md#durabletask). Po skonfigurowaniu `host.json` pliku aplikacji funkcji wysyła zdarzenia cyklu życia do tematu usługi event grid. To działa, po uruchomieniu aplikacji funkcji, zarówno lokalnie, jak i na platformie Azure. ""

Określ odpowiednie ustawienie aplikacji o klucz tematu w aplikacji funkcji i `local.setting.json`. Następujące dane JSON znajduje się przykład `local.settings.json` dla debugowania lokalnego. Zastąp `<topic_key>` kluczem tematu.  

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

Upewnij się, że [emulatora magazynu](https://docs.microsoft.com/azure/storage/common/storage-use-emulator) działa. To dobry pomysł, aby uruchomić `AzureStorageEmulator.exe clear all` polecenia przed wykonaniem.

## <a name="create-functions-that-listen-for-events"></a>Tworzenie funkcji, które nasłuchują zdarzeń

Tworzenie aplikacji funkcji. Zaleca się go zlokalizować w tym samym regionie, co tematu usługi event grid.

### <a name="create-an-event-grid-trigger-function"></a>Tworzenie funkcji wyzwalacza siatki zdarzeń

Utwórz funkcję odbierania zdarzeń cyklu życia. Wybierz **funkcję niestandardową**.

![Wybierz opcję Utwórz funkcję niestandardową.](./media/durable-functions-event-publishing/functions-portal.png)

Wybieranie wyzwalacza usługi Event Grid, a następnie wybierz pozycję `C#`.

![Wybieranie wyzwalacza usługi Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Wprowadź nazwę funkcji, a następnie wybierz pozycję `Create`.

![Tworzenie wyzwalacza usługi Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Zostanie utworzona funkcja następującym kodem:

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

Wybierz pozycję `Add Event Grid Subscription`. Ta operacja spowoduje dodanie subskrypcję siatki zdarzeń dla tematu siatki zdarzeń, który został utworzony. Aby uzyskać więcej informacji, zobacz [pojęcia w usłudze Azure Event Grid](https://docs.microsoft.com/azure/event-grid/concepts)

![Wybierz łącze wyzwalacza usługi Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Wybierz `Event Grid Topics` dla **Typ tematu**. Wybierz grupę zasobów utworzoną dla tematu usługi event grid. Następnie wybierz wystąpienie tematu usługi event grid. Naciśnij klawisz `Create`.

![Tworzy subskrypcję usługi Event Grid.](./media/durable-functions-event-publishing/eventsubscription.png)

Teraz możesz przystąpić do odbierania zdarzeń cyklu życia.

## <a name="create-durable-functions-to-send-the-events"></a>Tworzyć niezawodne funkcje do wysyłania zdarzeń

W projekcie funkcje trwałe Rozpocznij debugowanie na komputerze lokalnym.  Poniższy kod jest taki sam jak kod szablonu funkcji trwałe. Możesz już skonfigurowane `host.json` i `local.settings.json` na komputerze lokalnym.

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
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
            [OrchestrationClient]DurableOrchestrationClient starter,
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

Jeśli wywołasz `Sample_HttpStart` Postman lub przeglądarki trwałość funkcja rozpoczyna do wysyłania zdarzenia cyklu życia. Punkt końcowy jest zwykle `http://localhost:7071/api/Sample_HttpStart` dla debugowania lokalnego.

Zobacz dzienniki z funkcji, który został utworzony w witrynie Azure portal.

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

## <a name="event-schema"></a>Schemat zdarzeń

Poniżej opisano schemat zdarzenia cyklu życia:

* **`id`** : Unikatowy identyfikator zdarzenia siatki zdarzeń.
* **`subject`** : Ścieżka do tematu zdarzenia. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` będzie `Running`, `Completed`, `Failed`, i `Terminated`.  
* **`data`** : Trwałe funkcje określonymi parametrami.
  * **`hubName`** : [TaskHub](durable-functions-task-hubs.md) nazwy.
  * **`functionName`** : Nazwa funkcji programu orchestrator.
  * **`instanceId`** : Trwałe funkcje identyfikator wystąpienia.
  * **`reason`** : Dodatkowe dane skojarzone ze zdarzeniem śledzenia. Aby uzyskać więcej informacji, zobacz [Diagnostyka funkcje trwałe (usługi Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`** : Stan czasu wykonywania aranżacji. Uruchomione, zakończone, nie powiodło się, zostało anulowane.
* **`eventType`** : "orchestratorEvent"
* **`eventTime`** : Czas zdarzenia (UTC).
* **`dataVersion`** : Wersja schematu zdarzeń cyklu życia.
* **`metadataVersion`** :  Wersja metadanych.
* **`topic`** : Zasób tematu siatki zdarzeń.

## <a name="how-to-test-locally"></a>Jak przetestować ją lokalnie

Aby przetestować ją lokalnie, należy użyć [ngrok](../functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, Zarządzanie wystąpieniami w funkcje trwałe](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Dowiedz się, przechowywanie wersji w funkcje trwałe](durable-functions-versioning.md)
