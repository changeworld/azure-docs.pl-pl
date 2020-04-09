---
title: Trwałe jednostki — funkcje platformy Azure
description: Dowiedz się, czym są trwałe jednostki i jak ich używać w rozszerzeniu Funkcje trwałe dla usług Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 4f45ac40e7df865bdb4722d086325096c377cd59
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877546"
---
# <a name="entity-functions"></a>Funkcje encji

Funkcje encji definiują operacje odczytywania i aktualizowania małych fragmentów stanu, nazywanych *trwałymi elementami*. Podobnie jak funkcje orkiestratora, funkcje encji są funkcjami o specjalnym typie wyzwalacza, *wyzwalaczem jednostki*. W przeciwieństwie do funkcji koordynatora funkcje jednostki zarządzać stan jednostki jawnie, a nie niejawnie reprezentujących stan za pośrednictwem przepływu sterowania.
Jednostki zapewniają środki skalowania w poziomie aplikacji przez dystrybucję pracy między wieloma jednostkami, z których każda ma stan o niewielkich rozmiarach.

> [!NOTE]
> Funkcje jednostki i związane z nimi funkcje są dostępne tylko w funkcji trwałe 2.0 i powyżej.

## <a name="general-concepts"></a>Pojęcia ogólne

Jednostki zachowują się trochę jak małe usługi, które komunikują się za pośrednictwem wiadomości. Każda jednostka ma unikatową tożsamość i stan wewnętrzny (jeśli istnieje). Podobnie jak usługi lub obiekty, jednostki wykonują operacje po wyświetleniu monitu. Podczas wykonywania operacji może zaktualizować stan wewnętrzny jednostki. Może również wywołać usługi zewnętrzne i czekać na odpowiedź. Jednostki komunikują się z innymi jednostkami, aranżacjami i klientami przy użyciu wiadomości, które są niejawnie wysyłane za pośrednictwem niezawodnych kolejek. 

Aby zapobiec konfliktom, wszystkie operacje na jednej jednostce są gwarantowane do wykonywania szeregowo, to znaczy jeden po drugim. 

### <a name="entity-id"></a>Identyfikator jednostki
Jednostki są dostępne za pośrednictwem unikatowego identyfikatora, *identyfikatora jednostki*. Identyfikator jednostki jest po prostu parą ciągów, która jednoznacznie identyfikuje wystąpienie jednostki. Składa się z:

* **Nazwa jednostki**, która jest nazwą identyfikującą typ jednostki. Przykładem jest "Licznik". Ta nazwa musi być zgodna z nazwą funkcji jednostki, która implementuje jednostkę. Nie jest wrażliwy na wielkość liter.
* **Klucz jednostki**, który jest ciągiem, który jednoznacznie identyfikuje jednostkę wśród wszystkich innych jednostek o tej samej nazwie. Przykładem jest identyfikator GUID.

Na przykład `Counter` funkcja jednostki może służyć do przechowywania wyników w grze online. Każde wystąpienie gry ma unikatowy identyfikator `@Counter@Game1` jednostki, taki jak i `@Counter@Game2`. Wszystkie operacje przeznaczone dla określonej jednostki wymagają określenia identyfikatora jednostki jako parametru.

### <a name="entity-operations"></a>Operacje na jednostkach ###

Aby wywołać operację na jednostce, należy określić:

* **Identyfikator jednostki** jednostki docelowej.
* **Nazwa operacji**, która jest ciągiem określającym operację do wykonania. Na przykład `Counter` jednostka może `add` `get`obsługiwać `reset` , lub operacji.
* **Wejście operacji**, które jest opcjonalnym parametrem wejściowym dla operacji. Na przykład operacja dodawania może przyjmować kwotę całkowitą jako dane wejściowe.
* **Zaplanowany czas**, który jest opcjonalnym parametrem określającym czas dostawy operacji. Na przykład operacja może być niezawodnie zaplanowane do uruchomienia kilka dni w przyszłości.

Operacje mogą zwracać wartość wyniku lub wynik błędu, taki jak błąd JavaScript lub wyjątek .NET. Ten wynik lub błąd można zaobserwować przez aranżacji, które nazywane operacji.

Operacja jednostki może również tworzyć, odczytywać, aktualizować i usuwać stan jednostki. Stan jednostki jest zawsze trwale utrwalone w magazynie.

## <a name="define-entities"></a>Definiowanie elementów

Obecnie dwa różne interfejsy API do definiowania jednostek to:

**Składnia oparta na funkcjach**, gdzie jednostki są reprezentowane jako funkcje, a operacje są jawnie wywoływane przez aplikację. Ta składnia działa dobrze dla jednostek o stanie prostym, kilku operacjach lub dynamicznym zestawie operacji, takich jak w ramach aplikacji. Ta składnia może być uciążliwe do utrzymania, ponieważ nie przechwytuje błędów typu w czasie kompilacji.

**Składnia oparta na klasach (tylko.NET)**, gdzie jednostki i operacje są reprezentowane przez klasy i metody. Ta składnia tworzy łatwiej czytelny kod i umożliwia wywoływanie operacji w sposób bezpieczny dla typu. Składnia oparta na klasach jest cienką warstwą na wierzchu składni opartej na funkcjach, więc oba warianty mogą być używane zamiennie w tej samej aplikacji.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="example-function-based-syntax---c"></a>Przykład: Składnia oparta na funkcjach — C #

Poniższy kod jest przykładem `Counter` prostej jednostki zaimplementowane jako funkcja trwałe. Ta funkcja definiuje trzy `add` `reset`operacje, `get`i , z których każda działa w stanie liczby całkowitej.

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
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

Aby uzyskać więcej informacji na temat składni opartej na funkcjach i sposobu jej używania, zobacz [Składnia oparta na funkcjach](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="example-class-based-syntax---c"></a>Przykład: składnia oparta na klasach — C #

Poniższy przykład jest równoważne `Counter` implementacji jednostki przy użyciu klas i metod.

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

Stan tej encji jest obiektem typu `Counter`, który zawiera pole, które przechowuje bieżącą wartość licznika. Aby utrwalić ten obiekt w magazynie, jest serializowany i deserializowany przez [bibliotekę Json.NET.](https://www.newtonsoft.com/json) 

Aby uzyskać więcej informacji na temat składni opartej na klasach i sposobu jej używania, zobacz [Definiowanie klas jednostek](durable-functions-dotnet-entities.md#defining-entity-classes).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="example-javascript-entity"></a>Przykład: Encja JavaScript

Trwałe jednostki są dostępne w języku JavaScript, począwszy od `durable-functions` wersji **1.3.0** pakietu npm. Poniższy kod `Counter` jest jednostką zaimplementowana jako trwała funkcja zapisana w języku JavaScript.

**Licznik/funkcja.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**Licznik/index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

---

## <a name="access-entities"></a>Dostęp do encji

Jednostki są dostępne za pomocą komunikacji jednokierunkowej lub dwukierunkowej. Następujące terminologie rozróżnia dwie formy komunikacji: 

* **Wywołanie** jednostki korzysta z komunikacji dwukierunkowej (w obie strony). Przed kontynuowaniem należy wysłać komunikat operacji do encji, a następnie poczekać na komunikat odpowiedzi. Komunikat odpowiedzi może dostarczyć wartość wyniku lub wynik błędu, taki jak błąd JavaScript lub wyjątek .NET. Ten wynik lub błąd jest następnie obserwowany przez wywołującego.
* **Sygnalizacja** jednostki używa komunikacji jednokierunkowej (ognia i zapomniju). Wysyłasz komunikat operacji, ale nie czekaj na odpowiedź. Chociaż wiadomość jest gwarantowana do dostarczenia po pewnym czasie, nadawca nie wie, kiedy i nie może obserwować żadnej wartości wyniku lub błędów.

Jednostki są dostępne z poziomu funkcji klienta, z funkcji orkiestratora lub z funkcji jednostki. Nie wszystkie formy komunikacji są obsługiwane przez wszystkie konteksty:

* Z poziomu klientów można sygnalizować jednostki i odczytywać stan jednostki.
* Z poziomu aranżacji można sygnalizować jednostki i wywoływać jednostki.
* Z poziomu encji można sygnalizować jednostki.

Poniższe przykłady ilustrują te różne sposoby uzyskiwania dostępu do jednostek.

### <a name="example-client-signals-an-entity"></a>Przykład: Klient sygnalizuje jednostkę

Aby uzyskać dostęp do jednostek ze zwykłej funkcji platformy Azure, która jest również nazywana funkcją klienta, należy użyć [powiązania klienta jednostki.](durable-functions-bindings.md#entity-client) W poniższym przykładzie przedstawiono funkcję wyzwalaną kolejką sygnalizującą jednostkę używającą tego powiązania.

# <a name="c"></a>[C#](#tab/csharp)

> [!NOTE]
> Dla uproszczenia poniższe przykłady pokazują luźno wpisaną składnię uzyskiwania dostępu do jednostek. Ogólnie rzecz biorąc zaleca się [dostęp do jednostek za pośrednictwem interfejsów,](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) ponieważ zapewnia więcej sprawdzania typów.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

---

*Sygnał* terminu oznacza, że wywołanie interfejsu API jednostki jest jednokierunkowe i asynchroniczne. Nie jest możliwe dla funkcji klienta wiedzieć, kiedy jednostka przetworzyła operację. Ponadto funkcja klienta nie można zaobserwować żadnych wartości wynik lub wyjątków. 

### <a name="example-client-reads-an-entity-state"></a>Przykład: Klient odczytuje stan jednostki

Funkcje klienta mogą również badać stan jednostki, jak pokazano w poniższym przykładzie:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    EntityStateResponse<JObject> stateResponse = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, stateResponse.EntityState);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    const stateResponse = await client.readEntityState(entityId);
    return stateResponse.entityState;
};
```

---

Kwerendy o stan jednostki są wysyłane do magazynu śledzenia trwałe i zwracają ostatnio utrwalony stan jednostki. Ten stan jest zawsze "popełnione" stan, to znaczy, że nigdy nie jest tymczasowym stanem pośrednim zakładanym w trakcie wykonywania operacji. Jednak jest możliwe, że ten stan jest przestarzały w porównaniu do stanu w pamięci jednostki. Tylko aranżacji można odczytać stan jednostki w pamięci, zgodnie z opisem w poniższej sekcji.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Przykład: aranżacja sygnalizuje i wywołuje encję

Funkcje aranżatora mogą uzyskiwać dostęp do jednostek przy użyciu interfejsów API w [powiązaniu wyzwalacza aranżacji.](durable-functions-bindings.md#orchestration-trigger) Poniższy przykładowy kod pokazuje funkcję koordynatora `Counter` wywołującą i sygnalizującą jednostkę.

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
});
```

> [!NOTE]
> JavaScript obecnie nie obsługuje sygnalizacji jednostki z orchestrator. Zamiast tego użyj polecenia cmdlet `callEntity`.

---

Tylko aranżacji są w stanie wywołania jednostek i uzyskiwania odpowiedzi, która może być wartością zwracaną lub wyjątek. Funkcje klienta, które używają [powiązania klienta](durable-functions-bindings.md#entity-client) może tylko sygnał jednostek.

> [!NOTE]
> Wywoływanie jednostki z funkcji koordynatora jest podobne do wywoływania [funkcji działania](durable-functions-types-features-overview.md#activity-functions) z funkcji koordynatora. Główną różnicą jest to, że funkcje jednostki są trwałe obiekty z adresem, który jest identyfikatorem jednostki. Funkcje encji obsługują określanie nazwy operacji. Funkcje działania, z drugiej strony, są bezstanowe i nie mają pojęcia operacji.

### <a name="example-entity-signals-an-entity"></a>Przykład: Jednostka sygnalizuje encję

Funkcja jednostki może wysyłać sygnały do innych jednostek, a nawet do siebie, podczas wykonywania operacji.
Na przykład możemy zmodyfikować `Counter` przykład poprzedniej jednostki, tak aby wysyłał sygnał "osiągnięty punkt kontrolny" do jakiejś jednostki monitora, gdy licznik osiągnie wartość 100.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

---

## <a name="entity-coordination-currently-net-only"></a><a name="entity-coordination"></a>Koordynacja jednostek (obecnie tylko .NET)

Może się okazać, że trzeba koordynować operacje w wielu jednostkach. Na przykład w aplikacji bankowej mogą być jednostki reprezentujące poszczególne konta bankowe. Podczas przelewu środków z jednego konta na drugie należy upewnić się, że konto źródłowe ma wystarczające środki. Należy również upewnić się, że aktualizacje kont źródłowych i docelowych są wykonywane w sposób spójny pod względem transakcyjnym.

### <a name="example-transfer-funds-c"></a>Przykład: Środki transferowe (C#)

Poniższy przykładowy kod przenosi środki między dwoma encjami konta przy użyciu funkcji koordynatora. Koordynowanie aktualizacji encji `LockAsync` wymaga użycia metody do utworzenia _sekcji krytycznej_ w aranżacji.

> [!NOTE]
> Dla uproszczenia w tym `Counter` przykładzie ponownie używa jednostki zdefiniowane wcześniej. W rzeczywistej aplikacji lepiej byłoby zdefiniować `BankAccount` bardziej szczegółową jednostkę.

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

W .NET `LockAsync` `IDisposable`zwraca program , który kończy sekcję krytyczną po usunięciu. Ten `IDisposable` wynik może służyć `using` razem z blokiem, aby uzyskać reprezentację składni sekcji krytycznej.

W poprzednim przykładzie funkcja koordynatora przeniosła środki z jednostki źródłowej do jednostki docelowej. Metoda `LockAsync` zablokowała zarówno encje konta źródłowego, jak i docelowego. To blokowanie zapewniło, że żaden inny klient nie może wysyłać kwerend ani modyfikować stanu albo `using` konta, dopóki logika aranżacji nie zakończy sekcji krytycznej na końcu instrukcji. To zachowanie zapobiega możliwości przesiewania w rachunku bieżącym z konta źródłowego.

> [!NOTE] 
> Po zakończeniu aranżacji, normalnie lub z błędem, wszystkie krytyczne sekcje w toku są niejawnie zakończone i wszystkie blokady są zwalniane.

### <a name="critical-section-behavior"></a>Krytyczne zachowanie sekcji

Metoda `LockAsync` tworzy sekcję krytyczną w aranżacji. Te sekcje krytyczne uniemożliwiają innym aranżacji wprowadzanie nakładających się zmian do określonego zestawu jednostek. Wewnętrznie `LockAsync` interfejs API wysyła operacje "lock" do jednostek i zwraca po otrzymaniu komunikatu odpowiedzi "lock acquired" z każdej z tych samych jednostek. Blokada i odblokowanie są wbudowane operacje obsługiwane przez wszystkie jednostki.

Żadne operacje od innych klientów są dozwolone na jednostki, gdy jest w stanie zablokowanym. To zachowanie zapewnia, że tylko jedno wystąpienie aranżacji można zablokować jednostki w czasie. Jeśli obiekt wywołujący próbuje wywołać operację na jednostce, gdy jest zablokowana przez aranżację, ta operacja jest umieszczana w kolejce operacji oczekującej. Żadne oczekujące operacje są przetwarzane, dopóki po aranżacji gospodarstwa zwalnia blokadę.

> [!NOTE] 
> To zachowanie różni się nieco od ćwierczy synchronizacji używanych w większości języków programowania, takich jak instrukcja `lock` w języku C#. Na przykład w języku `lock` C#instrukcja musi być używana przez wszystkie wątki, aby zapewnić prawidłową synchronizację między wieloma wątkami. Jednostki, jednak nie wymagają wszystkich wywołujących jawnie zablokować jednostki. Jeśli którykolwiek obiekt wywołujący blokuje jednostkę, wszystkie inne operacje na tej jednostce są blokowane i umieszczane w kolejce za tą blokadą.

Blokady jednostek są trwałe, więc utrzymują się nawet wtedy, gdy proces wykonywania jest odtwoniany. Blokady są wewnętrznie utrwalone jako część trwałego stanu jednostki.

W przeciwieństwie do transakcji sekcje krytyczne nie są automatycznie wycofywane w przypadku błędów. Zamiast tego wszelkie obsługi błędów, takich jak wycofywanie lub ponowienie próby, muszą być jawnie kodowane, na przykład przez przechwytywanie błędów lub wyjątków. Ten wybór projektu jest zamierzony. Automatyczne wycofywanie wszystkich efektów aranżacji jest trudne lub niemożliwe w ogóle, ponieważ aranżacji może uruchamiać działania i nawiązywać wywołania do usług zewnętrznych, które nie mogą być wycofane. Ponadto próby wycofania się może się nie powieść i wymagają dalszej obsługi błędów.

### <a name="critical-section-rules"></a>Reguły sekcji krytycznej

W przeciwieństwie do niskopoziomowych ćwiercze blokowania w większości języków programowania, sekcje krytyczne *są gwarantowane, aby nie zakleszczenia*. Aby zapobiec zakleszczenia, wymuszamy następujące ograniczenia: 

* Nie można zagnieżdżać sekcji krytycznych.
* Sekcje krytyczne nie mogą tworzyć podnajmowania.
* Sekcje krytyczne mogą wywoływać tylko jednostki, które zostały zablokowane.
* Sekcje krytyczne nie można wywołać tej samej jednostki przy użyciu wielu wywołań równoległych.
* Sekcje krytyczne mogą sygnalizować tylko jednostki, które nie zostały zablokowane.

Wszelkie naruszenia tych reguł powodują błąd środowiska `LockingRulesViolationException` uruchomieniowego, takich jak w .NET, który zawiera komunikat, który wyjaśnia, jaka reguła została złamana.

## <a name="comparison-with-virtual-actors"></a>Porównanie z wirtualnymi aktorami

Wiele trwałych cech elementów jest inspirowanych [modelem aktora.](https://en.wikipedia.org/wiki/Actor_model) Jeśli znasz już aktorów, możesz rozpoznać wiele pojęć opisanych w tym artykule. Trwałe jednostki są szczególnie podobne do [wirtualnych podmiotów](https://research.microsoft.com/projects/orleans/)lub ziaren, spopularyzowanych w [projekcie Orleans.](http://dotnet.github.io/orleans/) Przykład:

* Trwałe jednostki są adresowalne za pośrednictwem identyfikatora jednostki.
* Operacje trwałego jednostki są wykonywane szeregowo, po jednym na raz, aby zapobiec warunkom wyścigu.
* Trwałe jednostki są tworzone niejawnie, gdy są one wywoływane lub sygnalizowane.
* Gdy nie wykonuje operacji, trwałe jednostki są dyskretnie zwalniane z pamięci.

Istnieje kilka ważnych różnic, które warto zauważyć:

* Trwałe elementy priorytet trwałość nad opóźnieniem, a więc może nie być odpowiednie dla aplikacji z wymaganiami o ściśle opóźnieniach.
* Trwałe jednostki nie mają wbudowanych limitów czasu dla wiadomości. W Orleanie limit czasu wszystkich wiadomości po konfigurowalnym czasie. Wartość domyślna to 30 sekund.
* Wiadomości wysyłane między jednostkami są dostarczane niezawodnie i w kolejności. W Orleanie niezawodne lub uporządkowane dostarczanie jest obsługiwane dla zawartości wysyłanej za pośrednictwem strumieni, ale nie jest gwarantowane dla wszystkich wiadomości między ziarnami.
* Wzorce żądania odpowiedzi w jednostkach są ograniczone do aranżacji. Z poziomu jednostek dozwolone jest tylko jednokierunkowe wiadomości (znane również jako sygnalizacja), jak w oryginalnym modelu aktora i w przeciwieństwie do ziaren w Orleanie. 
* Trwałe elementy nie zakleszczenia. W Orleanie zakleszczenia mogą wystąpić i nie rozwiązać, dopóki nie przesunie się limit czasu wiadomości.
* Trwałe jednostki mogą być używane w połączeniu z trwałymi aranżacjami i obsługują rozproszone mechanizmy blokowania. 


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przeczytaj przewodnik dewelopera dotyczący trwałych jednostek w witrynie .NET](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o centrach zadań](durable-functions-task-hubs.md)
