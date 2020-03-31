---
title: Zarządzanie stanem sieci szkieletowej usługi Azure
description: Dowiedz się więcej o uzyskiwaniu dostępu, zapisywaniu i usuwaniu stanu dla niezawodnego aktora sieci szkieletowej usług Azure oraz zagadnienia dotyczące projektowania aplikacji.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 788c337a37ec66c5aa1521c5cd9f2816ed7a8bf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645637"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Dostęp, zapisywanie i usuwanie stanu Niezawodni aktorzy
[Reliable Actors](service-fabric-reliable-actors-introduction.md) są jednowątkowe obiekty, które można hermetyzować zarówno logiki i stanu i niezawodnie zachować stan. Każde wystąpienie aktora ma swój własny [menedżer stanu:](service-fabric-reliable-actors-state-management.md)struktura danych podobną do słownika, która niezawodnie przechowuje pary klucz/wartość. Menedżer stanu jest otoki wokół dostawcy stanu. Można go używać do przechowywania danych, niezależnie od tego, które [ustawienie trwałości](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) jest używane.

Klucze menedżera stanu muszą być ciągami. Wartości są ogólne i mogą być dowolnego typu, w tym typów niestandardowych. Wartości przechowywane w Menedżerze stanu muszą być serializowalne dla kontraktu danych, ponieważ mogą być przesyłane przez sieć do innych węzłów podczas replikacji i mogą być zapisywane na dysku, w zależności od ustawienia trwałości stanu aktora.

Menedżer stanu udostępnia typowe metody słownika do zarządzania stanem, podobne do tych znalezionych w reliable dictionary.

Aby uzyskać więcej informacji, zobacz [najważniejsze wskazówki dotyczące zarządzania stanem aktora](service-fabric-reliable-actors-state-management.md#best-practices).

## <a name="access-state"></a>Stan dostępu
Stan jest dostępny za pośrednictwem menedżera stanu za pomocą klucza. Metody menedżera stanu są asynchroniczne, ponieważ mogą wymagać we/wy dysku, gdy aktorzy utrwaliły się w stanie. Przy pierwszym dostępie obiekty stanu są buforowane w pamięci. Operacje powtarzania dostępu uzyskują dostęp do obiektów bezpośrednio z pamięci i zwracają synchronicznie bez ponoszenia narzutów we/wy dysku lub asynchronicznie przełączających kontekst. Obiekt stanu jest usuwany z pamięci podręcznej w następujących przypadkach:

* Metoda aktora zgłasza nieobsługiowany wyjątek po pobraniu obiektu z menedżera stanu.
* Aktor jest reaktywowany po dezaktywacji lub po awarii.
* Serwer stanu podaje stan na dysku. To zachowanie zależy od implementacji dostawcy stanu. Domyślny dostawca stanu `Persisted` dla tego ustawienia ma to zachowanie.

Można pobrać stan przy użyciu standardowej operacji `KeyNotFoundException` *Pobierz,* która `NoSuchElementException`zgłasza (C#) lub (Java), jeśli wpis nie istnieje dla klucza:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

Można również pobrać stan przy użyciu *TryGet* metody, która nie zgłasza, jeśli wpis nie istnieje dla klucza:

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

## <a name="save-state"></a>Zapisz stan
Metody pobierania menedżera stanu zwracają odwołanie do obiektu w pamięci lokalnej. Modyfikowanie tego obiektu w pamięci lokalnej sam nie powoduje, że mają być zapisywane trwale. Gdy obiekt jest pobierany z menedżera stanu i modyfikowany, musi zostać ponownie wstawiony do menedżera stanu, aby zapisać trwale.

Stan można wstawić przy użyciu bezwarunkowego *zestawu*, `dictionary["key"] = value` który jest odpowiednikiem składni:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

Stan można dodać za pomocą *Add* metody. Ta metoda `InvalidOperationException`zgłasza (C#) lub `IllegalStateException`(Java), gdy próbuje dodać klucz, który już istnieje.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

Można również dodać stan przy użyciu *TryAdd* metody. Ta metoda nie jest zgłaszana, gdy próbuje dodać klucz, który już istnieje.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

Na końcu metody aktora menedżer stanu automatycznie zapisuje wszystkie wartości, które zostały dodane lub zmodyfikowane przez operację wstawiania lub aktualizacji. "Zapisz" może zawierać utrwalanie dysku i replikacji, w zależności od użytych ustawień. Wartości, które nie zostały zmodyfikowane, nie są zachowywane ani replikowane. Jeśli żadne wartości nie zostały zmodyfikowane, operacja zapisywania nic nie robi. Jeśli zapisywanie nie powiedzie się, zmodyfikowany stan zostanie odrzucony, a oryginalny stan zostanie ponownie załadowany.

Stan można również zapisać ręcznie, `SaveStateAsync` wywołując metodę na podstawie aktora:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

## <a name="remove-state"></a>Usuń stan
Stan można trwale usunąć z menedżera stanu aktora, wywołując *Remove* metody. Ta metoda `KeyNotFoundException`zgłasza (C#) lub `NoSuchElementException`(Java), gdy próbuje usunąć klucz, który nie istnieje.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

Stan można również usunąć trwale przy użyciu *TryRemove* metody. Ta metoda nie jest zgłaszana, gdy próbuje usunąć klucz, który nie istnieje.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="next-steps"></a>Następne kroki

Stan, który jest przechowywany w reliable actors musi być serializowany przed jego zapisane na dysku i replikowane dla wysokiej dostępności. Dowiedz się więcej [o serializacji typu aktora](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Następnie dowiedz się więcej o [diagnostyce aktora i monitorowaniu wydajności](service-fabric-reliable-actors-diagnostics.md).
