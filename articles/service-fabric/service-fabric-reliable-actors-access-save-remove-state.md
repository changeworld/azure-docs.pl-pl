---
title: Zarządzanie stanem sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uzyskać dostęp, zapisywania i usuwania usługi sieć szkieletowa Reliable Actors stanu.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: ac3afe144b9cf9e2fb307087edb175a603ffe4e9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206751"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Dostęp, zapisywania i usuwania stanu Reliable Actors
[Reliable Actors](service-fabric-reliable-actors-introduction.md) jednowątkowych obiektów, które hermetyzują zarówno logiki i stan i niezawodny sposób zarządzania stanem. Każde wystąpienie aktora ma własną [Menedżer stanu](service-fabric-reliable-actors-state-management.md): struktura typu słownika danych, która w niezawodny sposób przechowuje par klucz/wartość. Menedżer stanów jest otokę dostawcy stanu. Służy do przechowywania danych niezależnie od tego, który [ustawienie trwałości](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) jest używany.

Menedżer stanu klucze muszą być ciągami. Wartości są ogólne i mogą być dowolnego typu, łącznie z niestandardowych typów. Wartości przechowywane w Menedżerze stanu musi być kontraktu danych, które można serializować, ponieważ mogą być przesyłane przez sieć do innych węzłów podczas replikacji i mogą być zapisywane na dysku, w zależności od ustawienia trwałości stanu aktora.

Menedżer stanu przedstawia stan podobne do tych znaleziony w słowniku niezawodnej zarządzania typowe metody słownika.

Aby uzyskać informacje, zobacz [najlepsze rozwiązania w zakresie zarządzania stanu aktora](service-fabric-reliable-actors-state-management.md#best-practices).

## <a name="access-state"></a>Stan dostępu
Stan jest dostępny za pośrednictwem Menedżera stanu po klucz. Metody menedżera stanu są wszystkie asynchronicznego, ponieważ może wymagać We/Wy dysku, gdy podmiotów utrwalonych stanu. Po pierwszym dostępie obiekty stanu są buforowane w pamięci. Powtórz uzyskiwanie dostępu do operacji dostępu do obiektów bezpośrednio z pamięci i zwracany synchronicznie, bez ponoszenia We/Wy dysku lub asynchroniczne przełączania kontekstu. Obiekt stanu zostanie usunięty z pamięci podręcznej w następujących przypadkach:

* Metoda aktora zgłasza nieobsługiwany wyjątek, po jego pobiera obiekt przez menedżera stanu.
* Uaktywnieniu aktora, po dezaktywowany lub po awarii.
* Dostawca stanu strony stanu na dysku. To zachowanie zależy od implementacji dostawcy stanu. Domyślny dostawca stanu dla `Persisted` ma to zachowanie.

Stan można pobrać za pomocą standardowego *uzyskać* operacja, która zgłasza `KeyNotFoundException`(C#) lub `NoSuchElementException`(Java), jeśli wpis nie istnieje dla klucza:

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

Stan można również pobrać za pomocą *TryGet* metodę, która nie zgłasza, jeśli wpis nie istnieje dla klucza:

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
Metody pobierania stanu Menedżera zwraca odwołanie do obiektu w pamięci lokalnej. Modyfikowanie tego obiektu w pamięci lokalnej wyłącznie nie powoduje jego zapisania trwałym. Gdy obiekt jest pobierane z Menedżera stanu i modyfikować, należy ponownie wstawić do przez menedżera stanu można zapisać trwałym.

Stan można wstawić za pomocą bezwarunkowe *ustawić*, który jest odpowiednikiem `dictionary["key"] = value` składni:

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

Stan można dodać za pomocą *Dodaj* metody. Ta metoda zgłasza `InvalidOperationException`(C#) lub `IllegalStateException`(Java) próbuje dodać klucz już istnieje.

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

Stan można również dodać za pomocą *TryAdd* metody. Ta metoda nie zgłasza, gdy próbuje dodać klucz już istnieje.

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

Na końcu metody aktora Menedżer stanu automatycznie zapisuje wartości, które zostały dodane lub zmodyfikowane przez operacji insert lub update. "Zapisz" może zawierać przechowywanie na dysku i replikacji, w zależności od ustawienia używane. Wartości, które nie zostały zmodyfikowane nie są zachowywane lub replikowane. Jeśli wartości nie zostały zmodyfikowane, Zapisz działania nie działają. Jeśli zapiszesz kończy się niepowodzeniem, zostanie odrzucony stanu modyfikacji i załadowaniu oryginalnego stanu.

Można także zapisać stan ręcznie przez wywołanie metody `SaveStateAsync` metody na podstawie aktora:

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

## <a name="remove-state"></a>Usuwanie stanu
Możesz usunąć stan trwale z Menedżer stanu aktora wywołując *Usuń* metody. Ta metoda zgłasza `KeyNotFoundException`(C#) lub `NoSuchElementException`(Java) podczas próby usunięcia klucz, który nie istnieje.

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

Stan, który jest przechowywany w Reliable Actors musi być serializowany przed zapisany na dysku i replikowany wysokiej dostępności. Dowiedz się więcej o [serializacji typu aktora](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Następnie Dowiedz się więcej o [aktora Diagnostyka i monitorowanie wydajności](service-fabric-reliable-actors-diagnostics.md).
