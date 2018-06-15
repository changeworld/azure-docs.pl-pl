---
title: Trwałe funkcji publikowania do siatki zdarzeń Azure (wersja zapoznawcza)
description: Dowiedz się, jak skonfigurować publikowanie automatyczne Azure zdarzeń siatki dla funkcji trwałe.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/20/2018
ms.author: tdykstra
ms.openlocfilehash: 50e517e5719fb102fd91072abe59d3908176278e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33762466"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Trwałe funkcji publikowania do siatki zdarzeń Azure (wersja zapoznawcza)

W tym artykule pokazano, jak można skonfigurować usługi Azure Functions trwałe, aby opublikować zdarzenia cyklu życia orchestration (takich jak utworzone, została zakończona i nie powiodło się) do niestandardowego [temat siatki zdarzenia Azure](https://docs.microsoft.com/en-us/azure/event-grid/overview). 

Poniżej przedstawiono niektóre scenariusze, w których ta funkcja jest przydatna:

* **DevOps scenariuszy, takich jak wdrożenia blue/zielony**: warto wiedzieć, czy wszystkie zadania są uruchomione przed wdrożeniem [strategii wdrażania side-by-side](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-versioning#side-by-side-deployments).

* **Zaawansowana obsługa monitorowania i diagnostyki**: możesz można zachować informacje o informacje o stanie aranżacji w zewnętrznym sklepie zoptymalizowane pod kątem zapytania, takie jak bazy danych SQL lub CosmosDB.

* **Długotrwałe operacje w tle**: Jeśli używasz funkcji trwałe dla działania tła długotrwałe, ta funkcja pomaga wiedzieć przy bieżącym stanie.

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-rc lub nowszej w projekcie trwałe funkcji.
* Zainstaluj [emulatora magazynu Azure](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator).
* Zainstaluj [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest) lub użyj [powłoki chmury Azure](https://docs.microsoft.com/en-us/azure/cloud-shell/overview)

## <a name="create-a-custom-event-grid-topic"></a>Tworzenie niestandardowego tematu siatki zdarzeń

Utwórz temat siatki zdarzeń do wysyłania zdarzeń z funkcji trwałe. Poniższe instrukcje przedstawiają sposób tworzenia tematu przy użyciu wiersza polecenia platformy Azure. Aby uzyskać informacje o tym, jak to zrobić przy użyciu programu PowerShell lub w portalu Azure można znaleźć w następujących artykułach:

* [EventGrid Przewodniki Szybki Start: Tworzenie niestandardowe zdarzenie — PowerShell](https://docs.microsoft.com/en-us/azure/event-grid/custom-event-quickstart-powershell)
* [EventGrid Przewodniki Szybki Start: Tworzenie niestandardowego zdarzenia - portalu Azure](https://docs.microsoft.com/en-us/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz nową grupę zasobów o `az group create` polecenia. Obecnie Usługa zdarzeń siatki nie obsługuje wszystkich regionach. Aby uzyskać informacje o tym, które są obsługiwane regiony, zobacz [Przegląd zdarzeń siatki](https://docs.microsoft.com/en-us/azure/event-grid/overview). 

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Tworzenie tematu niestandardowego

Temat siatki zdarzenia zawiera zdefiniowane przez użytkownika punktu końcowego zakończonych post zdarzenie w celu. Zamień `<topic_name>` na unikatową nazwę tematu. Nazwa tematu musi być unikatowa, ponieważ staje się wpis DNS.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup 
```

## <a name="get-the-endpoint-and-key"></a>Pobieranie punktu końcowego i klucz

Pobierz punktu końcowego tego tematu. Zastąp `<topic_name>` z wybraną nazwę.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Pobierz klucz tematu. Zastąp `<topic_name>` z wybraną nazwę.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Teraz możesz wysłać zdarzenia do tematu.

## <a name="configure-azure-event-grid-publishing"></a>Konfigurowanie publikowania Azure zdarzeń siatki

W projekcie trwałe funkcje Znajdź `host.json` pliku.

Dodaj `EventGridTopicEndpoint` i `EventGridKeySettingName` w `durableTask` właściwości.

```json
{
    "durableTask": {
        "EventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "EventGridKeySettingName": "EventGridKey"
    }
}
```

* **EventGridTopicEndpoint** -punkt końcowy temat siatki zdarzenia.
* **EventGridKeySettingName** — klucz Ustawienia aplikacji na funkcji platformy Azure. Funkcje trwałe otrzyma klucz zdarzenia tematu siatki od wartości.

Po skonfigurowaniu `host.json` pliku Your funkcje trwałe projektu zaczyna wysyłać zdarzenia cyklu życia do tematu zdarzeń siatki. To działanie podczas uruchamiania w aplikacji funkcji lub po uruchomieniu lokalnie.

Ustawienie aplikacji o klucz tematu w aplikacji funkcji i `local.setting.json`. Następujący kod JSON jest przykładowe `local.settings.json` dla debugowania lokalnego. Zastąp `<topic_key>` z klucz tematu.  

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

Upewnij się, że [emulatora magazynu](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator) działa. Jest dobrym rozwiązaniem jest uruchamianie `AzureStorageEmulator.exe clear all` przed wykonaniem polecenia.

## <a name="create-functions-that-listen-for-events"></a>Tworzenie funkcji, które nasłuchiwanie zdarzeń

Tworzenie aplikacji funkcji. Najlepiej można znaleźć go w tym samym regionie co temat siatki zdarzenia.

### <a name="create-an-event-grid-trigger-function"></a>Tworzenie funkcji wyzwalacza zdarzenia siatki

Utwórz funkcję odbierania zdarzeń cyklu życia. Wybierz **funkcji niestandardowej**. 

![Wybierz Tworzenie funkcji niestandardowej.](media/durable-functions-event-publishing/functions-portal.png)

Wybierz polecenie wyzwalacz siatki zdarzeń, a następnie `C#`.

![Wybierz zdarzenia wyzwalacza siatki.](media/durable-functions-event-publishing/eventgrid-trigger.png)

Wprowadź nazwę funkcji, a następnie wybierz `Create`.

![Utwórz wyzwalacz zdarzenia siatki.](media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Utworzono funkcja następującym kodem: 

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

Wybierz pozycję `Add Event Grid Subscription`. Ta operacja doda subskrypcji zdarzeń siatki na temat zdarzeń siatki, który został utworzony. Aby uzyskać więcej informacji, zobacz [pojęcia w siatce zdarzeń platformy Azure](https://docs.microsoft.com/en-us/azure/event-grid/concepts)

![Wybierz łącze wyzwalacza zdarzenia siatki.](media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Wybierz `Event Grid Topics` dla **Typ tematu**. Wybierz grupę zasobów utworzonej dla tematu zdarzeń siatki. Następnie wybierz wystąpienie tematu zdarzeń siatki. Naciśnij klawisz `Create`.

![Tworzy subskrypcję usługi Event Grid.](media/durable-functions-event-publishing/eventsubscription.png)

Teraz możesz przystąpić do odbierania zdarzeń cyklu życia. 

## <a name="create-durable-functions-to-send-the-events"></a>Tworzy trwałe funkcji do wysyłania zdarzeń.

W projekcie trwałe funkcje rozpocząć debugowania na komputerze lokalnym.  Następujący kod jest taki sam, jak kod szablonu funkcji trwałe. Możesz już skonfigurowany `host.json` i `local.settings.json` na komputerze lokalnym. 

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
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
        public static string SayHello([ActivityTrigger] string name, TraceWriter log)
        {
            log.Info($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
            [OrchestrationClient]DurableOrchestrationClient starter,
            TraceWriter log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.Info($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

Jeśli należy wywołać `Sample_HttpStart` Postman lub przeglądarki, funkcja trwałe rozpoczyna wysyłanie zdarzeń cyklu życia. Punkt końcowy jest zwykle `http://localhost:7071/api/Sample_HttpStart` dla debugowania lokalnego.

Można znaleźć w dziennikach funkcji utworzone w portalu Azure.

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

## <a name="event-schema"></a>Schematu zdarzeń

Poniżej wyjaśniono schematu zdarzenia cyklu życia:

* **Identyfikator**: Unikatowy identyfikator dla zdarzenia zdarzeń siatki.
* **temat**: ścieżka do podmiotu zdarzeń. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` będzie `Running`, `Completed`, `Failed`, i `Terminated`.  
* **dane**: trwałe parametrów określonych funkcji.
    * **hubName**: [TaskHub](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-task-hubs) nazwy.
    * **functionName**: Nazwa funkcji programu Orchestrator.
    * **Identyfikator wystąpienia**: Identyfikator funkcji trwałego wystąpienia.
    * **Przyczyna**: dodatkowe dane skojarzone ze zdarzeniem śledzenia. Aby uzyskać więcej informacji, zobacz [diagnostyki w funkcjach trwałe (usługi Azure Functions)](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-diagnostics)
    * **runtimeStatus**: aranżacji stanu w czasie wykonywania. Uruchomiona zakończona, nie powiodło się, anulowane. 
* **Typ zdarzenia**: "orchestratorEvent"
* **eventTime**: czas trwania zdarzenia (UTC).
* **dataVersion**: wersja schematu zdarzeń cyklu życia.
* **Element metadataVersion**: wersja metadanych.
* **temat**: EventGrid temat zasobu.

## <a name="how-to-test-locally"></a>Jak przetestować lokalnie

Aby przetestować lokalnie, należy użyć [ngrok](functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, Zarządzanie wystąpieniami w funkcjach trwałych](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej wersji w funkcjach trwałych](durable-functions-versioning.md)
