---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6bb59db4c1b31033b1e116742dedc94621b1c60d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80116879"
---
Ustawienia konfiguracji [funkcji trwałych](../articles/azure-functions/durable-functions-overview.md).

### <a name="durable-functions-1x"></a>Funkcje trwałe 1.x

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

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Funkcje trwałe 2.x

```json
{
 "extensions": {
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
}

```

Nazwy centrum zadań muszą zaczynać się od litery i składać się tylko z liter i cyfr. Jeśli nie zostanie określona, domyślną nazwą centrum zadań dla aplikacji funkcji jest **DurableFunctionsHub**. Aby uzyskać więcej informacji, zobacz [Centra zadań](../articles/azure-functions/durable-functions-task-hubs.md).

|Właściwość  |Domyślne | Opis |
|---------|---------|---------|
|nazwa centrum|DurableFunctionsHub (Funkcje trwałe)|Alternatywne nazwy [centrum zadań](../articles/azure-functions/durable-functions-task-hubs.md) może służyć do izolowania wielu funkcji trwałe aplikacje od siebie, nawet jeśli są one przy użyciu tej samej wewnętrznej bazy danych magazynu.|
|controlQueueBatchSize (Rozmiar wyszczelić)|32|Liczba wiadomości do wyciągnięcia z kolejki kontrolnej w czasie.|
|kontrolaQueueBufferThreshold|256|Liczba komunikatów kolejki kontroli, które mogą być buforowane w pamięci w czasie, w którym to momencie dyspozytor będzie czekać przed usunięciem w kolejce żadnych dodatkowych komunikatów.|
|partitionCount |4|Liczba partycji dla kolejki formantu. Może być dodatnią liczą całkowitą między 1 a 16.|
|controlQueueVisibilityTimeout |5 minut|Limit czasu widoczności komunikatów kolejki sterującej de-sejka.|
|workItemQueueVisibilityTimeout |5 minut|Limit czasu widoczności komunikatów kolejki elementów roboczych de-sejka.|
|maxConcurrentActivityFunkcje |10-krotnie więcej niż liczba procesorów na obecnym komputerze|Maksymalna liczba funkcji działania, które mogą być przetwarzane jednocześnie na jednym wystąpieniu hosta.|
|maxConcurrentOrchestratorFunkcje |10-krotnie więcej niż liczba procesorów na obecnym komputerze|Maksymalna liczba funkcji koordynatora, które mogą być przetwarzane jednocześnie na jednym wystąpieniu hosta.|
|maxQueuePollingInterval|30 sekund|Maksymalny interwał sondowania kolejki formantów i elementów roboczych w formacie *hh:mm:ss.* Wyższe wartości mogą spowodować wyższe opóźnienia przetwarzania wiadomości. Niższe wartości mogą spowodować wyższe koszty magazynowania z powodu zwiększonych transakcji magazynu.|
|azureStorageConnectionStringName |AzureWebJobsStorage|Nazwa ustawienia aplikacji, która ma ciąg połączenia usługi Azure Storage używany do zarządzania podstawowymi zasobami usługi Azure Storage.|
|trackingStoreConnectionStringName||Nazwa ciągu połączenia do użycia w tabelach Historia i Wystąpienia. Jeśli nie zostanie `azureStorageConnectionStringName` określony, połączenie jest używane.|
|trackingStoreNamePrefix||Prefiks do użycia w tabelach `trackingStoreConnectionStringName` Historia i Wystąpienia, gdy jest określony. Jeśli nie jest ustawiona, domyślną wartością prefiksu będzie `DurableTask`. Jeśli `trackingStoreConnectionStringName` nie zostanie określony, a następnie historia `hubName` i wystąpienia tabele użyje `trackingStoreNamePrefix` wartości jako ich prefiks, a każde ustawienie dla zostaną zignorowane.|
|traceInputsAndOutputs |false|Wartość wskazująca, czy mają być śledzeni dane wejściowe i wyjściowe wywołań funkcji. Domyślne zachowanie podczas śledzenia zdarzeń wykonywania funkcji jest uwzględnienie liczby bajtów w serializowanych danych wejściowych i wyjściowych dla wywołań funkcji. To zachowanie zawiera minimalne informacje o tym, jak wyglądają dane wejściowe i wyjściowe bez wzdęcia dzienników lub nieumyślnego ujawnienia poufnych informacji. Ustawienie tej właściwości na true powoduje, że domyślne rejestrowanie funkcji do rejestrowania całej zawartości wejść i wyjść funkcji.|
|logReplayEvents|false|Wartość wskazująca, czy chcesz napisać zdarzenia odtwarzania aranżacji do usługi Application Insights.|
|eventGridTopicEndpoint ||Adres URL punktu końcowego tematu niestandardowego usługi Azure Event Grid. Po ustawieniu tej właściwości zdarzenia powiadomień o cyklu życia aranżacji są publikowane w tym punkcie końcowym. Ta właściwość obsługuje rozpoznawanie ustawień aplikacji.|
|eventGridKeyName ||Nazwa ustawienia aplikacji zawierającego klucz używany do uwierzytelniania w temacie `EventGridTopicEndpoint`niestandardowym usługi Azure Event Grid pod adresem .|
|eventGridPublishRetryCountCount|0|Liczba ponownych prób, jeśli publikowanie w temacie siatki zdarzeń nie powiedzie się.|
|eventGridPublishRetryInterval|5 minut|Siatka zdarzeń publikuje interwał ponawiania prób w formacie *hh:mm:ss.*|
|eventGridPublishEventTypes||Lista typów zdarzeń do opublikowania w uszmia zdarzenia. Jeśli nie zostanie określony, wszystkie typy zdarzeń zostaną opublikowane. Dozwolone wartości `Started`obejmują `Completed` `Failed`, `Terminated`, , .|
|użyjGracefulShutdown|false|(Wersja zapoznawcza) Włącz bezpiecznie zamykanie, aby zmniejszyć ryzyko zamykania hosta, które nie wykonują wykonywania funkcji w procesie.|

Wiele z tych ustawień jest przeznaczonych do optymalizacji wydajności. Aby uzyskać więcej informacji, zobacz [Wydajność i skala](../articles/azure-functions/durable-functions-perf-and-scale.md).
