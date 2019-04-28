---
title: Dokumentacja pliku host.JSON dla usługi Azure Functions 1.x
description: Dokumentacja pliku host.json usługi Azure Functions ze środowiskiem uruchomieniowym w wersji 1.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: glenga
ms.openlocfilehash: 44bc5a245d1bcbc8ff53991af4193ef86f7cd704
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107078"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>Dokumentacja pliku host.JSON dla usługi Azure Functions 1.x

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Wersja 1](functions-host-json-v1.md)
> * [Wersja 2](functions-host-json.md)

*Host.json* plik metadanych zawiera opcje konfiguracji globalne, które wpływają na wszystkie funkcje dla aplikacji funkcji. Ten artykuł zawiera listę ustawień, które są dostępne dla środowiska uruchomieniowego w wersji 1. Schemat JSON wynosi http://json.schemastore.org/host.

> [!NOTE]
> Ten artykuł jest przeznaczony dla usługi Azure Functions 1.x.  Odwołanie host.json w funkcjach 2.x, zobacz [dokumentacja pliku host.JSON dla usługi Azure Functions 2.x](functions-host-json.md).

Inne opcje konfiguracji aplikacji funkcji odbywa się w Twojej [ustawienia aplikacji](functions-app-settings.md).

Niektóre ustawienia host.json są używane tylko podczas uruchamiania lokalnego w [local.settings.json](functions-run-local.md#local-settings-file) pliku.

## <a name="sample-hostjson-file"></a>Przykładowy plik host.json

Poniższy przykład *host.json* pliki mają wszystkie możliwe opcje określone.


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

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

Ustawienia konfiguracji dla [Centrum zdarzeń wyzwalaczy i powiązań](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

Lista funkcji, które uruchamia hosta zadania. Pusta tablica oznacza, że uruchamianie wszystkich funkcji. Przeznaczony do użytku tylko wtedy, gdy [uruchamiane lokalnie](functions-run-local.md). W aplikacji funkcji na platformie Azure, należy zamiast tego wykonaj czynności opisane w [jak wyłączyć funkcje w usłudze Azure Functions](disable-function.md) wyłączenie określonych funkcji, a nie przy użyciu tego ustawienia.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Wskazuje wartość limitu czasu dla wszystkich funkcji. Bez użycia serwera planu zużycie prawidłowy zakres to od 1 sekundy do 10 minut, a wartość domyślna to 5 minut. W ramach planu usługi App Service nie ma limitu całkowitej, a wartość domyślna jest zależna od wersji środowiska uruchomieniowego.

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
|enabled|true|Określa, czy ta funkcja jest włączona. | 
|healthCheckInterval|10 sekund|Sprawdza, czy odstęp czasu między kondycji tle okresowe. | 
|healthCheckWindow|2 minuty|Przesuwającego się okna czasowego używane w połączeniu z `healthCheckThreshold` ustawienie.| 
|healthCheckThreshold|6|Maksymalna liczba prób kontrola kondycji może zakończyć się niepowodzeniem przed odtwarzanie hosta jest inicjowana.| 
|counterThreshold|0.80|Próg, w którym licznik wydajności będą uznawane za złej kondycji.| 

## <a name="http"></a>http

Ustawienia konfiguracji dla [http wyzwalaczy i powiązań](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

*W wersji 1.x tylko.*

Unikatowy identyfikator dla hosta zadania. Może się od małej litery identyfikatora GUID z kreskami usunięte. Wymagana, gdy uruchamiane lokalnie. Podczas uruchamiania na platformie Azure, zalecamy nie ustawisz wartość Identyfikatora. Identyfikator jest generowana automatycznie na platformie Azure podczas `id` zostanie pominięty. 

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

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|maxPollingInterval|60000|Maksymalny interwał w milisekundach między sondowaniami kolejki.| 
|visibilityTimeout|0|Odstęp czasu między kolejnymi próbami podczas przetwarzania komunikatu nie powiedzie się.| 
|batchSize|16|Liczba komunikatów w kolejce, które środowisko uruchomieniowe usługi Functions pobiera jednocześnie przetwarzane równolegle. Gdy liczba przetwarzanych wyświetlona w dół `newBatchThreshold`, środowisko uruchomieniowe inna partia pobiera i uruchamia przetwarzanie tych wiadomości. Dlatego jest maksymalna liczba współbieżnych komunikatów przetwarzanych dla każdej funkcji `batchSize` oraz `newBatchThreshold`. Ten limit dotyczy oddzielnie poszczególnych funkcji wyzwalanej przez kolejkę. <br><br>Jeśli chcesz uniknąć wykonywania równoległego dla wiadomości otrzymanych w jednej kolejki można ustawić `batchSize` 1. Jednak to ustawienie pozwala wyeliminować współbieżności tylko tak długo, jak aplikacja funkcji zostanie uruchomiona na jednej maszynie wirtualnej (VM). Jeśli aplikacja funkcji skalowania do wielu maszyn wirtualnych, każda maszyna wirtualna może uruchomić jedno wystąpienie każdej funkcji wyzwalanej przez kolejkę.<br><br>Maksymalna `batchSize` wynosi 32. | 
|maxDequeueCount|5|Liczba prób przetwarzania komunikatu przed jego przeniesieniem do skażone kolejki.| 
|newBatchThreshold|batchSize/2|Zawsze, gdy liczba komunikatów przetwarzanych jednocześnie uzyskuje na ten numer, środowisko uruchomieniowe pobiera inna partia.| 

## <a name="servicebus"></a>serviceBus

Ustawienie konfiguracji [usługi Service Bus wyzwalaczy i powiązań](functions-bindings-service-bus.md).

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|maxConcurrentCalls|16|Maksymalna liczba równoczesnych wywołań wywołanie zwrotne, które powinno zainicjować "pompy komunikatów". Domyślnie środowisko uruchomieniowe usługi Functions przetwarza wiele wiadomości jednocześnie. Aby skierowania czasu przetwarzania tylko jednej kolejki lub tematu wiadomości w czasie, należy ustawić `maxConcurrentCalls` 1. | 
|prefetchCount|Nie dotyczy|Domyślnie PrefetchCount używanej przez MessageReceiver bazowego.| 
|autoRenewTimeout|00:05:00|Maksymalny czas trwania, w którym blokadę komunikatu zostanie odnowiony automatycznie.| 

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

*W wersji 1.x*

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
