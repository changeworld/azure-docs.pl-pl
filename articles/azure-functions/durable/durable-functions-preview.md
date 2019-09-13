---
title: Durable Functions funkcji w wersji zapoznawczej — Azure Functions
description: Uzyskaj informacje na temat funkcji w wersji zapoznawczej dla Durable Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: article
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 8f2560141eac4e7d9fed267d347990e65bfe9c26
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933243"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2,0 (wersja zapoznawcza) (Azure Functions)

*Durable Functions* to rozszerzenie [Azure Functions](../functions-overview.md) i [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) , które pozwala pisać funkcje stanowe w środowisku bezserwerowym. Rozszerzenie zarządza stanem, punktami kontrolnymi i ponownym uruchamianiem. Jeśli nie znasz jeszcze Durable Functions, zapoznaj się z [dokumentacją dotyczącą przeglądu](durable-functions-overview.md).

Durable Functions 1. x to ogólnie dostępna funkcja Azure Functions, ale również zawiera kilka funkcji, które są obecnie dostępne w publicznej wersji zapoznawczej. W tym artykule opisano nowo wydane funkcje w wersji zapoznawczej i przedstawiono szczegółowe informacje na temat ich działania oraz sposobu ich używania.

> [!NOTE]
> Te funkcje w wersji zapoznawczej są częścią wersji 2,0 Durable Functions, która jest obecnie **wersją** zapoznawczą z kilkoma istotnymi zmianami. Kompilacje pakietu rozszerzenia trwałego Azure Functions można znaleźć w nuget.org z wersjami w postaci **2.0.0-betaX**. Te kompilacje nie są przeznaczone do obciążeń produkcyjnych, a kolejne wersje mogą zawierać dodatkowe istotne zmiany.

## <a name="breaking-changes"></a>Zmiany powodujące niezgodność

W Durable Functions 2,0 wprowadzono kilka istotnych zmian. Istniejące aplikacje nie powinny być zgodne z Durable Functions 2,0 bez zmian w kodzie. W tej sekcji wymieniono niektóre zmiany:

### <a name="hostjson-schema"></a>Schemat pliku host. JSON

Poniższy fragment kodu przedstawia nowy schemat pliku host. JSON. Głównymi zmianami, których należy wiedzieć, są nowe podsekcje:

* `"storageProvider"`(i `"azureStorage"` podsekcję) dla konfiguracji specyficznej dla magazynu
* `"tracking"`do śledzenia i rejestrowania konfiguracji
* `"notifications"`(i `"eventGrid"` podsekcję) dla konfiguracji powiadomień usługi Event Grid

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

Ponieważ Durable Functions 2,0 nadal ulega stabilizacji, w `durableTask` sekcji pliku host. JSON zostanie wprowadzonych więcej zmian. Aby uzyskać więcej informacji na temat tych zmian, zobacz [ten problem](https://github.com/Azure/azure-functions-durable-extension/issues/641)w usłudze GitHub.

### <a name="public-interface-changes"></a>Zmiany interfejsu publicznego

Różne obiekty "context" obsługiwane przez Durable Functions mają abstrakcyjne klasy bazowe przeznaczone do użycia w testach jednostkowych. W ramach Durable Functions 2,0 te abstrakcyjne klasy bazowe zostały zastąpione interfejsami. Nie wpłynie to na kod funkcji, który używa konkretnych typów bezpośrednio.

Poniższa tabela przedstawia główne zmiany:

| Stary typ | Nowy typ |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |
| OrchestrationClientAttribute | DurableClientAttribute |

W przypadku, gdy abstrakcyjna klasa bazowa zawiera metody wirtualne, te metody wirtualne zostały zastąpione metodami rozszerzenia zdefiniowanymi `DurableContextExtensions`w.

## <a name="entity-functions"></a>Funkcje jednostki

Począwszy od Durable Functions v 2.0.0-alpha wprowadziliśmy nową koncepcję [funkcji Entity](durable-functions-entities.md) .

Funkcje Entity definiują operacje umożliwiające odczytywanie i aktualizowanie małych fragmentów stanu, znanych jako *jednostek trwałych*. Podobnie jak funkcje programu Orchestrator, funkcje jednostki są funkcjami o specjalnym typie wyzwalacza, *wyzwalaczem jednostki*. W przeciwieństwie do funkcji programu Orchestrator, funkcje jednostek nie mają żadnych ograniczeń związanych z kodem. Funkcje jednostek również zarządzają stanem jawnie, a nie niejawnie reprezentującą stan za pośrednictwem przepływu sterowania.

W oparciu o początkową opinię użytkownika dodaliśmy obsługę modelu programowania opartego na klasie dla jednostek w Durable Functions v 2.0.0-beta1.

Aby dowiedzieć się więcej, zobacz artykuł dotyczący [funkcji Entity](durable-functions-entities.md) .

## <a name="durable-http"></a>Trwałe HTTP

Począwszy od Durable Functions v 2.0.0 beta2, wprowadziliśmy nową funkcję [trwałego http](durable-functions-http-features.md) , która umożliwia:

* Wywoływanie interfejsów API protokołu HTTP bezpośrednio z funkcji aranżacji (z niektórymi opisanymi ograniczeniami)
* Implementuje automatyczną sondowanie stanu protokołu HTTP 202 po stronie klienta
* Wbudowana obsługa [tożsamości zarządzanych przez platformę Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Aby dowiedzieć się więcej, zobacz artykuł dotyczący [funkcji protokołu HTTP](durable-functions-http-features.md#consuming-http-apis) .

## <a name="alternate-storage-providers"></a>Alternatywni dostawcy magazynu

Usługa trwałych zadań obsługuje obecnie wielu dostawców magazynu, w tym [usługę Azure Storage](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), [emulator w pamięci](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)i eksperymentalny dostawca [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) . Jednak do tej pory rozszerzenie zadania trwałego dla Azure Functions obsługiwane tylko przez dostawcę usługi Azure Storage. Począwszy od Durable Functions 2,0, zostanie dodana obsługa alternatywnych dostawców magazynu, rozpoczynając od dostawcy Redis.

> [!NOTE]
> Durable Functions 2,0 obsługuje tylko dostawców zgodnych .NET Standard 2,0.

### <a name="emulator"></a>Wzorca

Dostawca [DurableTask. emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) to pamięć lokalna, nietrwały dostawca magazynu odpowiedni dla scenariuszy testowania lokalnego. Można go skonfigurować przy użyciu następującego schematu minimalnego **hosta. JSON** :

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { "enabled": true }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (wersja eksperymentalna)

Dostawca [DurableTask. Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) utrzymuje wszystkie Stany aranżacji w skonfigurowanym klastrze Redis.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

`connectionStringName` Musi odwoływać się do nazwy ustawienia aplikacji lub zmiennej środowiskowej. To ustawienie aplikacji lub zmienna środowiskowa powinna zawierać Redis wartość parametrów połączenia w postaci *serwer: Port*. Na przykład `localhost:6379` w celu nawiązania połączenia z lokalnym klastrem Redis.

> [!NOTE]
> Dostawca Redis jest obecnie eksperymentalny i obsługuje tylko aplikacje funkcji działające w jednym węźle. Nie ma gwarancji, że dostawca Redis będzie kiedykolwiek dostępny i może zostać usunięty w przyszłej wersji.
