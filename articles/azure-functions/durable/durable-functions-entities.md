---
title: Trwałe jednostki — Azure Functions
description: Dowiedz się, co to są trwałe jednostki i jak ich używać w rozszerzeniu Durable Functions Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 419505404214d60a2c928770a34384f80c8446cb
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935873"
---
# <a name="entity-functions-preview"></a>Funkcje jednostki (wersja zapoznawcza)

Funkcje Entity definiują operacje umożliwiające odczytywanie i aktualizowanie małych fragmentów stanu, znanych jako *jednostek trwałych*. Podobnie jak funkcje programu Orchestrator, funkcje jednostki są funkcjami o specjalnym typie wyzwalacza, *wyzwalaczem jednostki*. W przeciwieństwie do funkcji programu Orchestrator, funkcje jednostek nie mają żadnych ograniczeń związanych z kodem. Funkcje jednostek również zarządzają stanem jawnie, a nie niejawnie reprezentującą stan za pośrednictwem przepływu sterowania.

> [!NOTE]
> Funkcje jednostki i powiązane funkcje są dostępne tylko w Durable Functions 2,0 i nowszych. Funkcje jednostki są obecnie w publicznej wersji zapoznawczej.

## <a name="entity-identity"></a>Tożsamość jednostki

Jednostki (czasami określane jako *wystąpienia*jednostek) są dostępne za pośrednictwem unikatowego identyfikatora, *identyfikatora jednostki*. Identyfikator jednostki to po prostu para ciągów, które jednoznacznie identyfikują wystąpienie jednostki. Składa się z:

* **Nazwa jednostki**: Nazwa identyfikująca typ jednostki (na przykład "licznik").
* **Klucz jednostki**: ciąg, który jednoznacznie identyfikuje jednostkę między wszystkimi innymi jednostkami o tej samej nazwie (na przykład identyfikator GUID).

Na przykład funkcja jednostki *licznika* może być używana do przechowywania wyników w grze online. Każde wystąpienie gry będzie miało unikatowy identyfikator jednostki, na `@Counter@Game1`przykład, `@Counter@Game2`, i tak dalej. Wszystkie operacje przeznaczone dla określonej jednostki wymagają określenia identyfikatora jednostki jako parametru.

## <a name="programing-models"></a>Modele programów

Trwałe jednostki obsługują dwa różne modele programowania. Pierwszy model jest dynamicznym modelem funkcjonalnym, w którym jednostka jest definiowana przez pojedynczą funkcję. Drugi model jest modelem zorientowanym na obiekt, w którym jednostka jest definiowana przez klasę i metody. Te modele i modele programowania umożliwiające współdziałanie z jednostkami są opisane w następnych sekcjach.

### <a name="defining-entities"></a>Definiowanie jednostek

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

Ten model działa najlepiej w przypadku prostych implementacji jednostek lub implementacji, które mają dynamiczny zestaw operacji. Można jednak również użyć modelu programowania opartego na klasach, który jest przydatny dla jednostek, które są statyczne, ale mają bardziej złożone implementacje. Poniższy przykład jest równoważną implementacją `Counter` jednostki przy użyciu klas i metod.

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

> [!NOTE]
> Metoda punktu wejścia funkcji z `[FunctionName]` atrybutem *musi* być zadeklarowana `static` w przypadku używania klas jednostek. Niestatyczne metody punktu wejścia mogą spowodować inicjalizację wielu obiektów oraz inne niezdefiniowane zachowania.

W modelu `IDurableEntityContext` programowania opartym na klasie obiekt jest dostępny `Entity.Current` we właściwości statycznej.

Model oparty na klasie jest podobny do modelu programowania popularnego przez [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). W tym modelu typ jednostki jest zdefiniowany jako Klasa platformy .NET. Każda metoda klasy jest operacją, którą może wywołać Klient zewnętrzny. W przeciwieństwie do orleans interfejsy .NET są jednak opcjonalne. Poprzedni przykład *licznika* nie korzystał z interfejsu, ale nadal może być wywoływany przez inne funkcje lub wywołania interfejsu API protokołu HTTP.

> [!NOTE]
> Funkcje wyzwalacza jednostki są dostępne w Durable Functions 2,0 i nowszych. Obecnie funkcje wyzwalacza jednostki są dostępne tylko dla aplikacji funkcji platformy .NET.

### <a name="accessing-entities-from-clients"></a>Uzyskiwanie dostępu do jednostek z klientów

Jednostki trwałe mogą być wywoływane lub wysyłane zapytania od zwykłych funkcji — znanych także jako *funkcje klienta* — za pomocą [powiązania danych wyjściowych klienta jednostki](durable-functions-bindings.md#entity-client). Poniższy przykład *pokazuje funkcję wyzwalaną przez* kolejkę przy użyciu tego powiązania.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

> [!NOTE]
> Funkcje platformy .NET obsługują zarówno luźno wpisane metody, jak i bezpieczne typy sygnalizujące jednostki. Szczegóły można znaleźć w dokumentacji dotyczącej [powiązań klienta jednostki](durable-functions-bindings.md#entity-client-usage) .

Termin " *sygnał* " oznacza, że wywołanie interfejsu API jednostki jest jednokierunkowe i asynchroniczne. Nie jest możliwe, aby *Funkcja klienta* wiedziała, kiedy jednostka przetworzyła operację, ani nie jest możliwe, aby funkcja jednostki zwracała wartość do funkcji klienta. Jednokierunkowa obsługa komunikatów oparta na kolejkach polega na zamierzonym wyborze jednostek trwałych w celu określenia priorytetów względem wydajności. Ten wybór projektowy jest jednym z kompromisów trwałych jednostek w porównaniu z innymi, podobnymi technologiami. Obecnie tylko aranżacje mogą obsługiwać wartości zwracane z jednostek, zgodnie z opisem w następnej sekcji.

Funkcje klienta programu mogą również wysyłać zapytania o stan jednostek, jak pokazano w następującym przykładzie:

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    JObject state = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, state);
}
```

Zapytania o stan jednostki są wysyłane do magazynu trwałego śledzenia i zwracają ostatnio *utrwalony* stan jednostki. Możliwe, że zwrócony stan może być nieodświeżony w porównaniu z stanem w pamięci jednostki. Tylko aranżacje mogą odczytywać stan w pamięci jednostki, zgodnie z opisem w następnej sekcji.

### <a name="accessing-entities-from-orchestrations"></a>Uzyskiwanie dostępu do jednostek z aranżacji

Funkcje programu Orchestrator mogą uzyskiwać dostęp do jednostek przy użyciu interfejsów API w ramach [powiązania wyzwalacza aranżacji](durable-functions-bindings.md#orchestration-trigger). Funkcje programu Orchestrator mogą wybierać między komunikacją jednokierunkową (pożar i zapomnij, zwane również *sygnałem*) oraz komunikacji dwukierunkowej (żądanie i odpowiedź, nazywane również *wywołaniem*). Poniższy przykładowy kod pokazuje funkcję programu Orchestrator *wywołującą* i *sygnalizującą* jednostkę *licznika* .

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Synchronous call to the entity which returns a value
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // Asynchronous call which updates the value
        await context.SignalEntityAsync<int>(entityId, "Add", 1);
    }
}
```

Tylko aranżacje mogą wywołać jednostki i uzyskać odpowiedź, co może być wartością zwracaną lub wyjątkiem. Funkcje klienta korzystające z [powiązania klienta](durable-functions-bindings.md#entity-client) mogą jedynie *sygnalizować* jednostki.

> [!NOTE]
> Wywołanie jednostki z funkcji orchestrtor jest podobne do wywoływania [funkcji działania](durable-functions-types-features-overview.md#activity-functions) z funkcji programu Orchestrator. Główną różnicą jest to, że funkcje jednostki są trwałymi obiektami z adresem ( *identyfikatorem jednostki*) i obsługują Określanie nazwy operacji. Funkcje działania, z drugiej strony, są bezstanowe i nie mają koncepcji operacji.

### <a name="dependency-injection-in-entity-classes-net"></a>Iniekcja zależności w klasach jednostek (.NET)

Klasy jednostek obsługują [iniekcję zależności Azure Functions](../functions-dotnet-dependency-injection.md). Poniższy przykład pokazuje, jak zarejestrować `IHttpClientFactory` usługę w jednostce opartej na klasie.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

W poniższym fragmencie kodu pokazano, jak dołączyć wstrzykiwaną usługę do klasy Entity.

```csharp
public class HttpEntity
{
    private readonly HttpClient client;

    public class HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    // The function entry point must be declared static
    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> W przeciwieństwie do używania iniekcji konstruktora w zwykłych Azure Functions .NET, Metoda punktu wejścia funkcji dla jednostek opartych na klasie musi `static`być zadeklarowana. Deklarowanie niestatycznego punktu wejścia funkcji może spowodować konflikty między normalnym inicjatorem obiektu Azure Functions i inicjatorem obiektów trwałe jednostki.

### <a name="bindings-in-entity-classes-net"></a>Powiązania w klasach jednostek (.NET)

W przeciwieństwie do funkcji regularnych, metody klasy jednostek nie mają bezpośredniego dostępu do powiązań wejściowych i wyjściowych. Zamiast tego, dane wiążące muszą być przechwytywane w deklaracji funkcji punktu wejścia, a następnie przekazywać do `DispatchAsync<T>` metody. Wszystkie obiekty, do `DispatchAsync<T>` których przechodzą, zostaną automatycznie przesłane do konstruktora klasy jednostki jako argument.

Poniższy przykład pokazuje, `CloudBlobContainer` jak odwołanie z [powiązania danych wejściowych obiektu BLOB](../functions-bindings-storage-blob.md#input) może zostać udostępnione jednostce opartej na klasie.

```csharp
public class BlobBackedEntity
{
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

Aby uzyskać więcej informacji na temat powiązań w Azure Functions, zapoznaj się z dokumentacją [Azure Functions wyzwalacze i powiązania](../functions-triggers-bindings.md) .

## <a name="entity-coordination"></a>Koordynacja jednostek

Mogą wystąpić sytuacje, w których trzeba skoordynować operacje w wielu jednostkach. Na przykład w aplikacji bankowej mogą istnieć jednostki reprezentujące poszczególne konta bankowe. W przypadku przenoszenia funduszy z jednego konta do innego należy upewnić się, że konto _źródłowe_ ma wystarczające środki i że aktualizacje zarówno dla konta _źródłowego_ , jak i _docelowego_ są wykonywane w sposób spójny.

### <a name="transfer-funds-example-in-c"></a>Przykład transferu pieniędzy wC#

Poniższy przykładowy kod transferuje fundusze między dwiema jednostkami _kont_ przy użyciu funkcji programu Orchestrator. Koordynacja aktualizacji jednostek wymaga użycia `LockAsync` metody do utworzenia _sekcji krytycznej_ w aranżacji:

> [!NOTE]
> Dla uproszczenia w tym przykładzie użyto `Counter` wcześniej zdefiniowanej jednostki. Jednak w rzeczywistej aplikacji lepiej jest zdefiniować bardziej szczegółową `BankAccount` jednostkę.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

W programie .NET `LockAsync` `IDisposable` funkcja zwraca wartość, która powoduje zakończenie sekcji krytycznej po jej zlikwidowaniu. Tego `IDisposable` wyniku można używać razem `using` z blokiem, aby uzyskać składniową reprezentację sekcji krytycznej.

W poprzednim przykładzie funkcja programu Orchestrator przesłała fundusze z jednostki _źródłowej_ do jednostki _docelowej_ . Metoda została zablokowana zarówno dla jednostki konta _źródłowego_ , jak i _docelowego._ `LockAsync` To blokowanie zapewnia, że żaden inny klient nie może wykonać zapytania lub zmodyfikować stanu jednego z kont, dopóki logika aranżacji nie zakończyła _sekcji krytycznej_ na `using` końcu instrukcji. Skutecznie uniemożliwia to przekroczenie możliwości przeprojektowania z konta _źródłowego_ .

### <a name="critical-section-behavior"></a>Zachowanie sekcji krytycznej

Metoda tworzy _sekcję krytyczną_ w aranżacji. `LockAsync` Te _krytyczne sekcje_ uniemożliwiają innym aranżacjom wprowadzanie nakładających się zmian do określonego zestawu jednostek. `LockAsync` Wewnętrznie interfejs API wysyła do jednostek operacje "Lock" i zwraca, gdy odbierze komunikat odpowiedzi "Zablokuj pobrany" z każdej z tych samych jednostek. *Blokady* i *odblokowywanie* to wbudowane operacje obsługiwane przez wszystkie jednostki.

Nie można wykonywać operacji z innych klientów w jednostce, gdy jest ona w stanie zablokowanym. Takie zachowanie zapewnia, że tylko jedno wystąpienie aranżacji może blokować jednostkę jednocześnie. Jeśli obiekt wywołujący podejmie próbę wywołania operacji na jednostce, gdy jest ona zablokowana przez aranżację, ta operacja zostanie umieszczona w *kolejce oczekujących operacji*. Żadne oczekujące operacje nie zostaną przetworzone do momentu, gdy organizacja holdingowa zwolni blokadę.

> [!NOTE] 
> Różni się to nieco od elementów pierwotnych synchronizacji używanych w większości języków programowania, takich jak `lock` instrukcja w C#. Na przykład w C#, `lock` instrukcja musi być używana przez wszystkie wątki, aby zapewnić poprawne synchronizację w wielu wątkach. Jednostki nie wymagają jednak, aby wszyscy wywołujący jawnie _blokowały_ jednostkę. Jeśli jakikolwiek obiekt wywołujący zablokuje jednostkę, wszystkie pozostałe operacje na tej jednostce zostaną zablokowane i umieszczone w kolejce za tę blokadę.

Blokady na jednostkach są trwałe, więc będą zachowywane nawet wtedy, gdy proces wykonywany zostanie odtworzony. Blokady są wewnętrznie utrwalane jako część trwałego stanu jednostki.

### <a name="critical-section-restrictions"></a>Ograniczenia sekcji krytycznej

Nakładamy kilka ograniczeń dotyczących sposobu używania sekcji krytycznych. Ograniczenia te służą do zapobiegania zakleszczeniom i współużytkowania wątkowości.

* Sekcje krytyczne nie mogą być zagnieżdżane.
* Sekcje krytyczne nie mogą tworzyć podaranżacji.
* Sekcje krytyczne mogą wywoływać tylko jednostki, które zostały zablokowane.
* Sekcje krytyczne nie mogą wywoływać tej samej jednostki przy użyciu wielu wywołań równoległych.
* Sekcje krytyczne mogą sygnalizować tylko jednostki, które nie zostały zablokowane.

## <a name="comparison-with-virtual-actors"></a>Porównanie z aktorami wirtualnymi

Wiele funkcji jednostek trwałych jest inspirowany [modelem aktora](https://en.wikipedia.org/wiki/Actor_model). Jeśli znasz już uczestników, możesz rozpoznać wiele pojęć opisanych w tym artykule. W szczególności trwałe jednostki są podobne do [aktorów wirtualnych](https://research.microsoft.com/projects/orleans/) na wiele sposobów:

* Jednostki trwałe są adresowane za pośrednictwem *identyfikatora jednostki*.
* Trwałe operacje jednostki wykonują szeregowo, pojedynczo, aby uniknąć sytuacji wyścigu.
* Trwałe jednostki są tworzone automatycznie po wywołaniu lub zasygnalizowaniu.
* W przypadku braku wykonywania operacji trwałe jednostki są dyskretnie zwalniane z pamięci.

Istnieją jednak pewne istotne różnice, które warto wziąć pod uwagę:

* Trwałe jednostki ustalają priorytety *trwałości* przed *opóźnieniami*i w ten sposób mogą nie być odpowiednie dla aplikacji z rygorystycznymi wymaganiami opóźnienia.
* Komunikaty wysyłane między jednostkami są dostarczane w sposób niezawodny i w kolejności.
* Trwałe jednostki mogą być używane w połączeniu z trwałymi aranżacjami i obsługują mechanizmy blokowania rozproszonego.
* Wzorce żądania/odpowiedzi w jednostkach są ograniczone do aranżacji. W przypadku komunikacji między *klientem* *i jednostką* , tylko jednokierunkowa obsługa komunikatów (nazywanych także "sygnalizacją") jest dozwolona, jak w oryginalnym modelu aktora. Takie zachowanie uniemożliwia rozproszone zakleszczenie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o centrach zadań](durable-functions-task-hubs.md)