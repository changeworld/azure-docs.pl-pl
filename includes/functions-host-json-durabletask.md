---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d430d7d94f8eed76bb78042a174aeddf2e6ccaa3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75410206"
---
Ustawienia konfiguracji dla [Durable Functions](../articles/azure-functions/durable-functions-overview.md).

### <a name="durable-functions-1x"></a>Durable Functions 1. x

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "controlQueueBatchSize": 32,
    "partitionCount": 4,
    "controlQueueVisibilityTimeout": "00:05:00",
    "workItemQueueVisibilityTimeout": "00:05:00",
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "maxQueuePollingInterval": "00:00:30",
    "azureStorageConnectionStringName": "AzureWebJobsStorage",
    "trackingStoreConnectionStringName": "TrackingStorage",
    "trackingStoreNamePrefix": "DurableTask",
    "traceInputsAndOutputs": false,
    "logReplayEvents": false,
    "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName":  "EventGridKey",
    "eventGridPublishRetryCount": 3,
    "eventGridPublishRetryInterval": "00:00:30",
    "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
  }
}
```

### <a name="durable-functions-2-0-host-json"></a>Durable Functions 2. x

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "workItemQueueVisibilityTimeout": "00:05:00",
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useGracefulShutdown": false
  }
}
```

Nazwy centrów zadań muszą zaczynać się literą i składać się tylko z liter i cyfr. Jeśli nie zostanie określony, domyślną nazwą centrum zadań dla aplikacji funkcji jest **DurableFunctionsHub**. Aby uzyskać więcej informacji, zobacz [centra zadań](../articles/azure-functions/durable-functions-task-hubs.md).

|Właściwość  |Domyślne | Opis |
|---------|---------|---------|
|hubName|DurableFunctionsHub|Alternatywne nazwy [centrów zadań](../articles/azure-functions/durable-functions-task-hubs.md) mogą służyć do izolowania wielu Durable Functions aplikacji od siebie, nawet jeśli używają tego samego zaplecza magazynu.|
|controlQueueBatchSize|32|Liczba komunikatów do ściągnięcia z kolejki kontroli w danym momencie.|
|controlQueueBufferThreshold|256|Liczba komunikatów w kolejce sterującej, które mogą być buforowane w pamięci w danym momencie, gdy Dyspozytor będzie oczekiwać przed dekolejkowanie wszelkich dodatkowych komunikatów.|
|partitionCount |4|Liczba partycji dla kolejki sterującej. Może to być dodatnia liczba całkowita z zakresu od 1 do 16.|
|controlQueueVisibilityTimeout |5 minut|Przekroczenie limitu czasu widoczności komunikatów w kolejce sterującej.|
|workItemQueueVisibilityTimeout |5 minut|Przekroczenie limitu czasu widoczności komunikatów w kolejce elementów roboczych.|
|maxConcurrentActivityFunctions |10X liczbę procesorów na bieżącym komputerze|Maksymalna liczba funkcji działania, które mogą być przetwarzane współbieżnie na jednym wystąpieniu hosta.|
|maxConcurrentOrchestratorFunctions |10X liczbę procesorów na bieżącym komputerze|Maksymalna liczba funkcji programu Orchestrator, które mogą być przetwarzane współbieżnie na jednym wystąpieniu hosta.|
|maxQueuePollingInterval|30 sekund|Maksymalny interwał sondowania kolejki elementów roboczych i kontrolki w formacie *gg: mm: SS* . Wyższe wartości mogą skutkować większymi opóźnieniami przetwarzania komunikatów. Niższe wartości mogą spowodować wyższe koszty magazynowania z powodu zwiększonych transakcji magazynu.|
|azureStorageConnectionStringName |AzureWebJobsStorage|Nazwa ustawienia aplikacji, które zawiera parametry połączenia usługi Azure Storage używane do zarządzania bazowymi zasobami usługi Azure Storage.|
|trackingStoreConnectionStringName||Nazwa parametrów połączenia do użycia w tabelach historia i wystąpienia. Jeśli nie zostanie określony, zostanie użyte połączenie `azureStorageConnectionStringName`.|
|trackingStoreNamePrefix||Prefiks, który ma być używany dla tabel historia i wystąpienia, jeśli określono `trackingStoreConnectionStringName`. Jeśli nie zostanie ustawiona, domyślną wartością prefiksu będzie `DurableTask`. Jeśli `trackingStoreConnectionStringName` nie zostanie określony, tabele historia i wystąpienia będą używały `hubName` wartości jako ich prefiksu, a wszystkie ustawienia dla `trackingStoreNamePrefix` zostaną zignorowane.|
|traceInputsAndOutputs |false|Wartość wskazująca, czy należy śledzić dane wejściowe i wyjściowe wywołań funkcji. Zachowanie domyślne podczas śledzenia zdarzeń wykonania funkcji polega na uwzględnieniu liczby bajtów w serializowanych danych wejściowych i wyjściowych dla wywołań funkcji. Takie zachowanie zapewnia minimalną ilość informacji o tym, co się stało z danymi wejściowymi i wyjściowymi, bez przeładowania dzienników lub przypadkowo ujawnia informacje poufne. Ustawienie tej właściwości na wartość true powoduje, że funkcja domyślna rejestrowania w usłudze rejestruje całą zawartość danych wejściowych i wyjściowych funkcji.|
|logReplayEvents|false|Wartość wskazująca, czy należy zapisywać zdarzenia powtarzania aranżacji do Application Insights.|
|eventGridTopicEndpoint ||Adres URL Azure Event Grid niestandardowego punktu końcowego tematu. Po ustawieniu tej właściwości zdarzenia powiadomień cyklu życia aranżacji są publikowane w tym punkcie końcowym. Ta właściwość obsługuje rozwiązanie ustawień aplikacji.|
|eventGridKeySettingName ||Nazwa ustawienia aplikacji zawierającego klucz używany do uwierzytelniania za pomocą niestandardowego tematu Azure Event Grid w `EventGridTopicEndpoint`.|
|eventGridPublishRetryCount|0|Liczba ponownych prób w przypadku, gdy publikowanie w temacie Event Grid nie powiedzie się.|
|eventGridPublishRetryInterval|5 minut|Event Grid publikuje interwał ponawiania prób w formacie *gg: mm: SS* .|
|eventGridPublishEventTypes||Lista typów zdarzeń do opublikowania w Event Grid. Jeśli nie zostanie określony, zostaną opublikowane wszystkie typy zdarzeń. Dozwolone wartości to `Started`, `Completed`, `Failed``Terminated`.|
|useGracefulShutdown|false|Przeglądania Włącz bezpieczne zamykanie, aby zmniejszyć prawdopodobieństwo niepowodzenia zamykania funkcji w procesie.|

Wiele z tych ustawień umożliwia optymalizację wydajności. Aby uzyskać więcej informacji, zobacz [wydajność i skalowanie](../articles/azure-functions/durable-functions-perf-and-scale.md).
