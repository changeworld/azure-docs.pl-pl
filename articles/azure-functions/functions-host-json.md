---
title: Dokumentacja pliku host. JSON dla Azure Functions 2. x
description: Dokumentacja referencyjna dotycząca pliku Azure Functions hosta. JSON z użyciem środowiska uruchomieniowego v2.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: glenga
ms.openlocfilehash: ecb2059e529347b7eff72bf6af74b82558a4c251
ms.sourcegitcommit: 83a89c45253b0d432ce8dcd70084c18e9930b1fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371699"
---
# <a name="hostjson-reference-for-azure-functions-2x"></a>Dokumentacja pliku host. JSON dla Azure Functions 2. x  

> [!div class="op_single_selector" title1="Wybierz wersję środowiska uruchomieniowego Azure Functions, którego używasz: "]
> * [Wersja 1](functions-host-json-v1.md)
> * [Wersja 2](functions-host-json.md)

Plik metadanych *hosta. JSON* zawiera globalne opcje konfiguracji, które mają wpływ na wszystkie funkcje aplikacji funkcji. W tym artykule wymieniono ustawienia, które są dostępne dla środowiska uruchomieniowego w wersji 2.  

> [!NOTE]
> Ten artykuł jest przeznaczony dla Azure Functions 2. x.  Odwołanie host.json w funkcjach 1.x, zobacz [dokumentacja pliku host.JSON dla usługi Azure Functions 1.x](functions-host-json-v1.md).

Inne opcje konfiguracji aplikacji funkcji są zarządzane w [ustawieniach aplikacji](functions-app-settings.md).

Niektóre ustawienia pliku host. JSON są używane tylko w przypadku uruchamiania lokalnego w pliku [Local. Settings. JSON](functions-run-local.md#local-settings-file) .

## <a name="sample-hostjson-file"></a>Przykładowy plik host. JSON

W poniższych przykładowych plikach *hosta. JSON* dostępne są wszystkie możliwe opcje.

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

Kontroluje [funkcję próbkowania w Application Insights](./functions-monitoring.md#configure-sampling).

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
> Próbkowanie dziennika może spowodować, że niektóre wykonania nie są wyświetlane w bloku monitora Application Insights.

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|isEnabled|true|Włącza lub wyłącza próbkowanie.| 
|maxTelemetryItemsPerSecond|5|Próg, w którym rozpoczyna się próbkowanie.| 

## <a name="cosmosdb"></a>cosmosDb

Ustawienie konfiguracji można znaleźć w [Cosmos DB wyzwalacze i powiązania](functions-bindings-cosmosdb-v2.md#host-json).

## <a name="durabletask"></a>durableTask

Ustawienie konfiguracji można znaleźć w obszarze [powiązania dla Durable Functions](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

Ustawienia konfiguracji można znaleźć w temacie [wyzwalacze i powiązania centrum zdarzeń](functions-bindings-event-hubs.md#host-json). 

## <a name="extensions"></a>rozszerzenia

Właściwość zwracająca obiekt, który zawiera wszystkie ustawienia specyficzne dla powiązania, takie jak [http](#http) i [eventHub](#eventhub).

## <a name="functions"></a>— funkcje

Lista funkcji uruchomionych przez hosta zadań. Pusta tablica oznacza uruchamianie wszystkich funkcji. Przeznaczone do użytku tylko w przypadku [uruchamiania lokalnego](functions-run-local.md). W aplikacjach funkcji na platformie Azure zamiast tego należy wykonać kroki opisane w temacie [Jak wyłączyć funkcje w Azure Functions](disable-function.md) , aby wyłączyć określone funkcje zamiast używać tego ustawienia.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Wskazuje czas trwania dla wszystkich funkcji. W planie zużycia bezserwerowego prawidłowy zakres to od 1 sekundy do 10 minut, a wartość domyślna to 5 minut. W planie App Service nie ma żadnego całkowitego limitu i domyślnie zależy od wersji środowiska uruchomieniowego. W wersji 2. x domyślna wartość planu App Service to 30 minut. W wersji 1. x ma *wartość null*, co oznacza brak limitu czasu. Nie można ustawić jako nieskończonej. Jeśli nie ustawimy tej wartości jawnie, będzie ona przyjmować wartość domyślną 30 minut.

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

Ustawienia konfiguracji można znaleźć w tematach [wyzwalacze i powiązania HTTP](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="logging"></a>rejestrować

Steruje zachowaniem rejestrowania aplikacji funkcji, w tym Application Insights.

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
|fileLoggingMode|debugOnly|Określa, jaki poziom rejestrowania plików jest włączony.  Dostępne opcje `never`to `always`, `debugOnly`,. |
|logLevel|Nie dotyczy|Obiekt, który definiuje filtrowanie kategorii dzienników dla funkcji w aplikacji. Wersja 2. x jest zgodna z układem ASP.NET Core dla filtrowania kategorii dzienników. Pozwala to na filtrowanie rejestrowania dla określonych funkcji. Aby uzyskać więcej informacji, zobacz [filtrowanie dzienników](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) w dokumentacji ASP.NET Core. |
|console|Nie dotyczy| [console](#console) ustawień rejestrowania. |
|applicationInsights|Nie dotyczy| Ustawienie [applicationInsights](#applicationinsights) . |

## <a name="console"></a>console

To ustawienie jest elementem podrzędnym [rejestrowania](#logging). Kontroluje Rejestrowanie konsoli, gdy nie jest w trybie debugowania.

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

## <a name="queues"></a>tworzone

Ustawienia konfiguracji można znaleźć w temacie [wyzwalacze i powiązania kolejki magazynu](functions-bindings-storage-queue.md#host-json).  

## <a name="sendgrid"></a>sendGrid

Ustawienie konfiguracji można znaleźć w [SendGrid wyzwalaczach i powiązaniach](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

Ustawienie konfiguracji można znaleźć w [Service Bus wyzwalacze i powiązania](functions-bindings-service-bus.md#host-json).

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

## <a name="version"></a>version

Ciąg `"version": "2.0"` wersji jest wymagany dla aplikacji funkcji, która jest przeznaczona dla środowiska uruchomieniowego v2.

## <a name="watchdirectories"></a>watchDirectories

Zestaw [udostępnionych katalogów kodu](functions-reference-csharp.md#watched-directories) , które powinny być monitorowane pod kątem zmian.  Zapewnia, że gdy kod w tych katalogach zostanie zmieniony, zmiany są pobierane przez funkcje.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="manageddependency"></a>managedDependency

Zależność zarządzana to funkcja w wersji zapoznawczej, która jest obecnie obsługiwana tylko w przypadku funkcji opartych na programie PowerShell. Umożliwia automatyczne zarządzanie zależnościami przez usługę. Gdy właściwość enabled ma wartość true, plik Requirements [. psd1](functions-reference-powershell.md#dependency-management) zostanie przetworzony. Zależności zostaną zaktualizowane, gdy zostaną wydane jakiekolwiek wersje pomocnicze.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o aktualizowaniu pliku host. JSON](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Zobacz ustawienia globalne w zmiennych środowiskowych](functions-app-settings.md)
