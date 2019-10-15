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
ms.openlocfilehash: e3a83730e47686e9d4757f057d2e8da4629fdd7a
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2019
ms.locfileid: "72312136"
---
# <a name="entity-functions-preview"></a>Funkcje jednostki (wersja zapoznawcza)

Funkcje Entity definiują operacje umożliwiające odczytywanie i aktualizowanie małych fragmentów stanu, znanych jako *jednostek trwałych*. Podobnie jak funkcje programu Orchestrator, funkcje jednostki są funkcjami o specjalnym typie wyzwalacza, *wyzwalaczem jednostki*. W przeciwieństwie do funkcji programu Orchestrator, funkcja Entity Functions zarządza stanem jednostki jawnie, a nie niejawnie reprezentującą stan za pośrednictwem przepływu sterowania.
Jednostki zapewniają metodę skalowania aplikacji przez dystrybucję pracy w wielu jednostkach, z których każdy ma stanowy rozmiar.

> [!NOTE]
> Funkcje jednostki i powiązane funkcje są dostępne tylko w Durable Functions 2,0 i nowszych. Funkcje jednostki są obecnie w publicznej wersji zapoznawczej.

## <a name="general-concepts"></a>Pojęcia ogólne

Jednostki zachowują się jak małe usługi, które komunikują się za pośrednictwem komunikatów. Każda jednostka ma unikatową tożsamość i stan wewnętrzny (jeśli istnieje). Podobnie jak w przypadku usług lub obiektów, jednostki wykonują operacje po wyświetleniu monitu. Gdy jest wykonywana, operacja może zaktualizować stan wewnętrzny jednostki. Może również wywołać usługi zewnętrzne i poczekać na odpowiedź. Jednostki komunikują się z innymi jednostkami, aranżacjami i klientami przy użyciu komunikatów, które są niejawnie wysyłane przez niezawodne kolejki. 

Aby zapobiec konfliktom, wszystkie operacje na pojedynczej jednostce są gwarantowane do wykonania szeregowego, czyli jeden po drugim. 

### <a name="entity-id"></a>Identyfikator jednostki
Do jednostek uzyskuje się dostęp za pośrednictwem unikatowego identyfikatora, *identyfikatora jednostki*. Identyfikator jednostki to po prostu para ciągów, które jednoznacznie identyfikują wystąpienie jednostki. Składa się z:

* **Nazwa jednostki**: Nazwa identyfikująca typ jednostki (na przykład "licznik"). Ta nazwa musi być zgodna z nazwą funkcji jednostki implementującej jednostkę. Wielkość liter nie jest uwzględniana.
* **Klucz jednostki**: ciąg, który jednoznacznie identyfikuje jednostkę między wszystkimi innymi jednostkami o tej samej nazwie (na przykład identyfikator GUID).

Na przykład funkcja jednostki *licznika* może być używana do przechowywania wyników w grze online. Każde wystąpienie gry będzie miało unikatowy identyfikator jednostki, taki jak `@Counter@Game1`, `@Counter@Game2` i tak dalej. Wszystkie operacje przeznaczone dla określonej jednostki wymagają określenia identyfikatora jednostki jako parametru.

### <a name="entity-operations"></a>Operacje jednostki ###

Aby wywołać operację dla jednostki, jeden określa

* *Identyfikator jednostki* docelowej
* *Nazwa operacji*, ciąg określający operację do wykonania. Na przykład jednostka licznika może obsługiwać operacje "Add", "Get" lub "reset".
* *Dane wejściowe operacji*, które są opcjonalnym parametrem wejściowym dla operacji. Na przykład operacja "Add" może przyjmować liczbę całkowitą jako dane wejściowe.

Operacje mogą zwracać wartość wyniku lub wynik błędu (na przykład błąd JavaScript lub wyjątek .NET). Ten wynik lub błąd może być zauważalny przez aranżacje, które wywołały operację.

Operacja jednostki może również tworzyć, odczytywać, aktualizować i usuwać stan jednostki. Stan jednostki jest zawsze trwale trwały w magazynie.

## <a name="defining-entities"></a>Definiowanie jednostek

Obecnie oferujemy dwa oddzielne interfejsy API do definiowania jednostek.

**Składnia oparta na funkcjach** , w której jednostki są reprezentowane jako funkcje, a operacje są jawnie wysyłane przez aplikację. Ta składnia działa dobrze w przypadku jednostek z prostym stanem, kilkoma operacjami lub dynamicznym zestawem operacji (na przykład w strukturach aplikacji). Jednak może być żmudnym, ponieważ nie przechwytuje błędów typu w czasie kompilacji.

**Składnia oparta na klasie** , w której jednostki i operacje są reprezentowane przez klasy i metody. Ta składnia daje łatwiejszy do odczytu kod i umożliwia wywoływanie operacji w sposób bezpieczny dla typu. Składnia oparta na klasie jest tylko cienką warstwą na podstawie składni opartej na funkcjach, tak aby obie warianty mogły być używane zamiennie w tej samej aplikacji.

### <a name="example-function-based-syntax"></a>Przykład: Składnia oparta na funkcjach

Poniższy kod jest przykładem prostej jednostki *licznika* wdrożonej jako funkcja trwała. Ta funkcja definiuje trzy operacje, `add`, `reset` i `get`, z których każda działa na stanie liczby całkowitej.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

Aby uzyskać więcej informacji na temat składni opartej na funkcjach i korzystania z niej, zobacz [składnia oparta na funkcjach](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="example-class-based-syntax"></a>Przykład: Składnia oparta na klasie

Poniższy przykład jest równoważną implementacją jednostki `Counter` przy użyciu klas i metod.

```csharp
[JsonObject(MemberSerialization.OptIn)]
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

Stan tej jednostki jest obiekt typu `Counter`, który zawiera pole przechowujące bieżącą wartość licznika. Aby zachować ten obiekt w magazynie, jest on serializowany i deserializowany przez bibliotekę [JSON.NET](https://www.newtonsoft.com/json) . 

Aby uzyskać więcej informacji na temat składni opartej na klasie i korzystania z niej, zobacz [Definiowanie klas jednostek](durable-functions-dotnet-entities.md#defining-entity-classes).

## <a name="accessing-entities"></a>Uzyskiwanie dostępu do jednostek

Dostęp do jednostek można uzyskać przy użyciu jednej lub dwukierunkowej komunikacji. Używamy następującej terminologii w celu odróżnienia: 

* **Wywołanie** jednostki oznacza, że używamy komunikacji dwukierunkowej (rundy): wysyłamy komunikat operacji do jednostki, a następnie poczekaj na komunikat odpowiedzi przed kontynuowaniem. Komunikat odpowiedzi może podawać wartość wyniku lub wynik błędu (na przykład błąd JavaScript lub wyjątek .NET). Ten wynik lub błąd jest następnie zauważalny przez obiekt wywołujący.
* **Sygnalizowanie** jednostki oznacza, że używamy komunikacji jednokierunkowej (Fire i zapomnij): wysyłamy komunikat operacji, ale nie czekaj na odpowiedź. Gdy komunikat jest gwarantowany w końcu, nadawca nie wie, kiedy i nie może obserwować żadnej wartości wyniku ani błędów.

Dostęp do jednostek można uzyskać z poziomu funkcji klienta, z poziomu funkcji programu Orchestrator lub z poziomu funkcji Entity. Nie wszystkie formy komunikacji są obsługiwane przez wszystkie konteksty:

* W ramach klientów można *sygnalizować* jednostki i można *odczytać* stan jednostki.
* Z poziomu aranżacji można *sygnalizować* jednostki i można *wywoływać* jednostki.
* Z poziomu jednostek można *sygnalizować* jednostki.

Poniżej przedstawiono przykłady ilustrujące różne sposoby uzyskiwania dostępu do jednostek.

> [!NOTE]
> Dla uproszczenia w poniższych przykładach przedstawiono składnię o jednoznacznie określonym typie w celu uzyskania dostępu do jednostek. Ogólnie rzecz biorąc, zalecamy [Uzyskiwanie dostępu do jednostek za poorednictwem interfejsów](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) , ponieważ zapewnia to większą kontrolę typów.

### <a name="example-client-signals-an-entity"></a>Przykład: klient sygnalizuje jednostkę

Aby uzyskać dostęp do jednostek z zwykłej funkcji platformy Azure, znanej również jako *Funkcja kliencka* , należy użyć [powiązania danych wyjściowych klienta jednostki](durable-functions-bindings.md#entity-client). Poniższy przykład *pokazuje funkcję wyzwalaną przez* kolejkę przy użyciu tego powiązania.

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

Termin " *sygnał* " oznacza, że wywołanie interfejsu API jednostki jest jednokierunkowe i asynchroniczne. Nie jest możliwe, aby *Funkcja klienta* wiedziała, kiedy jednostka przetworzyła operację. Ponadto funkcja klienta nie może obserwować żadnych wartości wyników ani wyjątków. 

### <a name="example-client-reads-an-entity-state"></a>Przykład: klient odczytuje stan jednostki

Funkcje klienta programu mogą również wysyłać zapytania o stan jednostki, jak pokazano w następującym przykładzie:

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

Zapytania o stan jednostki są wysyłane do magazynu trwałego śledzenia i zwracają ostatnio *utrwalony* stan jednostki. Ten stan jest zawsze "przydzielony", oznacza to, że nigdy nie jest tymczasowy stan pośredni przyjęty w trakcie wykonywania operacji. Jednak jest możliwe, że ten stan jest nieodświeżony w porównaniu do stanu w pamięci obiektu. Tylko aranżacje mogą odczytywać stan w pamięci jednostki, zgodnie z opisem w następnej sekcji.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Przykład: sygnały aranżacji i wywołania jednostki

Funkcje programu Orchestrator mogą uzyskiwać dostęp do jednostek przy użyciu interfejsów API w ramach [powiązania wyzwalacza aranżacji](durable-functions-bindings.md#orchestration-trigger). Poniższy przykładowy kod pokazuje funkcję programu Orchestrator *wywołującą* i *sygnalizującą* jednostkę *licznika* .

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

   // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

Tylko aranżacje mogą wywołać jednostki i uzyskać odpowiedź, co może być wartością zwracaną lub wyjątkiem. Funkcje klienta korzystające z [powiązania klienta](durable-functions-bindings.md#entity-client) mogą jedynie *sygnalizować* jednostki.

> [!NOTE]
> Wywołanie jednostki z funkcji programu Orchestrator jest podobne do wywołania [funkcji działania](durable-functions-types-features-overview.md#activity-functions) z funkcji programu Orchestrator. Główną różnicą jest to, że funkcje jednostki są trwałymi obiektami z adresem ( *identyfikatorem jednostki*) i obsługują Określanie nazwy operacji. Funkcje działania, z drugiej strony, są bezstanowe i nie mają koncepcji operacji.

### <a name="example-entity-signals-an-entity"></a>Przykład: jednostka sygnalizuje jednostkę

Funkcja Entity może wysyłać sygnały do innych jednostek (lub nawet siebie!) podczas wykonywania operacji.
Na przykład można zmodyfikować powyższy przykład jednostki licznika, aby wysyłał sygnał "punkt kontrolny" do pewnej jednostki monitora, gdy licznik osiągnie wartość 100:

```csharp
   case "add":
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }
        currentValue += amount;
        break;
```

W poniższym fragmencie kodu pokazano, jak dołączyć wstrzykiwaną usługę do klasy Entity.

```csharp
public class HttpEntity
{
    private readonly HttpClient client;

    public HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public async Task<int> GetAsync(string url)
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
> W przeciwieństwie do używania iniekcji konstruktora w zwykłych Azure Functions .NET, Metoda punktu wejścia funkcji dla jednostek opartych na klasie *musi* być zadeklarowana `static`. Deklarowanie niestatycznego punktu wejścia funkcji może spowodować konflikty między normalnym inicjatorem obiektu Azure Functions i inicjatorem obiektów trwałe jednostki.

### <a name="bindings-in-entity-classes-net"></a>Powiązania w klasach jednostek (.NET)

W przeciwieństwie do funkcji regularnych, metody klasy jednostek nie mają bezpośredniego dostępu do powiązań wejściowych i wyjściowych. Zamiast tego, dane wiążące muszą być przechwytywane w deklaracji funkcji punktu wejścia, a następnie przekazywać do metody `DispatchAsync<T>`. Wszystkie obiekty przenoszone do `DispatchAsync<T>` będą automatycznie przesyłane do konstruktora klasy jednostki jako argument.

Poniższy przykład pokazuje, jak odwołanie `CloudBlobContainer` z [powiązania danych wejściowych obiektu BLOB](../functions-bindings-storage-blob.md#input) może zostać udostępnione dla jednostki opartej na klasie.

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

### <a name="example-transfer-funds"></a>Przykład: transfer środków

Poniższy przykładowy kod transferuje fundusze między dwiema jednostkami _kont_ przy użyciu funkcji programu Orchestrator. Koordynacja aktualizacji jednostek wymaga użycia metody `LockAsync` do utworzenia _sekcji krytycznej_ w aranżacji:

> [!NOTE]
> Dla uproszczenia w tym przykładzie użyto zdefiniowanej wcześniej jednostki `Counter`. Jednak w rzeczywistej aplikacji lepiej jest zdefiniować bardziej szczegółową jednostkę `BankAccount`.

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

W programie .NET `LockAsync` zwraca `IDisposable`, które kończą sekcję krytyczną po usunięciu. Ten @no__t wynik-0 może być używany razem z blokiem `using` w celu uzyskania składniowej reprezentacji sekcji krytycznej.

W poprzednim przykładzie funkcja programu Orchestrator przesłała fundusze z jednostki _źródłowej_ do jednostki _docelowej_ . Metoda `LockAsync` zablokowała zarówno jednostkę _źródłową_ , jak i _docelową_ . Ten blok zapewnia, że żaden inny klient nie może wykonać zapytania lub zmodyfikować stanu jednego z kont do momentu, gdy logika aranżacji nie zakończyła _sekcji krytycznej_ na końcu instrukcji `using`. Skutecznie uniemożliwia to przekroczenie możliwości przeprojektowania z konta _źródłowego_ .

> [!NOTE] 
> Gdy aranżacja kończy się (zwykle lub z powodu błędu), wszelkie krytyczne sekcje w toku są niejawnie zakończone i wszystkie blokady są zwalniane.

### <a name="critical-section-behavior"></a>Zachowanie sekcji krytycznej

Metoda `LockAsync` tworzy _sekcję krytyczną_ w aranżacji. Te _krytyczne sekcje_ uniemożliwiają innym aranżacjom wprowadzanie nakładających się zmian do określonego zestawu jednostek. Wewnętrznie interfejs API `LockAsync` wysyła operacje "Lock" do jednostek i zwraca, gdy odbierze komunikat odpowiedzi "lockd" z każdej z tych samych jednostek. *Blokady* i *odblokowywanie* to wbudowane operacje obsługiwane przez wszystkie jednostki.

Nie można wykonywać operacji z innych klientów w jednostce, gdy jest ona w stanie zablokowanym. Takie zachowanie zapewnia, że tylko jedno wystąpienie aranżacji może blokować jednostkę jednocześnie. Jeśli obiekt wywołujący podejmie próbę wywołania operacji na jednostce, gdy jest ona zablokowana przez aranżację, ta operacja zostanie umieszczona w *kolejce oczekujących operacji*. Żadne oczekujące operacje nie zostaną przetworzone do momentu, gdy organizacja holdingowa zwolni blokadę.

> [!NOTE] 
> Różni się to nieco od elementów pierwotnych synchronizacji używanych w większości języków programowania, takich jak instrukcja `lock` w C#. Na przykład w C#, instrukcja `lock` musi być używana przez wszystkie wątki, aby zapewnić poprawne synchronizację w wielu wątkach. Jednostki nie wymagają jednak, aby wszyscy wywołujący jawnie _blokowały_ jednostkę. Jeśli jakikolwiek obiekt wywołujący zablokuje jednostkę, wszystkie pozostałe operacje na tej jednostce zostaną zablokowane i umieszczone w kolejce za tę blokadę.

Blokady na jednostkach są trwałe, więc będą zachowywane nawet wtedy, gdy proces wykonywany zostanie odtworzony. Blokady są wewnętrznie utrwalane jako część trwałego stanu jednostki.

W przeciwieństwie do transakcji sekcje krytyczne nie są automatycznie wycofywane w przypadku błędów. Zamiast tego, każda obsługa błędów (wycofywanie, ponawianie lub inne) musi być jawnie zakodowana; na przykład przez przechwytywanie błędów lub wyjątków. Ten wybór projektu jest zamierzony. Automatyczne wycofywanie wszystkich efektów aranżacji jest trudne lub niemożliwe, ponieważ aranżacje mogą uruchamiać działania i wykonywać wywołania do zewnętrznych usług, których nie można cofnąć. Ponadto próby wycofania mogą się nie powieść i wymagają dalszej obsługi błędów.

### <a name="critical-section-rules"></a>Reguły sekcji krytycznej

W przeciwieństwie do elementów podstawowych blokowania niskiego poziomu w większości języków programowania, sekcje krytyczne **nie są zagwarantowane**. Aby zapobiec zakleszczeniom, Wymuś następujące ograniczenia: 

* Sekcje krytyczne nie mogą być zagnieżdżane.
* Sekcje krytyczne nie mogą tworzyć podaranżacji.
* Sekcje krytyczne mogą wywoływać tylko jednostki, które zostały zablokowane.
* Sekcje krytyczne nie mogą wywoływać tej samej jednostki przy użyciu wielu wywołań równoległych.
* Sekcje krytyczne mogą sygnalizować tylko jednostki, które nie zostały zablokowane.

Wszelkie naruszenia tych reguł powodują wystąpienie błędu czasu wykonywania (takie jak `LockingRulesViolationException` w środowisku .NET), które zawiera komunikat z wyjaśnieniem, jaka reguła została przerwana.

## <a name="comparison-with-virtual-actors"></a>Porównanie z aktorami wirtualnymi

Wiele funkcji jednostek trwałych jest inspirowany [modelem aktora](https://en.wikipedia.org/wiki/Actor_model). Jeśli znasz już uczestników, możesz rozpoznać wiele pojęć opisanych w tym artykule. Trwałe jednostki są szczególnie podobne do [aktorów wirtualnych](https://research.microsoft.com/projects/orleans/)lub *ziaren*, które są popularne przez [projekt Orleans](http://dotnet.github.io/orleans/). Na przykład:

* Jednostki trwałe są adresowane za pośrednictwem *identyfikatora jednostki*.
* Trwałe operacje jednostki wykonują szeregowo, pojedynczo, aby uniknąć sytuacji wyścigu.
* Trwałe jednostki są tworzone niejawnie, gdy są wywoływane lub sygnalizowane.
* W przypadku braku wykonywania operacji trwałe jednostki są dyskretnie zwalniane z pamięci.

Istnieją jednak pewne istotne różnice, które warto wziąć pod uwagę:

* Trwałe jednostki ustalają priorytety *trwałości* przed *opóźnieniami*i w ten sposób mogą nie być odpowiednie dla aplikacji z rygorystycznymi wymaganiami opóźnienia.
* Jednostki trwałe nie mają wbudowanych limitów czasu dla komunikatów. W Orleans, wszystkie komunikaty przekroczą limit czasu po skonfigurowaniu czasu (domyślnie 30 sekund).
* Komunikaty wysyłane między jednostkami są dostarczane w sposób niezawodny i w kolejności. W Orleans, niezawodne lub uporządkowane dostarczanie jest obsługiwane w przypadku zawartości wysyłanej za poorednictwem strumieni, ale nie jest gwarantowane dla wszystkich komunikatów między ziarnami.
* Wzorce żądania/odpowiedzi w jednostkach są ograniczone do aranżacji. W obrębie jednostek dozwolony jest tylko jednokierunkowa obsługa komunikatów (nazywanych także "sygnalizacją"), jak w oryginalnym modelu aktora i w przeciwieństwie do ziaren w Orleans. 
* Trwałe jednostki nie są zakleszczony. W Orleans mogą wystąpić zakleszczenia (i nie należy rozwiązywać problemów przed upływem limitu czasu komunikatów).
* Trwałe jednostki mogą być używane w połączeniu z trwałymi aranżacjami i obsługują mechanizmy blokowania rozproszonego. 


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przewodnikiem dewelopera w przypadku trwałych jednostek w programie .NET](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Informacje o centrach zadań](durable-functions-task-hubs.md)
