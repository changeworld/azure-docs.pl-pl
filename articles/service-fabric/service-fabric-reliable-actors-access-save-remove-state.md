---
title: Zarządzanie stanem usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uzyskać dostęp, zapisywania i usuwania stanu elementów Reliable Actors usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 7c10d00916ef65767c98616c7337bfa444c339a9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60725401"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Dostęp, zapisywania i usuwania stanu elementów Reliable Actors
[Elementy Reliable Actors](service-fabric-reliable-actors-introduction.md) jednowątkowych obiektów można hermetyzować logiki i stanu i niezawodnie zarządzania stanem. Każde wystąpienie aktora ma swój własny [menedżera stanu](service-fabric-reliable-actors-state-management.md): struktura danych podobne do słownika, która w niezawodny sposób przechowuje par klucz/wartość. Menedżer stanu jest otokę dostawca stanu. Służy do przechowywania danych, niezależnie od tego, który [ustawienie trwałości](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) jest używany.

Menedżer stanu klucze muszą być ciągami. Wartości są ogólne i mogą być dowolnego typu, łącznie z typami niestandardowymi. Wartości przechowywane w Menedżer stanu musi być możliwy do serializacji kontrakt danych, ponieważ one mogą być przekazywane za pośrednictwem sieci do innych węzłów podczas replikacji i mogą być zapisywane na dysku, w zależności od ustawienia trwałość stanu aktora.

Menedżer stanu przedstawia typowe metody słownika Zarządzanie stanem, podobne do tych w niezawodnym słowniku.

Aby uzyskać informacje, zobacz [najlepsze rozwiązania w zakresie zarządzania stanu aktora](service-fabric-reliable-actors-state-management.md#best-practices).

## <a name="access-state"></a>Stan dostępu
Stan odbywa się za pośrednictwem Menedżera stanu według klucza. Metody menedżera stanu są wszystkie asynchronicznego, ponieważ może wymagać We/Wy dysku, gdy aktorów utrwalonych stanu. Po pierwszym dostępu obiekty stanu są buforowane w pamięci. Powtórz uzyskiwanie dostępu do operacji dostępu do obiektów bezpośrednio z pamięci i zwraca synchronicznie, bez ponoszenia We/Wy dysku lub asynchroniczne przełączania kontekstu. Obiekt stanu zostanie usunięty z pamięci podręcznej w następujących przypadkach:

* Metoda aktora zwraca nieobsługiwany wyjątek po pobiera obiekt z przez menedżera stanu.
* Aktor ponownej po dezaktywowane lub po awarii.
* Dostawca stanu strony stanu na dysku. To zachowanie zależy od implementacji dostawcy stanu. Dostawca stanu domyślnego dla `Persisted` ustawienia jest to zachowanie.

Można pobrać stanu przy użyciu standardowego *uzyskać* operacji, która zgłasza `KeyNotFoundException`(C#) lub `NoSuchElementException`(Java), jeśli wpis nie istnieje dla klucza:

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

Stan możesz również pobrać za pomocą *TryGet* metodę, która nie zgłasza, jeśli wpis nie istnieje dla klucza:

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
Metody pobierania menedżera stanu zwraca odwołanie do obiektu w pamięci lokalnej. Modyfikowanie tego obiektu w pamięci lokalnej samodzielnie nie powoduje zapisania trwale jej. Gdy obiekt jest pobierane z Menedżera stanu i modyfikować, należy ponownie wstawić do menedżera stanu można trwale zapisać.

Możesz wstawić stanu przy użyciu bezwarunkowe *ustaw*, który jest odpowiednikiem `dictionary["key"] = value` składni:

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

Stan można dodać za pomocą *Dodaj* metody. Ta metoda wyrzuca `InvalidOperationException`(C#) lub `IllegalStateException`(Java), gdy próbuje dodać klucz już istnieje.

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

Można również dodać stan za pomocą *TryAdd* metody. Ta metoda nie zgłaszają, gdy próbuje dodać klucz, który już istnieje.

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

Na końcu metody aktora Menedżer stanu automatycznie zapisuje wszelkie wartości, które zostały dodane lub zmodyfikowane przez operację wstawiania lub aktualizacji. "Zapisz" może zawierać przechowywanie na dysku i replikacji, w zależności od ustawienia używane. Wartości, które nie zostały zmodyfikowane nie są utrwalone lub replikowane. Jeśli żadne wartości nie zostały zmodyfikowane, Zapisz operacji nic nie robi. Jeśli trwa zapisywanie kończy się niepowodzeniem, zostanie odrzucony stanu modyfikacji i załadowaniu pierwotnego stanu.

Można również zapisać stanu ręcznie przez wywołanie metody `SaveStateAsync` metody na podstawie aktora:

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
Możesz usunąć stan trwale z Menedżera stanu aktora, wywołując *Usuń* metody. Ta metoda wyrzuca `KeyNotFoundException`(C#) lub `NoSuchElementException`(Java), podczas próby usunięcia klucza, który nie istnieje.

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

Można również usunąć stan trwale przy użyciu *TryRemove* metody. Ta metoda nie zgłasza podczas próby usunięcia klucza, który nie istnieje.

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

## <a name="next-steps"></a>Kolejne kroki

Stan, który jest przechowywany w elementach Reliable Actors, trzeba go serializować przed zapisane na dysku i replikowane w celu zapewnienia wysokiej dostępności. Dowiedz się więcej o [serializacja typów aktora](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Następnie Dowiedz się więcej o [aktora Diagnostyka i monitorowanie wydajności](service-fabric-reliable-actors-diagnostics.md).
