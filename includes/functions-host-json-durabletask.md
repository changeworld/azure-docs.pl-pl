---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 92eb13165326f44432f09322ea97f3cee5ccec2b
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50251247"
---
Ustawienia konfiguracji dla [funkcje trwałe](../articles/azure-functions/durable-functions-overview.md).

```json
{
  "durableTask": {
    "HubName": "MyTaskHub",
    "ControlQueueBatchSize": 32,
    "PartitionCount": 4,
    "ControlQueueVisibilityTimeout": "00:05:00",
    "WorkItemQueueVisibilityTimeout": "00:05:00",
    "MaxConcurrentActivityFunctions": 10,
    "MaxConcurrentOrchestratorFunctions": 10,
    "AzureStorageConnectionStringName": "AzureWebJobsStorage",
    "TraceInputsAndOutputs": false,
    "LogReplayEvents": false,
    "EventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "EventGridKeySettingName":  "EventGridKey",
    "EventGridPublishRetryCount": 3,
    "EventGridPublishRetryInterval": "00:00:30"
  }
}
```

Nazwy Centrum zadań musi rozpoczynać się literą i składać się wyłącznie z liter i cyfr. Jeśli nie zostanie określony, domyślna nazwa Centrum zadania dla aplikacji funkcji jest **DurableFunctionsHub**. Aby uzyskać więcej informacji, zobacz [koncentratory zadań](../articles/azure-functions/durable-functions-task-hubs.md).

|Właściwość  |Domyślne | Opis |
|---------|---------|---------|
|HubName|DurableFunctionsHub|Alternatywne [Centrum zadań](../articles/azure-functions/durable-functions-task-hubs.md) nazwy mogą być używane do izolowania wielu aplikacji funkcje trwałe od siebie, nawet wtedy, gdy oni są przy użyciu tej samej wewnętrznej bazy danych magazynu.|
|ControlQueueBatchSize|32|Liczba wiadomości do ściągania z kolejki formantu w czasie.|
|PartitionCount |4|Liczba partycji dla kolejki kontroli. Może być dodatnią liczbą całkowitą od 1 do 16.|
|ControlQueueVisibilityTimeout |5 minut|Limit czasu widoczności kontroli usuniętych z kolejki komunikatów w kolejce.|
|WorkItemQueueVisibilityTimeout |5 minut|Limit czasu widoczności wiadomości w kolejce elementu roboczego usuniętych z kolejki.|
|MaxConcurrentActivityFunctions |10 razy liczba procesorów na bieżącej maszynie|Maksymalna liczba funkcji działań, które mogą być przetwarzane jednocześnie w wystąpieniu jednego hosta.|
|MaxConcurrentOrchestratorFunctions |10 razy liczba procesorów na bieżącej maszynie|Maksymalna liczba funkcji programu orchestrator, które mogą być przetwarzane jednocześnie w wystąpieniu jednego hosta.|
|AzureStorageConnectionStringName |AzureWebJobsStorage|Nazwa ustawienia aplikacji, która ma parametry połączenia usługi Azure Storage, które są używane do zarządzania związanych z nią zasobów usługi Azure Storage.|
|TraceInputsAndOutputs |false|Wartość wskazująca, czy do śledzenia wejść i wyjść wywołania funkcji. Domyślne zachowanie, gdy Śledzenie zdarzeń do wykonywania funkcji ma zawierać liczbę bajtów w serializowane dane wejściowe i wyjściowe dla wywołania funkcji. Zapewnia to minimum informacji o dane wejściowe i wyjściowe wygląd bez nadmiernego zwiększenia rozmiaru dzienników lub przypadkowe udostępnianie poufnych informacji w dziennikach. Ustawienie tej właściwości na wartość true powoduje, że domyślne rejestrowanie funkcji logowania całą zawartość funkcji dane wejściowe i wyjściowe.|
|LogReplayEvents|false|Wartość wskazująca, czy chcesz zapisać aranżacji powtarzania zdarzeń do usługi Application Insights.|
|EventGridTopicEndpoint ||Adres URL punktu końcowego tematu niestandardowego usługi Azure Event Grid. Gdy ta właściwość jest ustawiona, organizowanie cyklu życia powiadomień zdarzenia są publikowane do tego punktu końcowego. Ta właściwość obsługuje rozpoznawanie ustawień aplikacji.|
|EventGridKeySettingName ||Nazwa ustawienia aplikacji zawierającego klucz używany do uwierzytelniania za pośrednictwem tematu niestandardowego usługi Azure Event Grid w `EventGridTopicEndpoint`.|
|EventGridPublishRetryCount|0|Liczba ponownych prób publikowania do tematu usługi Event Grid kończy się niepowodzeniem.|
|EventGridPublishRetryInterval|5 minut|Usługi Event Grid publikuje interwał ponawiania prób w *: mm: ss* formatu.|

Wiele z nich są do optymalizacji wydajności. Aby uzyskać więcej informacji, zobacz [wydajności i skali](../articles/azure-functions/durable-functions-perf-and-scale.md).