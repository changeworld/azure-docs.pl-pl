---
title: Funkcje trwałe funkcje — usługi Azure Functions w wersji zapoznawczej
description: Dowiedz się więcej o funkcji w wersji zapoznawczej dla funkcje trwałe.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.date: 04/23/2019
ms.author: azfuncdf
ms.openlocfilehash: 8ceb84ab9e9c41ff6a9cbde62571fb12ae67d790
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65596080"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Trwałe funkcje w wersji 2.0 (wersja zapoznawcza) (usługi Azure Functions)

*Trwałe funkcje* jest rozszerzeniem [usługi Azure Functions](../functions-overview.md) i [zadań Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) umożliwiający zapis stanowych funkcji w środowisku bezserwerowym. Rozszerzenie zarządza stanem, punktami kontrolnymi i ponownym uruchamianiem. Jeśli nie jesteś już znasz funkcje trwałe, zobacz [Przegląd dokumentacji](durable-functions-overview.md).

Funkcje trwałe to funkcja wersji ogólnie dostępnej (dostępne ogólnie) usługi Azure functions, ale zawiera także kilka podfunkcje, które są obecnie dostępne w publicznej wersji zapoznawczej. W tym artykule opisano funkcje nowo wydanej wersji zapoznawczej i przechodzi w szczegółowe informacje o sposobie ich działania i jak możesz rozpocząć korzystanie z nich.

> [!NOTE]
> Te funkcje w wersji zapoznawczej są częścią wersji trwałe Functions 2.0 jest obecnie **wersji alfa jakości** kilka najważniejszych zmian. Tworzy pakiet rozszerzenia niezawodne funkcje platformy Azure można znaleźć w witrynie nuget.org wersje w postaci **2.0.0-alpha**. Te kompilacje nie są odpowiednie dla dowolnych obciążeń produkcyjnych i późniejszymi wersjami może zawierać dodatkowe przełomowe zmiany.

## <a name="breaking-changes"></a>Zmiany powodujące niezgodność

Kilka przełomowe zmiany zostały wprowadzone trwałe funkcje w wersji 2.0. Istniejące aplikacje nie powinny być zgodne z trwałe Functions 2.0 bez zmian w kodzie. W tej sekcji przedstawiono niektóre zmiany:

### <a name="dropping-net-framework-support"></a>Upuszczanie Obsługa programu .NET Framework

Obsługa programu .NET Framework (i w związku z tym funkcje w wersji 1.0) został odrzucony, niezawodne funkcje 2.0. Głównym powodem jest umożliwienie współautorzy innych niż Windows, tworzyć i testować zmiany dokonane w funkcje trwałe z platform Linux i macOS. Pomocniczy przyczyną jest pomocne dla deweloperów, aby przejść do najnowszej wersji środowiska uruchomieniowego usługi Azure Functions.

### <a name="hostjson-schema"></a>Host.json schema

Poniższy fragment kodu przedstawia nowy schemat host.json. Zmiana głównego należy pamiętać o jest nowym `"storageProvider"` sekcji i `"azureStorage"` sekcji podrzędne. Ta zmiana została wykonana w celu obsługi [alternatywny dostawców magazynu](durable-functions-preview.md#alternate-storage-providers).

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
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "traceInputAndOutputs": <bool?>,
      "eventGridTopicEndpoint": <string?>,
      "eventGridKeySettingName": <string?>,
      "eventGridPublishRetryCount": <string?>,
      "eventGridPublishRetryInterval": <hh:mm:ss?>,
      "eventGridPublishRetryHttpStatus": <int[]?>,
      "eventgridPublishEventTypes": <string[]?>,
      "customLifeCycleNotificationHelperType"
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "logReplayEvents": <bool?>
  }
}
```

Jako trwałe Functions 2.0 w dalszym ciągu stabilizacji, zmiany zostaną wprowadzone do `durableTask` sekcji host.json. Aby uzyskać więcej informacji o tych zmianach, zobacz [problem w usłudze GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Zmiany interfejsu publicznego

Różne obiekty "kontekst" obsługiwane przez funkcje trwałe miał abstrakcyjnych klas bazowych, przeznaczonych dla testów jednostkowych. W ramach trwałego Functions 2.0 te abstrakcyjnych klas bazowych zostały zastąpione interfejsów. Nie wpływa na kod funkcji, która korzysta z typów konkretnych bezpośrednio.

Poniższa tabela przedstawia główne zmiany:

| Stary typ | Nowy typ |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

W przypadku, gdy abstrakcyjna klasa bazowa zawiera metod wirtualnych, te metody wirtualne zostały zastąpione przez metody rozszerzenia zdefiniowane w `DurableContextExtensions`.

## <a name="entity-functions"></a>Funkcje jednostki

Funkcje jednostki zdefiniować operacje odczytywania i aktualizowania małych fragmentów stanu, znane jako *trwałe jednostek*. Podobnie jak funkcje programu orchestrator, funkcje jednostki mają funkcje z typem wyzwalacza specjalne *wyzwalacza jednostki*. W przeciwieństwie do funkcji programu orchestrator funkcje jednostki nie ma żadnych ograniczeń określonego kodu. Funkcje jednostki również zarządzanie stanem jawnie zamiast niejawnie reprezentujący stan za pośrednictwem przepływu sterowania.

Poniższy kod jest przykładem funkcja proste jednostki, która definiuje *licznika* jednostki. Funkcja definiuje trzy operacje `add`, `subtract`, i `reset`, każdy z aktualizacji, którą wartość całkowitą `currentValue`.

```csharp
[FunctionName("Counter")]
public static async Task Counter(
    [EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    int operand = ctx.GetInput<int>();

    switch (ctx.OperationName)
    {
        case "add":
            currentValue += operand;
            break;
        case "subtract":
            currentValue -= operand;
            break;
        case "reset":
            await SendResetNotificationAsync();
            currentValue = 0;
            break;
    }

    ctx.SetState(currentValue);
}
```

Jednostka *wystąpień* są dostępne za pośrednictwem Unikatowy identyfikator *identyfikator jednostki*. Identyfikator jednostki jest po prostu pary ciągów, która jednoznacznie identyfikuje wystąpienie jednostki. Składa się z:

1. **nazwa jednostki**: nazwa, która określa typ jednostki (na przykład "Licznik")
2. **klucz jednostki**: ciąg, który unikatowo identyfikuje jednostkę wśród innych jednostek o takiej samej nazwie (na przykład, GUID)

Na przykład *licznika* funkcji jednostki mogą być używane do przechowywania wyników w grze online. Każde wystąpienie gry ma identyfikator unikatowy jednostki, takie jak `@Counter@Game1`, `@Counter@Game2`i tak dalej.

### <a name="comparison-with-virtual-actors"></a>Porównanie z wirtualnego aktorów

Silnie wpływają projektowania jednostek trwałe [model aktorów](https://en.wikipedia.org/wiki/Actor_model). Jeśli już znasz aktorów, pojęć dotyczących trwałego jednostek powinno być znane. W szczególności trwałe jednostki są podobne do [wirtualnego aktorów](https://research.microsoft.com/en-us/projects/orleans/) na wiele sposobów:

* Trwałe wytycznym organizacje mogą być adresowane za pośrednictwem *identyfikator jednostki*.
* Trwałe jednostki operacje wykonywane szeregowo, po kolei, aby zapobiec wyścigu.
* Trwałe jednostki są tworzone automatycznie, gdy są one nazywane lub zasygnalizowane.
* Gdy nie wykonuje operacje, trwałe jednostki są dyskretnie zwolniony z pamięci.

Istnieją pewne ważne różnice, które są warte odnotowania:

* Trwałe jednostki są modelowane jako czystych funkcji. Ten projekt jest inny niż większość struktur zorientowane obiektowo, które reprezentują złośliwych użytkowników używających obsługi określonego języka dla klas, właściwości i metody.
* Trwałe jednostek priorytety *trwałości* za pośrednictwem *opóźnienie*i dlatego nie może być odpowiednia dla aplikacji z ograniczeniami opóźnień.
* Komunikaty wysyłane między jednostkami są dostarczane, niezawodnie i w kolejności.
* Trwałe jednostki mogą być używane w połączeniu z mechanizmów trwałe i może służyć jako rozproszone blokad, które są opisane w dalszej części tego artykułu.
* Wzorce jednostek żądań/odpowiedzi są ograniczone do aranżacji. Komunikacji jednostki do jednostki tylko jednokierunkowe komunikaty (znany także jako "sygnalizowanie") są dozwolone, tak jak w oryginalnym model aktorów. To zachowanie zapobiega rozproszonych zakleszczenia.

### <a name="durable-entity-apis"></a>Interfejsy API trwałe jednostki

Obsługa jednostki obejmuje kilka interfejsów API. Dla, istnieje nowy interfejs API definiowania jednostka funkcji, jak pokazano powyżej, które określą, co ma się zdarzyć, gdy operacja jest wywoływany w jednostce. Ponadto istniejących interfejsów API dla klientów i aranżacji zostały zaktualizowane o nowe funkcje do interakcji z jednostkami.

### <a name="implementing-entity-operations"></a>Działania jednostki

Wykonywanie operacji na jednostce może wywołać te elementy członkowskie obiektu context (`IDurableEntityContext` na platformie .NET):

* **OperationName**: pobiera nazwę operacji.
* **GetInput\<T >** : pobiera dane wejściowe dla operacji.
* **GetState\<T >** : pobiera bieżący stan jednostki.
* **SetState**: aktualizuje stan jednostki.
* **SignalEntity**: wysyła komunikat jednokierunkowy do jednostki.
* **Funkcja samoobsługowego**: pobiera identyfikator obiektu.
* **Zwróć**: zwraca wartość do klienta lub aranżacji, który wywołał operację.
* **IsNewlyConstructed**: zwraca `true` Jeśli jednostki nie istniały przed wykonaniem operacji.
* **DestructOnExit**: usuwa jednostki po zakończeniu operacji.

Operacje są mniej ograniczone niż mechanizmów:

* Operacje mogą wywołać zewnętrznych we/wy przy użyciu interfejsów API synchroniczna lub asynchroniczna (zaleca się używanie tylko tych asynchroniczne).
* Operacje mogą być deterministyczna. Na przykład, jest bezpieczny do wywołania `DateTime.UtcNow`, `Guid.NewGuid()` lub `new Random()`.

### <a name="accessing-entities-from-clients"></a>Uzyskiwanie dostępu do jednostek od klientów

Trwałe jednostki mogą być wywoływane z zwykłe funkcje za pośrednictwem `orchestrationClient` powiązania (`IDurableOrchestrationClient` na platformie .NET). Obsługiwane są następujące metody:

* **ReadEntityStateAsync\<T >** : odczytuje stan obiektu.
* **SignalEntityAsync**: wysyła komunikat jednokierunkowy do jednostki, a następnie czeka na jego można umieścić w kolejce.

Te metody priorytety wydajności za pośrednictwem spójności: `ReadEntityStateAsync` może zwrócić wartość starych i `SignalEntityAsync` może zwrócić, zanim operacja zostanie zakończona. Z kolei wywołanie jednostki z mechanizmów (zgodnie z opisem) jest silnie spójne.

### <a name="accessing-entities-from-orchestrations"></a>Uzyskiwanie dostępu do jednostek z aranżacji

Aranżacji można uzyskać dostęp do jednostki przy użyciu obiektu kontekstu. Można wybrać komunikacja jednokierunkowa (zostanie wyzwolony i zapomnij) i komunikacja dwukierunkowa (żądania i odpowiedzi). W odpowiednich metod

* **SignalEntity**: wysyła komunikat jednokierunkowy do jednostki.
* **CallEntityAsync**: wysyła komunikat do jednostki, a następnie czeka na odpowiedź wskazującą, czy operacja zostanie ukończona.
* **CallEntityAsync\<T >** : wysyła komunikat do jednostki, a następnie czeka na odpowiedź zawierającą wynik o typie T.

Korzystając z komunikacja dwukierunkowa, wyjątki zgłaszane podczas wykonywania operacji również są przesyłane z powrotem do wywołującego aranżacji i zgłaszany ponownie. Z kolei korzystając z pożarem i zapominać, wyjątki nie są przestrzegane.

### <a name="locking-entities-from-orchestrations"></a>Blokowanie jednostek z aranżacji

Aranżacji można zablokować jednostek. Ta funkcja zapewnia prosty sposób zapobiec sam niepożądane, za pomocą *sekcje krytyczne*.

Obiekt kontekstu udostępnia następujące metody:

* **LockAsync**: nabywa blokady na co najmniej jednej jednostki.
* **IsLocked**: zwraca wartość true, jeśli aktualnie w sekcję krytyczną, wartość false w przeciwnym razie.

Sekcja krytycznego kończy się i wszystkie blokady są zwalniane, a po zakończeniu aranżacji. Na platformie .NET `LockAsync` zwraca `IDisposable` , kończy się sekcję krytyczną po usunięciu, który może być używany razem z `using` klauzuli można pobrać składni reprezentacja sekcję krytyczną.

Na przykład należy wziąć pod uwagę aranżacji, który wymaga, aby sprawdzić, czy dostępne są dwie gracze, a następnie przypisz im zarówno do gier. To zadanie można zaimplementować przy użyciu sekcję krytyczną, w następujący sposób:

```csharp
[FunctionName("Orchestrator")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext ctx)
{
    EntityId player1 = /* ... */;
    EntityId player2 = /* ... */;

    using (await ctx.LockAsync(player1, player2))
    {
        bool available1 = await ctx.CallEntityAsync<bool>(player1, "is-available");
        bool available2 = await ctx.CallEntityAsync<bool>(player2, "is-available");

        if (available1 && available2)
        {
            Guid gameId = ctx.NewGuid();

            await ctx.CallEntityAsync(player1, "assign-game", gameId);
            await ctx.CallEntityAsync(player2, "assign-game", gameId);
        }
    }
}
```

W ramach sekcję krytyczną jednostek obu graczy są zablokowane, co oznacza, że nie wykonują wszystkich operacji innych niż te, które są wywoływane z w ramach sekcję krytyczną). To zachowanie zapobiega sam z operacjami wywołującymi konflikt, takich jak gracze przypisywanych do innego grę lub podpisywania wyłączone.

Firma Microsoft nakłada kilka ograniczeń w sekcjach stopień można użyć. Ograniczenia te służą do zapobiegania zakleszczenia i współużytkowania wątkowości.

* Nie można zagnieżdżać sekcje krytyczne.
* Sekcje krytyczne, nie można utworzyć suborchestrations.
* Sekcje krytyczne może wywoływać tylko w przypadku jednostek, które mają one zablokowany.
* Sekcje krytyczne, nie można wywołać tej samej jednostki przy użyciu wielu wywołań równoległych.
* Sekcje krytyczne można sygnał tylko jednostek, które nie mają one zablokowany.

## <a name="alternate-storage-providers"></a>Magazyn alternatywnego dostawcy

Trwałe Framework zadanie obsługuje wielu dostawców magazynu już dzisiaj, w tym [usługi Azure Storage](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [usługi Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), [emulatora w pamięci](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)i eksperymentalne [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) dostawcy. Jednak do chwili obecnej, rozszerzenie trwałe zadania dla usługi Azure Functions obsługiwane tylko dostawcy usługi Azure Storage. Począwszy od dnia niezawodne funkcje wersji 2.0, obsługę alternatywnego magazynu dostawców jest dodawany, począwszy od dostawcy pamięci podręcznej Redis.

> [!NOTE]
> Trwałe funkcje 2.0 obsługuje tylko .NET Standard 2.0 zgodnych dostawców. W czasie pisania dostawca usługi Azure Service Bus nie obsługuje .NET Standard 2.0 i dlatego nie dostępne jako dostawcę magazynu alternatywne.

### <a name="emulator"></a>Emulator

[DurableTask.Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) dostawca jest pamięci lokalnej, dostawca magazynu nietrwałe jest odpowiednia dla lokalnego testowania scenariuszy. Można skonfigurować, za pomocą następujących minimalny **host.json** schematu:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis Cache (wersja eksperymentalna)

[DurableTask.Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) dostawca będzie się powtarzać, stan wszystkich aranżacji na skonfigurowanym klastrze pamięci podręcznej Redis.

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

`connectionStringName` Musi odwoływać się do nazwy zmiennej ustawienie lub środowiska aplikacji. Tej zmiennej ustawienie lub środowiska aplikacji powinna zawierać wartość parametrów połączenia w pamięci podręcznej Redis w formie *serwer: port*. Na przykład `localhost:6379` do łączenia się z lokalnego klastra pamięci podręcznej Redis.

> [!NOTE]
> Dostawcy pamięci podręcznej Redis jest obecnie eksperymentalny i obsługuje tylko aplikacji funkcji działających w jednym węźle.
