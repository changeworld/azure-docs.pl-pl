---
title: Przewodnik dla deweloperów dotyczący trwałych jednostek w witrynie .NET — usługi Azure Functions
description: Jak pracować z trwałymi jednostkami w .NET z rozszerzeniem Funkcje trwałe dla usług Azure Functions.
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 01e07eaee705634b03cc4462c4058e290daa8bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278131"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>Przewodnik dla deweloperów dotyczący trwałych jednostek w pliku .NET

W tym artykule opisano dostępne interfejsy do tworzenia trwałych jednostek z .NET szczegółowo, w tym przykłady i porady ogólne. 

Funkcje encji zapewniają deweloperom aplikacji bezserwerowych wygodny sposób organizowania stanu aplikacji jako kolekcji jednostek szczegółowe. Aby uzyskać więcej informacji na temat podstawowych pojęć, zobacz [trwałe jednostki: Pojęcia](durable-functions-entities.md) artykułu.

Obecnie oferujemy dwa interfejsy API do definiowania jednostek:

- Składnia **oparta na klasach** reprezentuje jednostki i operacje jako klasy i metody. Ta składnia tworzy czytelny kod i umożliwia wywoływanie operacji w sposób sprawdzany przez typ za pośrednictwem interfejsów. 

- Składnia **oparta na funkcjach** jest interfejsem niższego poziomu, który reprezentuje jednostki jako funkcje. Zapewnia precyzyjną kontrolę nad tym, jak operacje jednostki są wywoływane i jak zarządzany jest stan jednostki.  

W tym artykule koncentruje się przede wszystkim na składni opartej na klasach, ponieważ oczekujemy, że będzie lepiej nadaje się do większości aplikacji. Jednak [składnia oparta na funkcjach](#function-based-syntax) może być odpowiednia dla aplikacji, które chcą zdefiniować lub zarządzać własnymi abstrakcjami dla stanu jednostki i operacji. Ponadto może być odpowiednie dla implementacji bibliotek, które wymagają ogólnegou nie jest obecnie obsługiwany przez składnię opartą na klasach. 

> [!NOTE]
> Składnia oparta na klasach jest tylko warstwą na górze składni opartej na funkcjach, więc oba warianty mogą być używane zamiennie w tej samej aplikacji. 
 
## <a name="defining-entity-classes"></a>Definiowanie klas jednostek

Poniższy przykład `Counter` jest implementacją jednostki, która przechowuje pojedynczą wartość `Add`typu `Reset` `Get`liczby `Delete`całkowitej i oferuje cztery operacje , , i .

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int Value { get; set; }

    public void Add(int amount) 
    {
        this.Value += amount;
    }

    public Task Reset() 
    {
        this.Value = 0;
        return Task.CompletedTask;
    }

    public Task<int> Get() 
    {
        return Task.FromResult(this.Value);
    }

    public void Delete() 
    {
        Entity.Current.DeleteState();
    }

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

Funkcja `Run` zawiera standardową płytę wymaganą do użycia składni opartej na klasach. Musi to być *statyczna* funkcja platformy Azure. Wykonuje jeden raz dla każdego komunikatu operacji, który jest przetwarzany przez jednostkę. Gdy `DispatchAsync<T>` jest wywoływana, a jednostka nie jest jeszcze w `T` pamięci, konstruuje obiekt typu i wypełnia jego pola z ostatniego utrwalone JSON znaleźć w magazynie (jeśli istnieje). Następnie wywołuje metodę o pasującej nazwie.

> [!NOTE]
> Stan jednostki opartej na klasach jest **tworzony niejawnie** przed procesem operacji przez `Entity.Current.DeleteState()`jednostkę i można go **jawnie usunąć** w operacji przez wywołanie .

### <a name="class-requirements"></a>Wymagania klasy
 
Klasy jednostek są POCO (zwykły stary CLR obiektów), które nie wymagają specjalnych superclasses, interfejsy lub atrybuty. Jednak:

- Klasa musi być konstrukcyjna (patrz [Konstrukcja jednostki](#entity-construction)).
- Klasa musi być serializable JSON (patrz [serializacja jednostki).](#entity-serialization)

Ponadto każda metoda, która ma być wywoływana jako operacja, musi spełniać dodatkowe wymagania:

- Operacja musi mieć co najwyżej jeden argument i nie może mieć żadnych przeciążeń lub argumentów typu ogólnego.
- Operacja przeznaczona do wywołania z aranżacji przy użyciu interfejsu musi powrócić `Task` lub `Task<T>`.
- Argumenty i zwracane wartości muszą być wartościami lub obiektami, które można serializować.

### <a name="what-can-operations-do"></a>Co może zrobić operacje?

Wszystkie operacje jednostki można odczytać i zaktualizować stan jednostki, a zmiany w stanie są automatycznie utrwalone do magazynu. Ponadto operacje mogą wykonywać zewnętrzne operacje we/wy lub inne obliczenia, w ramach ogólnych limitów wspólnych dla wszystkich funkcji platformy Azure.

Operacje mają również dostęp do `Entity.Current` funkcji zapewnianych w kontekście:

* `EntityName`: nazwa aktualnie wykonywanej jednostki.
* `EntityKey`: klucz aktualnie wykonywanej jednostki.
* `EntityId`: identyfikator aktualnie wykonywanej jednostki (zawiera nazwę i klucz).
* `SignalEntity`: wysyła wiadomość jednokierunkową do jednostki.
* `CreateNewOrchestration`: rozpoczyna nową aranżację.
* `DeleteState`: usuwa stan tej jednostki.

Na przykład możemy zmodyfikować jednostkę licznika, tak aby uruchamiała aranżację, gdy licznik osiągnie 100 i przekazuje identyfikator jednostki jako argument wejściowy:

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount > 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId)
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>Bezpośredni dostęp do jednostek

Jednostki oparte na klasach są dostępne bezpośrednio, przy użyciu jawnych nazw ciągów dla jednostki i jej operacji. Poniżej przedstawiamy kilka przykładów; aby uzyskać głębsze wyjaśnienie podstawowych pojęć (takich jak sygnały a wywołania) zobacz dyskusję w [jednostkach programu Access](durable-functions-entities.md#access-entities). 

> [!NOTE]
> W miarę możliwości zaleca się [uzyskiwanie dostępu do jednostek za pośrednictwem interfejsów,](#accessing-entities-through-interfaces)ponieważ zapewnia ona więcej sprawdzania typu.

### <a name="example-client-signals-entity"></a>Przykład: jednostka sygnałów klienta

Następująca funkcja http platformy Azure implementuje operację DELETE przy użyciu konwencji REST. Wysyła sygnał usuwania do jednostki licznika, którego klucz jest przekazywany w ścieżce adresu URL.

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync(entityId, "Delete");    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

### <a name="example-client-reads-entity-state"></a>Przykład: klient odczytuje stan jednostki

Następująca funkcja http platformy Azure implementuje operację GET przy użyciu konwencji REST. Odczytuje bieżący stan jednostki licznika, którego klucz jest przekazywany w ścieżce adresu URL.

```csharp
[FunctionName("GetCounter")]
public static async Task<HttpResponseMessage> GetCounter(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    var state = await client.ReadEntityStateAsync<Counter>(entityId); 
    return req.CreateResponse(state);
}
```

> [!NOTE]
> Obiekt zwrócony `ReadEntityStateAsync` przez jest tylko kopią lokalną, czyli migawką stanu jednostki z jakiegoś wcześniejszego punktu w czasie. W szczególności może być przestarzały, a modyfikowanie tego obiektu nie ma wpływu na rzeczywistą jednostkę. 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>Przykład: najpierw sygnały aranżacji, a następnie wywołuje encję

Następujące aranżacji sygnalizuje jednostki licznika, aby ją przyrost, a następnie wywołuje tę samą jednostkę, aby odczytać jego najnowszą wartość.

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");

    // One-way signal to the entity - does not await a response
    context.SignalEntity(entityId, "Add", 1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");

    return currentValue;
}
```

## <a name="accessing-entities-through-interfaces"></a>Uzyskiwanie dostępu do encji za pośrednictwem interfejsów

Interfejsy mogą służyć do uzyskiwania dostępu do jednostek za pośrednictwem wygenerowanych obiektów proxy. Takie podejście zapewnia, że nazwa i typ argumentu operacji odpowiada temu, co jest implementowane. Firma Microsoft zaleca korzystanie z interfejsów do uzyskiwania dostępu do jednostek, gdy jest to możliwe.

Na przykład możemy zmodyfikować przykład licznika w następujący sposób:

```csharp
public interface ICounter
{
    void Add(int amount);
    Task Reset();
    Task<int> Get();
    void Delete();
}

public class Counter : ICounter
{
    ...
}
```

Klasy jednostek i interfejsy jednostek są podobne do interfejsów ziarna i ziarna spopularyzowanych przez [Orlean](https://www.microsoft.com/research/project/orleans-virtual-actors/). Aby uzyskać więcej informacji na temat podobieństw i różnic między trwałymi jednostkami a Orleanem, zobacz [Porównanie z podmiotami wirtualnymi](durable-functions-entities.md#comparison-with-virtual-actors).

Oprócz zapewnienia sprawdzania typu, interfejsy są przydatne dla lepszego oddzielenia problemów w aplikacji. Na przykład ponieważ jednostka może implementować wiele interfejsów, pojedyncza jednostka może obsługiwać wiele ról. Ponadto ponieważ interfejs może być zaimplementowany przez wiele jednostek, ogólne wzorce komunikacji mogą być implementowane jako biblioteki wielokrotnegoużytnia.

### <a name="example-client-signals-entity-through-interface"></a>Przykład: jednostka sygnałów klienta za pośrednictwem interfejsu

Kod klienta `SignalEntityAsync<TEntityInterface>` może używać do wysyłania `TEntityInterface`sygnałów do jednostek, które implementują . Przykład:

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync<ICounter>(entityId, proxy => proxy.Delete());    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

W tym przykładzie `proxy` parametr jest dynamicznie `ICounter`generowanym wystąpieniem , `Delete` które wewnętrznie tłumaczy wywołanie na sygnał.

> [!NOTE]
> Interfejsy `SignalEntityAsync` API mogą być używane tylko dla operacji jednokierunkowych. Nawet jeśli operacja `Task<T>`zwraca , `T` wartość parametru zawsze `default`będzie null lub , nie rzeczywisty wynik.
Na przykład nie ma sensu sygnalizować `Get` operacji, ponieważ nie jest zwracana żadna wartość. Zamiast tego klienci mogą `ReadStateAsync` używać albo do bezpośredniego dostępu do stanu licznika lub `Get` można uruchomić funkcję koordynatora, który wywołuje operację. 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>Przykład: najpierw aranżacja sygnałów, a następnie wywołuje encję za pośrednictwem serwera proxy

Aby wywołać lub zasygnalizować `CreateEntityProxy` jednostkę z poziomu aranżacji, można użyć, wraz z typem interfejsu, do wygenerowania serwera proxy dla jednostki. Ten serwer proxy może być następnie używany do wywoływania lub sygnalizowania operacji:

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");
    var proxy = context.CreateEntityProxy<ICounter>(entityId);

    // One-way signal to the entity - does not await a response
    proxy.Add(1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await proxy.Get();

    return currentValue;
}
```

Niejawnie wszystkie operacje, które zwracają `void` są sygnalizowane i wszelkie operacje, które zwracają `Task` lub `Task<T>` są wywoływane. Można zmienić to domyślne zachowanie i operacje sygnału, `SignalEntity<IInterfaceType>` nawet jeśli zwracają zadanie, przy użyciu metody jawnie.

### <a name="shorter-option-for-specifying-the-target"></a>Krótsza opcja określania celu

Podczas wywoływania lub sygnalizowania jednostki przy użyciu interfejsu, pierwszy argument należy określić jednostki docelowej. Obiekt docelowy można określić, określając identyfikator jednostki lub, w przypadkach, gdy istnieje tylko jedna klasa, która implementuje jednostkę, tylko klucz jednostki:

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

Jeśli określono tylko klucz jednostki i nie można znaleźć unikatowej implementacji w czasie wykonywania, `InvalidOperationException` jest rzutowy. 

### <a name="restrictions-on-entity-interfaces"></a>Ograniczenia dotyczące interfejsów encji

Jak zwykle wszystkie typy parametrów i zwracania muszą być możliwe do serializacji JSON. W przeciwnym razie wyjątki serializacji są generowane w czasie wykonywania.

Egzekwujemy również niektóre dodatkowe reguły:
* Interfejsy jednostek muszą definiować tylko metody.
* Interfejsy jednostek nie mogą zawierać parametrów ogólnych.
* Metody interfejsu jednostki nie może mieć więcej niż jeden parametr.
* Metody interfejsu jednostki muszą zwracać `void`, `Task`lub`Task<T>` 

Jeśli którakolwiek z tych reguł `InvalidOperationException` zostanie naruszona, jest wyrzucany w czasie `SignalEntity` `CreateProxy`wykonywania, gdy interfejs jest używany jako argument typu do lub . Komunikat o wyjątku wyjaśnia, która reguła została złamana.

> [!NOTE]
> Zwracane `void` metody interfejsu mogą być sygnalizowane tylko (jednokierunkowe), a nie wywoływane (dwukierunkowe). Metody interfejsu `Task` zwracane lub `Task<T>` mogą być wywoływane lub sygnalizowane. Jeśli wywołane, zwracają wynik operacji lub ponownie zgłaszać wyjątki generowane przez operację. Jednak po zasygnalizowana nie zwracają rzeczywisty wynik lub wyjątek od operacji, ale tylko wartość domyślna.

## <a name="entity-serialization"></a>Serializacja encji

Ponieważ stan jednostki jest trwale utrwalone, klasa jednostki musi być serializable. Środowisko uruchomieniowe funkcji trwałych używa [biblioteki Json.NET](https://www.newtonsoft.com/json) w tym celu, która obsługuje szereg zasad i atrybutów do kontrolowania procesu serializacji i deserializacji. Najczęściej używane typy danych języka C# (w tym tablice i typy kolekcji) są już serializable i można łatwo używać do definiowania stanu trwałych jednostek.

Na przykład Json.NET można łatwo serializować i deserialize następujące klasy:

```csharp
[JsonObject(MemberSerialization = MemberSerialization.OptIn)]
public class User
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("yearOfBirth")]
    public int YearOfBirth { get; set; }

    [JsonProperty("timestamp")]
    public DateTime Timestamp { get; set; }

    [JsonProperty("contacts")]
    public Dictionary<Guid, Contact> Contacts { get; set; } = new Dictionary<Guid, Contact>();

    [JsonObject(MemberSerialization = MemberSerialization.OptOut)]
    public struct Contact
    {
        public string Name;
        public string Number;
    }

    ...
}
```

### <a name="serialization-attributes"></a>Atrybuty serializacji

W powyższym przykładzie zdecydowaliśmy się uwzględnić kilka atrybutów, aby podstawowa serializacja była bardziej widoczna:
- Możemy adnotacje klasy `[JsonObject(MemberSerialization.OptIn)]` z, aby przypomnieć nam, że klasa musi być serializable i utrzymywać tylko elementy członkowskie, które są jawnie oznaczone jako właściwości JSON.
-  Możemy dodawać adnotacje do `[JsonProperty("name")]` pól, które mają być utrwalane, aby przypomnieć nam, że pole jest częścią stanu utrwalone jednostki i określić nazwę właściwości, która ma być używana w reprezentacji JSON.

Jednak te atrybuty nie są wymagane; inne konwencje lub atrybuty są dozwolone, o ile współpracują z Json.NET. Na przykład można `[DataContract]` użyć atrybutów lub w ogóle nie atrybuty:

```csharp
[DataContract]
public class Counter
{
    [DataMember]
    public int Value { get; set; }
    ...
}

public class Counter
{
    public int Value;
    ...
}
```

Domyślnie nazwa klasy *nie* jest przechowywana jako część reprezentacji JSON: `TypeNameHandling.None` oznacza to, że używamy jako ustawienie domyślne. To domyślne zachowanie można `JsonObject` zastąpić `JsonProperty` za pomocą lub atrybuty.

### <a name="making-changes-to-class-definitions"></a>Wprowadzanie zmian w definicjach klas

Niektóre ostrożność jest wymagana podczas wprowadzania zmian w definicji klasy po uruchomieniu aplikacji, ponieważ przechowywany obiekt JSON może już nie odpowiadać nowej definicji klasy. Mimo to często można prawidłowo radzić sobie ze zmianą formatów danych, o `JsonConvert.PopulateObject`ile rozumie się proces deserializacji używany przez program .

Oto na przykład kilka przykładów zmian i ich skutków:

1. Jeśli zostanie dodana nowa właściwość, która nie jest obecna w przechowywanym JSON, przyjmuje jego wartość domyślną.
1. Jeśli właściwość zostanie usunięta, która jest obecna w przechowywanym JSON, poprzednia zawartość zostanie utracona.
1. Jeśli nazwa właściwości jest zmieniana, efekt jest tak, jakby usunięcie starego i dodanie nowej.
1. Jeśli typ właściwości zostanie zmieniony, więc nie można już deserializacji z przechowywanego JSON, wyjątek.
1. Jeśli typ właściwości zostanie zmieniony, ale nadal może być deserializowane z przechowywane JSON, zrobi to.

Dostępnych jest wiele opcji dostosowywania zachowania Json.NET. Na przykład, aby wymusić wyjątek, jeśli przechowywany JSON zawiera pole, które `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)`nie jest obecne w klasie, określ atrybut . Istnieje również możliwość zapisu kodu niestandardowego dla deserializacji, który może odczytywać JSON przechowywane w dowolnych formatach.

## <a name="entity-construction"></a>Budowa jednostki

Czasami chcemy wywierać większą kontrolę nad tym, jak obiekty jednostki są konstruowane. Teraz opisano kilka opcji zmiany zachowania domyślnego podczas konstruowania obiektów jednostki. 

### <a name="custom-initialization-on-first-access"></a>Inicjowanie niestandardowe przy pierwszym dostępie

Od czasu do czasu musimy wykonać niektóre specjalne inicjowanie przed wysłaniem operacji do jednostki, która nigdy nie była dostępna lub która została usunięta. Aby określić to zachowanie, można dodać `DispatchAsync`warunek przed:

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    return ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>Powiązania w klasach encji

W przeciwieństwie do regularnych funkcji metody klasy jednostki nie mają bezpośredniego dostępu do powiązań wejściowych i wyjściowych. Zamiast tego dane wiązania muszą być przechwytywane w deklaracji `DispatchAsync<T>` funkcji punktu wejścia, a następnie przekazywane do metody. Wszystkie obiekty `DispatchAsync<T>` przekazywane do zostaną automatycznie przekazane do konstruktora klasy jednostki jako argument.

Poniższy przykład pokazuje, `CloudBlobContainer` jak odwołanie z powiązania [wejściowego obiektu blob](../functions-bindings-storage-blob-input.md) mogą być udostępniane do jednostki opartej na klasie.

```csharp
public class BlobBackedEntity
{
    [JsonIgnore]
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

Aby uzyskać więcej informacji na temat powiązań w usłudze Azure Functions, zobacz dokumentację [wyzwalaczy i powiązań funkcji platformy Azure.](../functions-triggers-bindings.md)

### <a name="dependency-injection-in-entity-classes"></a>Iniekcja zależności w klasach jednostek

Klasy jednostek obsługują [intryb zależności usług Azure Functions](../functions-dotnet-dependency-injection.md). W poniższym przykładzie pokazano, jak zarejestrować `IHttpClientFactory` usługę w jednostce opartej na klasach.

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

Poniższy fragment kodu pokazuje, jak włączyć wstrzykniętą usługę do klasy jednostki.

```csharp
public class HttpEntity
{
    [JsonIgnore]
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

    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> Aby uniknąć problemów z serializacji, upewnij się, aby wykluczyć pola przeznaczone do przechowywania wstrzykniętych wartości z serializacji.

> [!NOTE]
> W przeciwieństwie do korzystania z iniekcji konstruktora w regularnych *must* .NET `static`Usługi platformy Azure, metody punktu wejścia funkcji dla jednostek opartych na klasach musi być zadeklarowany. Deklarowanie punktu wejścia funkcji niestatycznych może spowodować konflikty między normalnym inicjatorem obiektu usługi Azure Functions a inicjatorem obiektu trwałe jednostki.

## <a name="function-based-syntax"></a>Składnia oparta na funkcjach

Do tej pory skupiliśmy się na składni opartej na klasach, ponieważ oczekujemy, że będzie ona lepiej dostosowana do większości aplikacji. Jednak składnia oparta na funkcjach może być odpowiednia dla aplikacji, które chcą zdefiniować lub zarządzać własnymi abstrakcjami dla stanu jednostki i operacji. Ponadto może być właściwe podczas implementowania bibliotek, które wymagają ogólnegou nie jest obecnie obsługiwany przez składnię opartą na klasach. 

W składni opartej na funkcjach funkcja jednostki jawnie obsługuje wysyłkę operacji i jawnie zarządza stanem jednostki. Na przykład poniższy kod przedstawia *Counter* jednostki zaimplementowane przy użyciu składni opartej na funkcji.  

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
        case "delete":
            ctx.DeleteState();
            break;
    }
}
```

### <a name="the-entity-context-object"></a>Obiekt kontekstu jednostki

Dostęp do funkcji specyficznych dla jednostki można `IDurableEntityContext`uzyskać za pośrednictwem obiektu kontekstowego typu . Ten obiekt kontekstu jest dostępny jako parametr funkcji jednostki i `Entity.Current`za pośrednictwem właściwości asynchronizowal-lokalna.

Następujące elementy członkowskie zawierają informacje o bieżącej operacji i umożliwiają nam określenie wartości zwracanej. 

* `EntityName`: nazwa aktualnie wykonywanej jednostki.
* `EntityKey`: klucz aktualnie wykonywanej jednostki.
* `EntityId`: identyfikator aktualnie wykonywanej jednostki (zawiera nazwę i klucz).
* `OperationName`: nazwa bieżącej operacji.
* `GetInput<TInput>()`: pobiera dane wejściowe dla bieżącej operacji.
* `Return(arg)`: zwraca wartość do aranżacji, która wywoływała operację.

Następujący członkowie zarządzają stanem jednostki (tworzenie, odczytywanie, aktualizowanie, usuwanie). 

* `HasState`: czy jednostka istnieje, to znaczy, ma jakiś stan. 
* `GetState<TState>()`: pobiera bieżący stan jednostki. Jeśli jeszcze nie istnieje, zostanie utworzony.
* `SetState(arg)`: tworzy lub aktualizuje stan jednostki.
* `DeleteState()`: usuwa stan jednostki, jeśli istnieje. 

Jeśli stan zwracany przez `GetState` jest obiektem, może być bezpośrednio modyfikowany przez kod aplikacji. Nie ma potrzeby, `SetState` aby zadzwonić ponownie na końcu (ale także bez szkody). Jeśli `GetState<TState>` jest wywoływana wiele razy, ten sam typ musi być używany.

Na koniec następujące elementy członkowskie są używane do sygnalizowania innych jednostek lub uruchamiania nowych aranżacji:

* `SignalEntity(EntityId, operation, input)`: wysyła wiadomość jednokierunkową do jednostki.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: rozpoczyna nową aranżację.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o pojęciach dotyczących encji](durable-functions-entities.md)
