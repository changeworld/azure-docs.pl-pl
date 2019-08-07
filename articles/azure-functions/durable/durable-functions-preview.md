---
title: Durable Functions funkcji w wersji zapoznawczej — Azure Functions
description: Uzyskaj informacje na temat funkcji w wersji zapoznawczej dla Durable Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: article
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 1609931cd5fcab0977ff64f680fbb1f253f3caaf
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782187"
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

W przypadku, gdy abstrakcyjna klasa bazowa zawiera metody wirtualne, te metody wirtualne zostały zastąpione metodami rozszerzenia zdefiniowanymi `DurableContextExtensions`w.

## <a name="entity-functions"></a>Funkcje jednostki

Funkcje Entity definiują operacje umożliwiające odczytywanie i aktualizowanie małych fragmentów stanu, znanych jako *jednostek trwałych*. Podobnie jak funkcje programu Orchestrator, funkcje jednostki są funkcjami o specjalnym typie wyzwalacza, *wyzwalaczem jednostki*. W przeciwieństwie do funkcji programu Orchestrator, funkcje jednostek nie mają żadnych ograniczeń związanych z kodem. Funkcje jednostek również zarządzają stanem jawnie, a nie niejawnie reprezentującą stan za pośrednictwem przepływu sterowania.

### <a name="net-programing-models"></a>Modele programów .NET

Istnieją dwa opcjonalne modele programowania do tworzenia trwałych jednostek. Poniższy kod jest przykładem prostej jednostki *licznika* zaimplementowaną jako funkcja standardowa. Ta funkcja definiuje trzy *operacje*, `add`, `reset`, i `get`, z których `currentValue`każda działa na wartości stanu liczby całkowitej.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

Ten model działa najlepiej w przypadku prostych implementacji jednostek lub implementacji, które mają dynamiczny zestaw operacji. Istnieje jednak również model programowania oparty na klasie, który jest przydatny dla jednostek, które są statyczne, ale mają bardziej złożone implementacje. Poniższy przykład jest równoważną implementacją `Counter` jednostki przy użyciu klas i metod platformy .NET.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;
    
    public void Reset() => this.CurrentValue = 0;
    
    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

Model oparty na klasie jest podobny do modelu programowania popularnego przez [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). W tym modelu typ jednostki jest zdefiniowany jako Klasa platformy .NET. Każda metoda klasy jest operacją, którą może wywołać Klient zewnętrzny. W przeciwieństwie do orleans interfejsy .NET są jednak opcjonalne. Poprzedni przykład *licznika* nie korzystał z interfejsu, ale nadal może być wywoływany przez inne funkcje lub wywołania interfejsu API protokołu HTTP.

*Wystąpienia* jednostek są dostępne za pośrednictwem unikatowego identyfikatora, *identyfikatora jednostki*. Identyfikator jednostki to po prostu para ciągów, które jednoznacznie identyfikują wystąpienie jednostki. Składa się z:

* **Nazwa jednostki**: Nazwa identyfikująca typ jednostki (na przykład "licznik").
* **Klucz jednostki**: ciąg, który jednoznacznie identyfikuje jednostkę między wszystkimi innymi jednostkami o tej samej nazwie (na przykład identyfikator GUID).

Na przykład funkcja jednostki *licznika* może być używana do przechowywania wyników w grze online. Każde wystąpienie gry będzie miało unikatowy identyfikator jednostki, na `@Counter@Game1`przykład, `@Counter@Game2`, i tak dalej.

### <a name="comparison-with-virtual-actors"></a>Porównanie z aktorami wirtualnymi

[Model aktora](https://en.wikipedia.org/wiki/Actor_model)ma silną wpływ na projekt obiektów trwałych. Jeśli znasz już aktorów, możesz zapoznać się z pojęciami związanymi z trwałymi jednostkami. W szczególności trwałe jednostki są podobne do [aktorów wirtualnych](https://research.microsoft.com/projects/orleans/) na wiele sposobów:

* Jednostki trwałe są adresowane za pośrednictwem *identyfikatora jednostki*.
* Trwałe operacje jednostki wykonują szeregowo, pojedynczo, aby uniknąć sytuacji wyścigu.
* Trwałe jednostki są tworzone automatycznie po wywołaniu lub zasygnalizowaniu.
* W przypadku braku wykonywania operacji trwałe jednostki są dyskretnie zwalniane z pamięci.

Istnieją jednak pewne istotne różnice, które warto wziąć pod uwagę:

* Trwałe jednostki ustalają priorytety *trwałości* przed *opóźnieniami*i w ten sposób mogą nie być odpowiednie dla aplikacji z rygorystycznymi wymaganiami opóźnienia.
* Komunikaty wysyłane między jednostkami są dostarczane w sposób niezawodny i w kolejności.
* Trwałe jednostki mogą być używane w połączeniu z trwałymi aranżacjami i mogą służyć jako blokady rozproszone, które są opisane w dalszej części tego artykułu.
* Wzorce żądania/odpowiedzi w jednostkach są ograniczone do aranżacji. W przypadku komunikacji między jednostkami można używać tylko komunikatów jednokierunkowych (nazywanych także "sygnalizacją"), jak w oryginalnym modelu aktora. Takie zachowanie uniemożliwia rozproszone zakleszczenie.

### <a name="durable-entity-net-apis"></a>Trwałe interfejsy API platformy .NET dla jednostek

Obsługa jednostek obejmuje kilka interfejsów API. Dla jednego z nich istnieje nowy interfejs API służący do definiowania funkcji Entity, jak pokazano powyżej, które określają, co ma się zdarzyć, gdy operacja jest wywoływana w jednostce. Ponadto istniejące interfejsy API dla klientów i aranżacji zostały zaktualizowane o nowe funkcje interakcji z jednostkami.

#### <a name="implementing-entity-operations"></a>Implementowanie operacji jednostki

Wykonanie operacji na jednostce może wywołać te elementy członkowskie w obiekcie kontekstu (`IDurableEntityContext` w programie .NET):

* **OperationName**: Pobiera nazwę operacji.
* **Getinput\<TInput >** : Pobiera dane wejściowe dla operacji.
* **GetState\<TState >** : Pobiera bieżący stan jednostki.
* **Setstate**: aktualizuje stan jednostki.
* **SignalEntity**: wysyła komunikat jednokierunkowy do jednostki.
* **Własne**: Pobiera identyfikator jednostki.
* **Return**: zwraca wartość do klienta lub aranżacji, która wywołała operację.
* **IsNewlyConstructed**: zwraca `true` czy jednostka nie istnieje przed operacją.
* **DestructOnExit**: usuwa jednostkę po zakończeniu operacji.

Operacje są mniej ograniczone niż aranżacje:

* Operacje umożliwiają wywoływanie zewnętrznych operacji we/wy przy użyciu synchronicznych lub asynchronicznych interfejsów API (zaleca się używanie tylko elementów asynchronicznych).
* Operacje mogą być niejednoznaczne. Na przykład bezpieczne jest wywołanie `DateTime.UtcNow`, `Guid.NewGuid()` lub `new Random()`.

#### <a name="accessing-entities-from-clients"></a>Uzyskiwanie dostępu do jednostek z klientów

Jednostki trwałe mogą być wywoływane z funkcji zwykłych za pośrednictwem `orchestrationClient` powiązania (`IDurableOrchestrationClient` w programie .NET). Obsługiwane są następujące metody:

* **ReadEntityStateAsync\<T >** : odczytuje stan jednostki.
* **SignalEntityAsync**: wysyła komunikat jednokierunkowy do jednostki i czeka na jego przekolejkowanie.
* **SignalEntityAsync\<T >** : taki sam `SignalEntityAsync` jak, ale używa wygenerowanego obiektu `T`serwera proxy typu.

Poprzednie `SignalEntityAsync` wywołanie wymaga określenia nazwy operacji jednostki `string` jako i ładunku operacji jako `object`. Następujący przykładowy kod jest przykładem tego wzorca:

```csharp
EntityId id = // ...
object amount = 5;
context.SignalEntityAsync(id, "Add", amount);
```

Istnieje również możliwość wygenerowania obiektu proxy dla dostępu bezpiecznego typu. Aby wygenerować bezpieczny dla typu serwer proxy, typ jednostki musi implementować interfejs. Załóżmy na przykład, że `Counter` jednostka wymieniona wcześniej `ICounter` implementuje interfejs, zdefiniowane w następujący sposób:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    int Get();
}

public class Counter : ICounter
{
    // ...
}
```

Kod klienta może następnie użyć `SignalEntityAsync<T>` i `ICounter` określić interfejs jako parametr typu, aby wygenerować bezpieczny dla typu serwer proxy. Użycie bezpiecznych typów serwerów proxy jest zademonstrowane w następującym przykładzie kodu:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [OrchestrationClient] IDurableOrchestrationClient client)
{
    int amount = int.Parse(message);
    var target = new EntityId(nameof(Counter), "MyCounter");
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

W poprzednim przykładzie `proxy` parametr jest dynamicznie generowanym `ICounter`wystąpieniem, które wewnętrznie tłumaczy wywołanie na `Add` równoważne (bez typu) wywołania do `SignalEntityAsync`.

Parametr typu dla `SignalEntityAsync<T>` ma następujące ograniczenia:

* Parametr typu musi być interfejsem.
* Tylko metody można definiować w interfejsie. Właściwości nie są obsługiwane.
* Każda metoda musi definiować jeden lub bez parametrów.
* Każda metoda musi zwracać `void`element,, lub `Task<T>` gdzie `T` jest jakiś typ, `Task`który serializować kod JSON.
* Interfejs musi być zaimplementowany przez dokładnie jeden typ w ramach zestawu interfejsu.

W większości przypadków interfejsy, które nie spełniają tych wymagań, spowodują wyjątek czasu wykonania.

> [!NOTE]
> Należy pamiętać, że `ReadEntityStateAsync` metody i `SignalEntityAsync` ustalają priorytety `IDurableOrchestrationClient` wydajności w porównaniu z spójnością. `ReadEntityStateAsync`może zwrócić nieodświeżoną wartość i `SignalEntityAsync` może zwrócić przed zakończeniem operacji.

#### <a name="accessing-entities-from-orchestrations"></a>Uzyskiwanie dostępu do jednostek z aranżacji

Aranżacje mogą uzyskiwać dostęp do jednostek `IDurableOrchestrationContext` przy użyciu obiektu. Mogą oni wybierać między komunikacją jednokierunkową (ogień i zapomnienie) oraz komunikacji dwukierunkowej (żądanie i odpowiedź). Odpowiednie metody to:

* **SignalEntity**: wysyła komunikat jednokierunkowy do jednostki.
* **CallEntityAsync**: wysyła komunikat do jednostki i czeka na odpowiedź wskazującą, że operacja została ukończona.
* **CallEntityAsync\<T >** : wysyła komunikat do jednostki i czeka na odpowiedź zawierającą wynik typu T.

W przypadku komunikacji dwukierunkowej wszystkie wyjątki zgłoszone podczas wykonywania operacji są również przesyłane z powrotem do aranżacji wywołującej i ponownie zgłoszonych. Natomiast w przypadku korzystania z ognia i zapomnienia wyjątki nie są spełnione.

W celu bezpiecznego dostępu do typu funkcje aranżacji mogą generować serwery proxy oparte na interfejsie. W tym celu można użyć metody rozszerzającej:`CreateEntityProxy`

```csharp
public interface IAsyncCounter
{
    Task AddAsync(int amount);
    Task ResetAsync();
    Task<int> GetAsync();
}

[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...
    IAsyncCounter proxy = context.CreateEntityProxy<IAsyncCounter>("MyCounter");
    await proxy.AddAsync(5);
    int newValue = await proxy.GetAsync();
    // ...
}
```

W poprzednim przykładzie przyjęto założenie, że istnieje jednostka "licznik", która implementuje `IAsyncCounter` interfejs. Aranżacja była w stanie użyć `IAsyncCounter` definicji typu do wygenerowania typu serwera proxy na potrzeby synchronicznej współpracy z jednostką.

### <a name="locking-entities-from-orchestrations"></a>Blokowanie jednostek z aranżacji

Aranżacje mogą blokować jednostki. Ta funkcja zapewnia prosty sposób zapobiegania niepożądanym Races za pomocą *sekcji krytycznych*.

Obiekt kontekstu udostępnia następujące metody:

* **LockAsync**: uzyskuje blokadę dla co najmniej jednej jednostki.
* IsLocked: zwraca wartość true, jeśli obecnie znajduje się w sekcji krytycznej, w przeciwnym razie false.

Sekcja krytyczna zostanie zakończona, a wszystkie blokady zostaną wydane po zakończeniu aranżacji. W programie .NET `LockAsync` `IDisposable` funkcja zwraca kończącą sekcję krytyczną po usunięciu, która może `using` być używana razem z klauzulą w celu uzyskania składniowej reprezentacji sekcji krytycznej.

Rozważmy na przykład aranżację, która musi sprawdzić, czy dwie osoby są dostępne, a następnie przypisać je do gry. To zadanie można zaimplementować przy użyciu sekcji krytycznej w następujący sposób:

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

W sekcji krytycznej obie jednostki odtwarzacza są zablokowane, co oznacza, że nie wykonuje żadnych operacji innych niż te, które są wywoływane z poziomu sekcji krytycznej). Takie zachowanie uniemożliwia Races z powodujących konflikt operacji, takich jak gracze są przypisani do innej gry lub wylogowania.

Nakładamy kilka ograniczeń dotyczących sposobu używania sekcji krytycznych. Ograniczenia te służą do zapobiegania zakleszczeniom i współużytkowania wątkowości.

* Sekcje krytyczne nie mogą być zagnieżdżane.
* Sekcje krytyczne nie mogą tworzyć podaranżacji.
* Sekcje krytyczne mogą wywoływać tylko jednostki, które zostały zablokowane.
* Sekcje krytyczne nie mogą wywoływać tej samej jednostki przy użyciu wielu wywołań równoległych.
* Sekcje krytyczne mogą sygnalizować tylko jednostki, które nie zostały zablokowane.

## <a name="alternate-storage-providers"></a>Alternatywni dostawcy magazynu

Usługa trwałych zadań obsługuje obecnie wielu dostawców magazynu, w tym [usługę Azure Storage](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), [emulator w pamięci](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)i eksperymentalny dostawca [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) . Jednak do tej pory rozszerzenie zadania trwałego dla Azure Functions obsługiwane tylko przez dostawcę usługi Azure Storage. Począwszy od Durable Functions 2,0, zostanie dodana obsługa alternatywnych dostawców magazynu, rozpoczynając od dostawcy Redis.

> [!NOTE]
> Durable Functions 2,0 obsługuje tylko dostawców zgodnych .NET Standard 2,0. W chwili pisania dostawca Azure Service Bus nie obsługuje .NET Standard 2,0 i dlatego nie jest dostępny jako alternatywny dostawca magazynu.

### <a name="emulator"></a>Wzorca

Dostawca [DurableTask. emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) to pamięć lokalna, nietrwały dostawca magazynu odpowiedni dla scenariuszy testowania lokalnego. Można go skonfigurować przy użyciu następującego schematu minimalnego **hosta. JSON** :

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
