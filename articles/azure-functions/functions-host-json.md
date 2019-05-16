---
title: Dokumentacja pliku host.JSON dla usługi Azure Functions 2.x
description: Dokumentacja pliku host.json usługi Azure Functions ze środowiskiem uruchomieniowym w wersji 2.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: glenga
ms.openlocfilehash: ddd3b0889eedd55f809dbb57b2ef41a2ae3f9c94
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521394"
---
# <a name="hostjson-reference-for-azure-functions-2x"></a>Dokumentacja pliku host.JSON dla usługi Azure Functions 2.x  

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Wersja 1](functions-host-json-v1.md)
> * [Wersja 2](functions-host-json.md)

*Host.json* plik metadanych zawiera opcje konfiguracji globalne, które wpływają na wszystkie funkcje dla aplikacji funkcji. Ten artykuł zawiera listę ustawień, które są dostępne dla środowiska uruchomieniowego w wersji 2.  

> [!NOTE]
> Ten artykuł jest przeznaczony dla usługi Azure Functions 2.x.  Odwołanie host.json w funkcjach 1.x, zobacz [dokumentacja pliku host.JSON dla usługi Azure Functions 1.x](functions-host-json-v1.md).

Inne opcje konfiguracji aplikacji funkcji odbywa się w Twojej [ustawienia aplikacji](functions-app-settings.md).

Niektóre ustawienia host.json są używane tylko podczas uruchamiania lokalnego w [local.settings.json](functions-run-local.md#local-settings-file) pliku.

## <a name="sample-hostjson-file"></a>Przykładowy plik host.json

Poniższy przykład *host.json* pliki mają wszystkie możliwe opcje określone.

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
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
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 5
            }
        }
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ],
    "managedDependency": {
        "enabled": true
    }
}
```

W poniższych sekcjach tego artykułu opisano każdą właściwość najwyższego poziomu. Wszystkie są opcjonalne, o ile nie wskazano inaczej.

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

To ustawienie jest elementem podrzędnym [rejestrowania](#logging).

Formanty [próbkowania funkcji w usłudze Application Insights](./functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "samplingSettings": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

> [!NOTE]
> Próbkowanie dziennika może spowodować pewne wykonania nie są wyświetlani w bloku monitor usługi Application Insights.

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|isEnabled|true|Włącza lub wyłącza próbkowania.| 
|maxTelemetryItemsPerSecond|5|Rozpoczyna się progu, na które próbkowania.| 

## <a name="cosmosdb"></a>bazy danych cosmos DB

Ustawienia konfiguracji można znaleźć w [Cosmos DB, wyzwalaczy i powiązań](functions-bindings-cosmosdb-v2.md#host-json).

## <a name="durabletask"></a>durableTask

Ustawienia konfiguracji można znaleźć w [powiązania trwałe funkcje](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

Ustawienia konfiguracji można znaleźć w [Centrum zdarzeń wyzwalaczy i powiązań](functions-bindings-event-hubs.md#host-json). 

## <a name="extensions"></a>Rozszerzenia

Właściwość, która zwraca obiekt, który zawiera wszystkie ustawienia specyficzne dla powiązania, takich jak [http](#http) i [eventHub](#eventhub).

## <a name="functions"></a>funkcje

Lista funkcji, które uruchamia hosta zadania. Pusta tablica oznacza, że uruchamianie wszystkich funkcji. Przeznaczony do użytku tylko wtedy, gdy [uruchamiane lokalnie](functions-run-local.md). W aplikacji funkcji na platformie Azure, należy zamiast tego wykonaj czynności opisane w [jak wyłączyć funkcje w usłudze Azure Functions](disable-function.md) wyłączenie określonych funkcji, a nie przy użyciu tego ustawienia.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Wskazuje wartość limitu czasu dla wszystkich funkcji. Bez użycia serwera planu zużycie prawidłowy zakres to od 1 sekundy do 10 minut, a wartość domyślna to 5 minut. W ramach planu usługi App Service nie ma limitu całkowitej, a wartość domyślna jest zależna od wersji środowiska uruchomieniowego. W wersji 2.x, wartością domyślną dla usługi App Service plan to 30 minut. W wersji 1.x, jest *null*, co oznacza brak limitu czasu.

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

Ustawienia konfiguracji można znaleźć w [http wyzwalaczy i powiązań](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="logging"></a>Rejestrowanie

Kontroluje zachowania rejestrowania aplikacji funkcji, łącznie z usługi Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------|
|fileLoggingMode|debugOnly|Określa, jaki poziom rejestrowania w pliku jest włączone.  Dostępne są opcje `never`, `always`, `debugOnly`. |
|logLevel|Nie dotyczy|Obiekt, który definiuje kategoria dziennika filtrowania dla funkcji w aplikacji. W wersji 2.x następuje układ platformy ASP.NET Core w celu filtrowania kategorii dziennika. Dzięki temu można filtrować rejestrowania dla określonych funkcji. Aby uzyskać więcej informacji, zobacz [filtrowanie dziennika](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) w dokumentacji platformy ASP.NET Core. |
|console|Nie dotyczy| [Konsoli](#console) ustawień rejestrowania. |
|applicationInsights|Nie dotyczy| [ApplicationInsights](#applicationinsights) ustawienie. |

## <a name="console"></a>console

To ustawienie jest elementem podrzędnym [rejestrowania](#logging). Kontroluje konsoli rejestrowania nie w trybie debugowania.

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|isEnabled|false|Włącza lub wyłącza rejestrowanie konsoli.| 

## <a name="queues"></a>kolejki

Ustawienia konfiguracji można znaleźć w [magazynu kolejki wyzwalaczy i powiązań](functions-bindings-storage-queue.md#host-json).  

## <a name="sendgrid"></a>sendGrid

Ustawienia konfiguracji można znaleźć w [SendGrid wyzwalaczy i powiązań](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

Ustawienia konfiguracji można znaleźć w [usługi Service Bus wyzwalaczy i powiązań](functions-bindings-service-bus.md#host-json).

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

## <a name="version"></a>version

Ciąg wersji `"version": "2.0"` jest wymagana dla aplikacji funkcji, który jest przeznaczony dla środowiska uruchomieniowego w wersji 2.

## <a name="watchdirectories"></a>watchDirectories

Zbiór [udostępnionych katalogów kodu](functions-reference-csharp.md#watched-directories) powinny być monitorowane zmian.  Zapewnia, że po zmianie kodu w tych katalogach zmiany są wykrywane przez funkcje.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="manageddependency"></a>managedDependency

Zależność zarządzanych jest funkcja w wersji zapoznawczej, która jest obecnie obsługiwane tylko za pomocą programu PowerShell oparta na funkcjach. Dzięki temu zależności, które mają być automatycznie zarządzane przez usługę. Gdy właściwość włączone ma wartość true, [requirements.psd1](functions-reference-powershell.md#dependency-management) plik zostanie przetworzony. Zależności zostanie zaktualizowany, gdy są zwalniane, wszystkie wersje pomocnicze.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak można zaktualizować pliku host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Wyświetlane ustawienia globalne w zmiennych środowiskowych](functions-app-settings.md)
