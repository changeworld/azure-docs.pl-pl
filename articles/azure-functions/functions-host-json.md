---
title: Dokumentacja pliku host.JSON dla usługi Azure Functions
description: Dokumentacja pliku host.json usługi Azure Functions.
services: functions
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/12/2018
ms.author: glenga
ms.openlocfilehash: 9043add91022c2829c305425dba9c8f11b224fcf
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2018
ms.locfileid: "39345518"
---
# <a name="hostjson-reference-for-azure-functions"></a>Dokumentacja pliku host.JSON dla usługi Azure Functions

*Host.json* plik metadanych zawiera opcje konfiguracji globalne, które wpływają na wszystkie funkcje dla aplikacji funkcji. Ten artykuł zawiera listę ustawień, które są dostępne. Schemat JSON wynosi http://json.schemastore.org/host.

Istnieją inne opcje konfiguracji globalnej w [ustawienia aplikacji](functions-app-settings.md) i [local.settings.json](functions-run-local.md#local-settings-file) pliku.

## <a name="sample-hostjson-file"></a>Przykładowy plik host.json

Poniższy przykład *host.json* plik zawiera wszystkie możliwe opcje określone.

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

W poniższych sekcjach tego artykułu opisano każdą właściwość najwyższego poziomu. Wszystkie są opcjonalne, o ile nie wskazano inaczej.

## <a name="aggregator"></a>aggregator

Określa, ile wywołania funkcji są agregowane, kiedy [obliczanie metryki dla usługi Application Insights](functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Właściwość |Domyślne  | Opis |
|---------|---------|---------| 
|batchSize|1000|Maksymalna liczba żądań do zagregowania.| 
|flushTimeout|00:00:30|Maksymalny czas okresu do zagregowania.| 

Wywołania funkcji są agregowane, gdy pierwsze z tych limitów osiągnięciu.

## <a name="applicationinsights"></a>applicationInsights

Formanty [próbkowania funkcji w usłudze Application Insights](functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|isEnabled|true|Włącza lub wyłącza próbkowania.| 
|maxTelemetryItemsPerSecond|5|Rozpoczyna się progu, na które próbkowania.| 

## <a name="durabletask"></a>durableTask

Ustawienia konfiguracji dla [funkcje trwałe](durable-functions-overview.md).

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

Nazwy Centrum zadań musi rozpoczynać się literą i składać się wyłącznie z liter i cyfr. Jeśli nie zostanie określony, domyślna nazwa Centrum zadania dla aplikacji funkcji jest **DurableFunctionsHub**. Aby uzyskać więcej informacji, zobacz [koncentratory zadań](durable-functions-task-hubs.md).

|Właściwość  |Domyślne | Opis |
|---------|---------|---------|
|HubName|DurableFunctionsHub|Alternatywne [Centrum zadań](durable-functions-task-hubs.md) nazwy mogą służyć do izolowania wielu aplikacji funkcje trwałe od siebie, nawet wtedy, gdy używają tej samej wewnętrznej bazy danych magazynu.|
|ControlQueueBatchSize|32|Liczba wiadomości do ściągania z kolejki formantu w czasie.|
|PartitionCount |4|Liczba partycji dla kolejki kontroli. Może być dodatnią liczbą całkowitą od 1 do 16.|
|ControlQueueVisibilityTimeout |5 minut|Limit czasu widoczności kontroli usuniętych z kolejki komunikatów w kolejce.|
|WorkItemQueueVisibilityTimeout |5 minut|Limit czasu widoczności wiadomości w kolejce elementu roboczego usuniętych z kolejki.|
|MaxConcurrentActivityFunctions |10 razy liczba procesorów na bieżącej maszynie|Maksymalna liczba funkcji działań, które mogą być przetwarzane jednocześnie w wystąpieniu jednego hosta.|
|MaxConcurrentOrchestratorFunctions |10 razy liczba procesorów na bieżącej maszynie|Maksymalna liczba funkcji działań, które mogą być przetwarzane jednocześnie w wystąpieniu jednego hosta.|
|AzureStorageConnectionStringName |AzureWebJobsStorage|Nazwa ustawienia aplikacji, która ma parametry połączenia usługi Azure Storage, które są używane do zarządzania związanych z nią zasobów usługi Azure Storage.|
|TraceInputsAndOutputs |false|Wartość wskazująca, czy do śledzenia wejść i wyjść wywołania funkcji. Domyślne zachowanie, gdy Śledzenie zdarzeń do wykonywania funkcji ma zawierać liczbę bajtów w serializowane dane wejściowe i wyjściowe dla wywołania funkcji. Zapewnia to minimum informacji o dane wejściowe i wyjściowe wygląd bez nadmiernego zwiększenia rozmiaru dzienników lub przypadkowe udostępnianie poufnych informacji w dziennikach. Ustawienie tej właściwości na wartość true powoduje, że domyślne rejestrowanie funkcji logowania całą zawartość funkcji dane wejściowe i wyjściowe.|
|LogReplayEvents|false|Wartość wskazująca, czy chcesz zapisać aranżacji powtarzania zdarzeń do usługi Application Insights.|
|EventGridTopicEndpoint ||Adres URL punktu końcowego tematu niestandardowego usługi Azure Event Grid. Gdy ta właściwość jest ustawiona, organizowanie cyklu życia powiadomień zdarzenia są publikowane do tego punktu końcowego. Ta właściwość obsługuje rozpoznawanie ustawień aplikacji.|
|EventGridKeySettingName ||Nazwa ustawienia aplikacji zawierającego klucz używany do uwierzytelniania za pośrednictwem tematu niestandardowego usługi Azure Event Grid w `EventGridTopicEndpoint`.|
|EventGridPublishRetryCount|0|Liczba ponownych prób publikowania do tematu usługi Event Grid kończy się niepowodzeniem.|
|EventGridPublishRetryInterval|5 minut|Usługi Event Grid publikowania interwał ponawiania prób w *: mm: ss* formatu.|

Wiele z nich są do optymalizacji wydajności. Aby uzyskać więcej informacji, zobacz [wydajności i skali](durable-functions-perf-and-scale.md).

## <a name="eventhub"></a>eventHub

Ustawienia konfiguracji dla [Centrum zdarzeń wyzwalaczy i powiązań](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

Lista funkcji, które host zadania zostanie uruchomiony. Pusta tablica oznacza, że uruchamianie wszystkich funkcji. Przeznaczony do użytku tylko wtedy, gdy [uruchamiane lokalnie](functions-run-local.md). W aplikacji funkcji, użyj *function.json* `disabled` właściwości zamiast tej właściwości w *host.json*.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Wskazuje wartość limitu czasu dla wszystkich funkcji. W ramach planów zużycia prawidłowy zakres to od 1 sekundy do 10 minut, a wartość domyślna to 5 minut. W planach usługi App Service nie ma żadnego limitu, a wartość domyślna to null, co oznacza brak limitu czasu.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Ustawienia konfiguracji dla [monitor kondycji hosta](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|enabled|true|Czy ta funkcja jest włączona. | 
|healthCheckInterval|10 sekund|Sprawdza, czy odstęp czasu między kondycji tle okresowe. | 
|healthCheckWindow|2 minuty|Przesuwającego się okna czasowego używane w połączeniu z `healthCheckThreshold` ustawienie.| 
|healthCheckThreshold|6|Maksymalna liczba prób kontrola kondycji może zakończyć się niepowodzeniem przed odtwarzanie hosta jest inicjowana.| 
|counterThreshold|0.80|Próg, w którym licznik wydajności będą uznawane za złej kondycji.| 

## <a name="http"></a>http

Ustawienia konfiguracji dla [http wyzwalaczy i powiązań](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

Unikatowy identyfikator dla hosta zadania. Może się od małej litery identyfikatora GUID z kreskami usunięte. Wymagana, gdy uruchamiane lokalnie. Podczas uruchamiania w usłudze Azure Functions, identyfikator jest generowane automatycznie, jeśli `id` zostanie pominięty.

Jeśli udostępniasz konta magazynu przez wiele aplikacji funkcji, upewnij się, że każda aplikacja funkcji ma inną `id`. Możesz pominąć `id` właściwości lub ręcznym ustawieniu każda aplikacja funkcji `id` na inną wartość. Wyzwalacz czasomierza stosowana jest blokada, magazynu, aby upewnić się, że będą istnieć tylko jedno wystąpienie czasomierza w przypadku aplikacji funkcji skalowania do wielu wystąpień. Jeśli dwie aplikacje funkcji współużytkować ten sam `id` i każdy z nich korzysta z wyzwalacza czasomierza, uruchomi timer tylko jeden.


```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

Kontrolki filtrowania dla dzienników napisane przez [obiektu ILogger](functions-monitoring.md#write-logs-in-c-functions) lub [context.log](functions-monitoring.md#write-logs-in-javascript-functions).

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|categoryFilter|Nie dotyczy|Określa, filtrowanie według kategorii| 
|defaultLevel|Informacje|Dla dowolnej kategorii nie jest określony w `categoryLevels` tablicy, Wyślij dzienniki na tym poziomie i nowszych do usługi Application Insights.| 
|categoryLevels|Nie dotyczy|Tablica kategorii określa minimalny poziom rejestrowania do wysłania do usługi Application Insights dla każdej kategorii. Kategoria określone w tym miejscu kontroluje wszystkie kategorie, które zaczynają się od tej samej wartości i wartości dłuższe wyższy priorytet. W poprzednim przykładzie *host.json* pliku wszystkie kategorie, które zaczynają się od "Host.Aggregator" dziennika w `Information` poziom. Wszystkie kategorie, które zaczynają się od "Host", takich jak "Host.Executor" Zaloguj się na `Error` poziom.| 

## <a name="queues"></a>kolejki

Ustawienia konfiguracji dla [magazynu kolejki wyzwalaczy i powiązań](functions-bindings-storage-queue.md).

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="servicebus"></a>serviceBus

Ustawienie konfiguracji [usługi Service Bus wyzwalaczy i powiązań](functions-bindings-service-bus.md).

[!INCLUDE [functions-host-json-service-bus](../../includes/functions-host-json-service-bus.md)]

## <a name="singleton"></a>pojedyncze

Ustawienia konfiguracji dla pojedynczego wystąpienia zachowanie blokad. Aby uzyskać więcej informacji, zobacz [problem w usłudze GitHub o obsłudze pojedyncze](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|lockPeriod|00:00:15|Okres blokady poziomu funkcji są pobierane dla. Blokady automatycznego odnawiania.| 
|listenerLockPeriod|00:01:00|Odbiornik blokady są pobierane dla okresu.| 
|listenerLockRecoveryPollingInterval|00:01:00|Przedział czasu, używane do odzyskania blokady odbiornika, jeśli nie można pobrać blokady odbiornika przy uruchamianiu.| 
|lockAcquisitionTimeout|00:01:00|Maksymalna ilość czasu, środowisko uruchomieniowe podejmie próbę uzyskania blokady.| 
|lockAcquisitionPollingInterval|Nie dotyczy|Interwał między próbami pozyskiwania blokady.| 

## <a name="tracing"></a>Śledzenie

Ustawienia konfiguracji dla dzienników, które tworzysz przy użyciu `TraceWriter` obiektu. Zobacz [C# rejestrowania](functions-reference-csharp.md#logging) i [rejestrowania Node.js](functions-reference-node.md#writing-trace-output-to-the-console). 

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|consoleLevel|informacje|Poziom śledzenia dla rejestrowania konsoli. Opcje to: `off`, `error`, `warning`, `info`, i `verbose`.|
|fileLoggingMode|debugOnly|Poziom śledzenia dla rejestrowania w pliku. Dostępne są opcje `never`, `always`, `debugOnly`.| 

## <a name="watchdirectories"></a>watchDirectories

Zbiór [udostępnionych katalogów kodu](functions-reference-csharp.md#watched-directories) powinny być monitorowane zmian.  Zapewnia, że po zmianie kodu w tych katalogach zmiany są wykrywane przez funkcje.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak można zaktualizować pliku host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Wyświetlane ustawienia globalne w zmiennych środowiskowych](functions-app-settings.md)
