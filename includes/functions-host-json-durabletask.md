---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 497552edaad2a35d58bc8b3f05533afcc5f399e2
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67568359"
---
Ustawienia konfiguracji dla [funkcje trwałe](../articles/azure-functions/durable-functions-overview.md).

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

Nazwy Centrum zadań musi rozpoczynać się literą i składać się wyłącznie z liter i cyfr. Jeśli nie zostanie określony, domyślna nazwa Centrum zadania dla aplikacji funkcji jest **DurableFunctionsHub**. Aby uzyskać więcej informacji, zobacz [koncentratory zadań](../articles/azure-functions/durable-functions-task-hubs.md).

|Właściwość  |Domyślne | Opis |
|---------|---------|---------|
|hubName|DurableFunctionsHub|Alternatywne [Centrum zadań](../articles/azure-functions/durable-functions-task-hubs.md) nazwy mogą służyć do izolowania wielu aplikacji funkcje trwałe od siebie, nawet wtedy, gdy przy użyciu tej samej wewnętrznej bazy danych magazynu.|
|controlQueueBatchSize|32|Liczba wiadomości do ściągania z kolejki formantu w czasie.|
|partitionCount |4|Liczba partycji dla kolejki kontroli. Może być dodatnią liczbą całkowitą od 1 do 16.|
|controlQueueVisibilityTimeout |5 minut|Limit czasu widoczności kontroli usuniętych z kolejki komunikatów w kolejce.|
|workItemQueueVisibilityTimeout |5 minut|Limit czasu widoczności wiadomości w kolejce elementu roboczego usuniętych z kolejki.|
|maxConcurrentActivityFunctions |10 razy liczba procesorów na bieżącej maszynie|Maksymalna liczba funkcji działań, które mogą być przetwarzane jednocześnie w wystąpieniu jednego hosta.|
|maxConcurrentOrchestratorFunctions |10 razy liczba procesorów na bieżącej maszynie|Maksymalna liczba funkcji programu orchestrator, które mogą być przetwarzane jednocześnie w wystąpieniu jednego hosta.|
|maxQueuePollingInterval|30 sekund|Maksymalną kontrolę i interwału sondowania w kolejce elementu roboczego w *: mm: ss* formatu. Wyższe wartości może spowodować wyższe przetwarzania opóźnienia komunikatów. Ze względu na transakcje magazynu zwiększone niższe wartości może spowodować wyższe koszty magazynowania.|
|azureStorageConnectionStringName |AzureWebJobsStorage|Nazwa ustawienia aplikacji, która ma parametry połączenia usługi Azure Storage, które są używane do zarządzania związanych z nią zasobów usługi Azure Storage.|
|trackingStoreConnectionStringName||Nazwa parametrów połączenia do użycia w przypadku tabel historii i wystąpień. Jeśli nie zostanie określony, `azureStorageConnectionStringName` połączenia jest używana.|
|trackingStoreNamePrefix||Prefiks używany dla historii i wystąpień tabele, kiedy `trackingStoreConnectionStringName` jest określony. Jeśli nie jest ustawiona, wartość domyślna prefiks będzie `DurableTask`. Jeśli `trackingStoreConnectionStringName` nie zostanie określony, a następnie użyje tabel historii i wystąpień `hubName` wartość jako ich prefiksu i wszystkie ustawienia dla `trackingStoreNamePrefix` zostaną zignorowane.|
|TraceInputsAndOutputs |false|Wartość wskazująca, czy do śledzenia wejść i wyjść wywołania funkcji. Domyślne zachowanie, gdy Śledzenie zdarzeń do wykonywania funkcji ma zawierać liczbę bajtów w serializowane dane wejściowe i wyjściowe dla wywołania funkcji. To zachowanie zawiera minimalne informacje o dane wejściowe i wyjściowe wygląd bez nadmiernego zwiększenia rozmiaru dzienników lub przypadkowe udostępnianie poufnych informacji. Ustawienie tej właściwości na wartość true powoduje, że domyślne rejestrowanie funkcji logowania całą zawartość funkcji dane wejściowe i wyjściowe.|
|LogReplayEvents|false|Wartość wskazująca, czy chcesz zapisać aranżacji powtarzania zdarzeń do usługi Application Insights.|
|eventGridTopicEndpoint ||Adres URL punktu końcowego tematu niestandardowego usługi Azure Event Grid. Gdy ta właściwość jest ustawiona, organizowanie cyklu życia powiadomień zdarzenia są publikowane do tego punktu końcowego. Ta właściwość obsługuje rozpoznawanie ustawień aplikacji.|
|eventGridKeySettingName ||Nazwa ustawienia aplikacji zawierającego klucz używany do uwierzytelniania za pośrednictwem tematu niestandardowego usługi Azure Event Grid w `EventGridTopicEndpoint`.|
|eventGridPublishRetryCount|0|Liczba ponownych prób publikowania do tematu usługi Event Grid kończy się niepowodzeniem.|
|eventGridPublishRetryInterval|5 minut|Usługi Event Grid publikuje interwał ponawiania prób w *: mm: ss* formatu.|
|eventGridPublishEventTypes||Lista typów zdarzeń, aby opublikować w usłudze Event Grid. Jeśli nie zostanie określony, wszystkie typy zdarzeń zostaną opublikowane. Dozwolone wartości to `Started`, `Completed`, `Failed`, `Terminated`.|

Wiele z tych ustawień są do optymalizacji wydajności. Aby uzyskać więcej informacji, zobacz [wydajności i skali](../articles/azure-functions/durable-functions-perf-and-scale.md).
