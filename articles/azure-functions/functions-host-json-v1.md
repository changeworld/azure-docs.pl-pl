---
title: Dokumentacja pliku host. JSON dla Azure Functions 1. x
description: Dokumentacja referencyjna dotycząca pliku host. JSON Azure Functions przy użyciu środowiska uruchomieniowego v1.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: glenga
ms.openlocfilehash: c169d9cc774a2c6264ba1520240005f13ba9d2da
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096452"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>Dokumentacja pliku host. JSON dla Azure Functions 1. x

> [!div class="op_single_selector" title1="Wybierz wersję środowiska uruchomieniowego Azure Functions, którego używasz: "]
> * [Wersja 1](functions-host-json-v1.md)
> * [Wersja 2](functions-host-json.md)

Plik metadanych *hosta. JSON* zawiera globalne opcje konfiguracji, które mają wpływ na wszystkie funkcje aplikacji funkcji. W tym artykule wymieniono ustawienia dostępne dla środowiska uruchomieniowego v1. Schemat JSON ma wartość http://json.schemastore.org/host.

> [!NOTE]
> Ten artykuł jest przeznaczony dla usługi Azure Functions 1.x.  Aby uzyskać odwołanie do pliku host. JSON w funkcjach 2. x, zobacz informacje dotyczące pliku [host. JSON dla Azure Functions 2. x](functions-host-json.md).

Inne opcje konfiguracji aplikacji funkcji są zarządzane w [ustawieniach aplikacji](functions-app-settings.md).

Niektóre ustawienia pliku host. JSON są używane tylko w przypadku uruchamiania lokalnego w pliku [Local. Settings. JSON](functions-run-local.md#local-settings-file) .

## <a name="sample-hostjson-file"></a>Przykładowy plik host. JSON

W poniższych przykładowych plikach *hosta. JSON* dostępne są wszystkie możliwe opcje.


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

Ustawienia konfiguracji dla [wyzwalaczy i powiązań centrum zdarzeń](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>— funkcje

Lista funkcji uruchomionych przez hosta zadań. Pusta tablica oznacza uruchamianie wszystkich funkcji. Przeznaczone do użytku tylko w przypadku [uruchamiania lokalnego](functions-run-local.md). W aplikacjach funkcji na platformie Azure zamiast tego należy wykonać kroki opisane w temacie [Jak wyłączyć funkcje w Azure Functions](disable-function.md) , aby wyłączyć określone funkcje zamiast używać tego ustawienia.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Wskazuje czas trwania dla wszystkich funkcji. W planie zużycia bezserwerowego prawidłowy zakres to od 1 sekundy do 10 minut, a wartość domyślna to 5 minut. W planie App Service nie ma żadnego całkowitego limitu i domyślnie zależy od wersji środowiska uruchomieniowego.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Ustawienia konfiguracji dla [monitora kondycji hosta](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

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
|enabled|true|Określa, czy funkcja jest włączona. | 
|healthCheckInterval|10 sekund|Przedział czasu między okresowymi kontrolami kondycji w tle. | 
|healthCheckWindow|2 minuty|Przedział czasu, który jest używany w połączeniu `healthCheckThreshold` z ustawieniem.| 
|healthCheckThreshold|6|Maksymalna liczba przypadków, w których Sprawdzenie kondycji może zakończyć się niepowodzeniem przed zainicjowaniem odtwarzania hosta.| 
|counterThreshold|0,80|Próg, w którym licznik wydajności będzie traktowany jako w złej kondycji.| 

## <a name="http"></a>http

Ustawienia konfiguracji dla [wyzwalaczy i powiązań http](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

*Tylko wersja 1. x.*

Unikatowy identyfikator dla hosta zadania. Może to być dolny identyfikator GUID przypadku z usuniętymi kreskami. Wymagane w przypadku uruchamiania lokalnego. W przypadku uruchamiania na platformie Azure zalecamy, aby nie ustawić wartości identyfikatora. Identyfikator jest generowany automatycznie na platformie Azure, `id` gdy zostanie pominięty. 

W przypadku udostępniania konta magazynu w wielu aplikacjach funkcji upewnij się, że każda aplikacja funkcji ma inną `id`wartość. Możesz pominąć `id` właściwość lub ręcznie ustawić każdą `id` aplikację funkcji na inną wartość. Wyzwalacz czasomierza korzysta z blokady magazynu, aby upewnić się, że po przeskalowaniu aplikacji funkcji na wiele wystąpień będzie istnieć tylko jedno wystąpienie czasomierza. Jeśli dwie aplikacje funkcji współużytkują `id` te same i każdy z nich używa wyzwalacza czasomierza, zostanie uruchomiony tylko jeden czasomierz.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

Kontroluje filtrowanie dla dzienników pisanych przez [obiekt ILogger](functions-monitoring.md#write-logs-in-c-functions) lub według [kontekstu. log](functions-monitoring.md#write-logs-in-javascript-functions).

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
|categoryFilter|Nie dotyczy|Określa filtrowanie według kategorii| 
|defaultLevel|Information|Dla wszystkich kategorii, które nie są `categoryLevels` określone w tablicy, Wyślij dzienniki na tym poziomie i powyżej, aby Application Insights.| 
|categoryLevels|Nie dotyczy|Tablica kategorii, która określa minimalny poziom rejestrowania do wysłania do Application Insights dla każdej kategorii. Określona tutaj Kategoria kontroluje wszystkie kategorie, które zaczynają się od tej samej wartości, a dłuższe wartości mają pierwszeństwo. W poprzednim przykładzie pliku *host. JSON* wszystkie kategorie zaczynające się od dziennika "host. agregator" na `Information` poziomie. Wszystkie inne kategorie zaczynające się od "host", takie jak "host. wykonawca", `Error` log na poziomie.| 

## <a name="queues"></a>tworzone

Ustawienia konfiguracji dla [wyzwalaczy i powiązań kolejki magazynu](functions-bindings-storage-queue.md).

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
|maxPollingInterval|60000|Maksymalny interwał w milisekundach między sondowaniem kolejki.| 
|visibilityTimeout|0|Przedział czasu między ponownymi próbami, gdy przetwarzanie komunikatu kończy się niepowodzeniem.| 
|batchSize|16|Liczba komunikatów w kolejce, które środowisko uruchomieniowe funkcji jednocześnie pobiera i przetwarza równolegle. Gdy przetwarzana liczba jest w dół `newBatchThreshold`, środowisko uruchomieniowe pobiera kolejną partię i uruchamia przetwarzanie tych komunikatów. Dlatego Maksymalna liczba współbieżnych komunikatów przetwarzanych na funkcję to `batchSize` Plus. `newBatchThreshold` Ten limit dotyczy osobno każdej funkcji wyzwalanej przez kolejkę. <br><br>Aby zapobiec równoległemu wykonywaniu komunikatów odebranych w jednej kolejce, można ustawić wartość `batchSize` 1. To ustawienie eliminuje jednak współbieżność tylko wtedy, gdy aplikacja funkcji działa na jednej maszynie wirtualnej. Jeśli aplikacja funkcji jest skalowana do wielu maszyn wirtualnych, każda maszyna wirtualna może uruchomić jedno wystąpienie każdej funkcji wyzwalanej przez kolejkę.<br><br>Wartość maksymalna `batchSize` to 32. | 
|maxDequeueCount|5|Liczba prób przetworzenia komunikatu przed przeniesieniem go do kolejki trującej.| 
|newBatchThreshold|batchSize/2|Gdy liczba przetwarzanych komunikatów współbieżnie przyjdzie do tego numeru, środowisko uruchomieniowe pobiera kolejną partię.| 

## <a name="servicebus"></a>serviceBus

Ustawienia konfiguracji dla [wyzwalaczy Service Bus i powiązań](functions-bindings-service-bus.md).

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
|Właściwość autorenewtimeout|00:05:00|Maksymalny czas trwania, w którym blokadę komunikatu zostanie odnowiony automatycznie.| 

## <a name="singleton"></a>pojedynczego

Ustawienia konfiguracji dla zachowania pojedynczej blokady. Aby uzyskać więcej informacji, zobacz [temat problem z usługą GitHub dotyczący pojedynczej pomocy technicznej](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

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
|lockPeriod|00:00:15|Okres, w którym są wykonywane blokady poziomu funkcji. Blokady autorenew.| 
|listenerLockPeriod|00:01:00|Okres, w którym są wykonywane blokady odbiornika.| 
|listenerLockRecoveryPollingInterval|00:01:00|Przedział czasu używany do odzyskiwania blokady odbiornika, jeśli nie można uzyskać blokady odbiornika podczas uruchamiania.| 
|lockAcquisitionTimeout|00:01:00|Maksymalny czas, przez jaki środowisko uruchomieniowe podejmie próbę uzyskania blokady.| 
|lockAcquisitionPollingInterval|Nie dotyczy|Interwał między kolejnymi próbami przejęcia blokady.| 

## <a name="tracing"></a>pochodzenia

*Wersja 1. x*

Ustawienia konfiguracji dla dzienników utworzonych przy użyciu `TraceWriter` obiektu. Zobacz [ C# rejestrowanie](functions-reference-csharp.md#logging) i [Rejestrowanie w węźle Node. js](functions-reference-node.md#writing-trace-output-to-the-console).

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
|consoleLevel|info|Poziom śledzenia dla rejestrowania konsoli. Dostępne są następujące `off`opcje `error`: `warning`, `info`,, `verbose`i.|
|fileLoggingMode|debugOnly|Poziom śledzenia dla rejestrowania plików. Dostępne opcje `never`to `always`, `debugOnly`,.| 

## <a name="watchdirectories"></a>watchDirectories

Zestaw [udostępnionych katalogów kodu](functions-reference-csharp.md#watched-directories) , które powinny być monitorowane pod kątem zmian.  Zapewnia, że gdy kod w tych katalogach zostanie zmieniony, zmiany są pobierane przez funkcje.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o aktualizowaniu pliku host. JSON](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Zobacz ustawienia globalne w zmiennych środowiskowych](functions-app-settings.md)
