---
title: Dokumentacja pliku host. JSON dla Azure Functions 2. x
description: Dokumentacja referencyjna dotycząca pliku Azure Functions hosta. JSON z użyciem środowiska uruchomieniowego v2.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: glenga
ms.openlocfilehash: 222ca8781ae9532f10ed7d113b93eac78c6a3bba
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129081"
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
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
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
              "maxTelemetryItemsPerSecond" : 20
            }
        }
    },
    "managedDependency": {
        "enabled": true
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ]
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
          "maxTelemetryItemsPerSecond" : 20
        }
    }
}
```

> [!NOTE]
> Próbkowanie dziennika może spowodować, że niektóre wykonania nie są wyświetlane w bloku monitora Application Insights.

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|isEnabled|true|Włącza lub wyłącza próbkowanie.| 
|maxTelemetryItemsPerSecond|20|Próg, w którym rozpoczyna się próbkowanie.| 
|EnableLiveMetrics |true|Włącza zbieranie metryk na żywo.|
|EnableDependencyTracking|true|Włącza śledzenie zależności.|
|EnablePerformanceCountersCollection|true|Włącza Zbieranie liczników wydajności kudu.|

## <a name="cosmosdb"></a>cosmosDb

Ustawienie konfiguracji można znaleźć w [Cosmos DB wyzwalacze i powiązania](functions-bindings-cosmosdb-v2.md#host-json).

## <a name="durabletask"></a>durableTask

Ustawienie konfiguracji można znaleźć w obszarze [powiązania dla Durable Functions](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

Ustawienia konfiguracji można znaleźć w temacie [wyzwalacze i powiązania centrum zdarzeń](functions-bindings-event-hubs.md#host-json). 

## <a name="extensions"></a>rozszerzenia

Właściwość zwracająca obiekt, który zawiera wszystkie ustawienia specyficzne dla powiązania, takie jak [http](#http) i [eventHub](#eventhub).

## <a name="extensionbundle"></a>extensionBundle 

Zestawy rozszerzeń umożliwiają dodawanie zgodnego zestawu funkcji powiązania do aplikacji funkcji. Aby dowiedzieć się więcej, zobacz [zestawy rozszerzeń na potrzeby lokalnego tworzenia](functions-bindings-register.md#extension-bundles)oprogramowania.

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>— funkcje

Lista funkcji uruchomionych przez hosta zadań. Pusta tablica oznacza uruchamianie wszystkich funkcji. Przeznaczone do użytku tylko w przypadku [uruchamiania lokalnego](functions-run-local.md). W aplikacjach funkcji na platformie Azure zamiast tego należy wykonać kroki opisane w temacie [Jak wyłączyć funkcje w Azure Functions](disable-function.md) , aby wyłączyć określone funkcje zamiast używać tego ustawienia.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Wskazuje czas trwania dla wszystkich funkcji. Jest on zgodny z formatem ciągu TimeSpan. W planie zużycia bezserwerowego prawidłowy zakres to od 1 sekundy do 10 minut, a wartość domyślna to 5 minut.  
W przypadku planu dedykowanego (App Service) nie ma żadnego całkowitego limitu, a wartość domyślna to 30 minut. Wartość `-1` wskazuje nieograniczone wykonanie.

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
|dostępny|true|Określa, czy funkcja jest włączona. | 
|healthCheckInterval|10 sekund|Przedział czasu między okresowymi kontrolami kondycji w tle. | 
|healthCheckWindow|2 minuty|Przedział czasu, który jest używany w połączeniu z ustawieniem `healthCheckThreshold`.| 
|healthCheckThreshold|6|Maksymalna liczba przypadków, w których Sprawdzenie kondycji może zakończyć się niepowodzeniem przed zainicjowaniem odtwarzania hosta.| 
|counterThreshold|0,80|Próg, w którym licznik wydajności będzie traktowany jako w złej kondycji.| 

## <a name="http"></a>http

Ustawienia konfiguracji można znaleźć w tematach [wyzwalacze i powiązania HTTP](functions-bindings-http-webhook.md#hostjson-settings).

## <a name="logging"></a>rejestrować

Steruje zachowaniem rejestrowania aplikacji funkcji, w tym Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly"
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
|fileLoggingMode|debugOnly|Określa, jaki poziom rejestrowania plików jest włączony.  Dostępne są `never`, `always`, `debugOnly`. |
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

## <a name="manageddependency"></a>managedDependency

Zależność zarządzana to funkcja, która jest obecnie obsługiwana tylko w przypadku funkcji opartych na programie PowerShell. Umożliwia automatyczne zarządzanie zależnościami przez usługę. Gdy właściwość `enabled` jest ustawiona na `true`, plik `requirements.psd1` jest przetwarzany. Zależności są aktualizowane, gdy zostaną wydane jakiekolwiek wersje pomocnicze. Aby uzyskać więcej informacji, zobacz [zależność zarządzana](functions-reference-powershell.md#dependency-management) w artykule dotyczącym programu PowerShell.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>tworzone

Ustawienia konfiguracji można znaleźć w temacie [wyzwalacze i powiązania kolejki magazynu](functions-bindings-storage-queue.md#host-json).  

## <a name="sendgrid"></a>sendGrid

Ustawienie konfiguracji można znaleźć w [SendGrid wyzwalaczach i powiązaniach](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

Ustawienie konfiguracji można znaleźć w [Service Bus wyzwalacze i powiązania](functions-bindings-service-bus.md#host-json).

## <a name="singleton"></a>Pojedynczego

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

Ciąg wersji `"version": "2.0"` jest wymagany dla aplikacji funkcji, która jest przeznaczona dla środowiska uruchomieniowego v2.

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
