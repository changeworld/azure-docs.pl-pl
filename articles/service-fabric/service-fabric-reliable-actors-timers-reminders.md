---
title: Niezawodne czasomierze złośliwych użytkowników i przypomnieniami | Dokumentacja firmy Microsoft
description: Wprowadzenie do czasomierze i przypomnienia dla elementów Reliable Actors sieci szkieletowej usług.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 00c48716-569e-4a64-bd6c-25234c85ff4f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: e43aec6630a4a688ffd6c52a5e5bd711243fa662
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206795"
---
# <a name="actor-timers-and-reminders"></a>Czasomierze aktora i przypomnieniami
Złośliwych użytkowników można zaplanować okresowe pracy od samych siebie rejestrując czasomierze lub przypomnienia. W tym artykule przedstawiono sposób użycia czasomierze i przypomnieniami i wyjaśniono różnice między nimi.

## <a name="actor-timers"></a>Czasomierze aktora
Czasomierze aktora zapewniają prosty otokę .NET lub Java czasomierza aby upewnić się, że metody wywołania zwrotnego przestrzegać współbieżności opartej na włączanie gwarantuje, że runtime podmiotów zawiera.

Można użyć złośliwych użytkowników `RegisterTimer`(C#) lub `registerTimer`(Java) i `UnregisterTimer`(C#) lub `unregisterTimer`metod klasy podstawowej umożliwia rejestrowanie i wyrejestrowywanie ich czasomierze (Java). W poniższym przykładzie pokazano sposób użycia czasomierza interfejsów API. Interfejsy API są bardzo podobne do czasomierz .NET lub Java czasomierza. W tym przykładzie, gdy zegar jest wymagany, środowiska uruchomieniowego podmiotów wywoła `MoveObject`(C#) lub `moveObject`— metoda (Java). Metoda jest gwarantowana przestrzegać współbieżności opartej na ruch. Oznacza to, że nie ma innych metod aktora lub wywołania zwrotne czasomierza/monitu będzie w toku zakończenia wykonywania tego wywołania zwrotnego.

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    public VisualObjectActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```
```Java
public class VisualObjectActorImpl extends FabricActor implements VisualObjectActor
{
    private ActorTimer updateTimer;

    public VisualObjectActorImpl(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture onActivateAsync()
    {
        ...

        return this.stateManager()
                .getOrAddStateAsync(
                        stateName,
                        VisualObject.createRandom(
                                this.getId().toString(),
                                new Random(this.getId().toString().hashCode())))
                .thenApply((r) -> {
                    this.registerTimer(
                            (o) -> this.moveObject(o),                        // Callback method
                            "moveObject",
                            null,                                             // Parameter to pass to the callback method
                            Duration.ofMillis(10),                            // Amount of time to delay before the callback is invoked
                            Duration.ofMillis(timerIntervalInMilliSeconds));  // Time interval between invocations of the callback method
                    return null;
                });
    }

    @Override
    protected CompletableFuture onDeactivateAsync()
    {
        if (updateTimer != null)
        {
            unregisterTimer(updateTimer);
        }

        return super.onDeactivateAsync();
    }

    private CompletableFuture moveObject(Object state)
    {
        ...
        return this.stateManager().getStateAsync(this.stateName).thenCompose(v -> {
            VisualObject v1 = (VisualObject)v;
            v1.move();
            return (CompletableFuture<?>)this.stateManager().setStateAsync(stateName, v1).
                    thenApply(r -> {
                      ...
                      return null;});
        });
    }
}
```

Następnym okresie czasomierza rozpoczyna się wykonanie zakończone wywołanie zwrotne. Oznacza to, że zegar jest zatrzymywany podczas wywołania zwrotnego jest wykonywany i została uruchomiona po zakończeniu wywołania zwrotnego.

Środowisko uruchomieniowe podmiotów zapisuje zmiany wprowadzone Menedżer stanu aktora po zakończeniu wywołania zwrotnego. Jeśli wystąpi błąd podczas zapisywania stanu, ten obiekt aktora spowoduje wyłączenie i nowe wystąpienie zostanie aktywowany.

Zatrzymuje wszystkie czasomierze aktora jest dezaktywowana w ramach operacji wyrzucania elementów bezużytecznych. Nie wywołania zwrotne czasomierza są wywoływane po tym. Ponadto środowiska uruchomieniowego złośliwych użytkowników nie zachowuje żadnych informacji o czasomierzy, które były uruchomione przed dezaktywacji. Jest aktora, aby zarejestrować wszelkie czasomierzy, które wymaga uaktwnieniu go w przyszłości. Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [aktora wyrzucanie elementów bezużytecznych](service-fabric-reliable-actors-lifecycle.md).

## <a name="actor-reminders"></a>Przypomnienia aktora
Przypomnienia są mechanizm umożliwiający wyzwalanie trwałe wywołań zwrotnych na aktora w określonych godzinach. Czasomierze odpowiada ich funkcje. Jednak w przeciwieństwie do czasomierze, przypomnienia są wyzwalane we wszystkich okolicznościach, dopóki aktora jawnie wyrejestrowuje je lub aktora jest jawnie usunięte. W szczególności przypomnienia są wyzwalane przez aktora deactivations i tryb failover, ponieważ środowiska uruchomieniowego złośliwych użytkowników będzie nadal występował, informacje o przypomnienia aktora przy użyciu dostawcy stanu aktora. Należy pamiętać, że niezawodność przypomnienia jest związany z gwarancje niezawodności stanu udostępniane przez dostawcę stanu aktora. Oznacza to, że osób, w których trwałość stanu ma wartość None, monity nie będą uruchamiane po przejściu w tryb failover. 

Aby zarejestrować przypomnienie, wywołuje aktora `RegisterReminderAsync` metoda w klasie podstawowej, jak pokazano w poniższym przykładzie:

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),    //The amount of time to delay before firing the reminder
        TimeSpan.FromDays(1));    //The time interval between firing of reminders
}
```

```Java
@Override
protected CompletableFuture onActivateAsync()
{
    String reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    ActorReminder reminderRegistration = this.registerReminderAsync(
            reminderName,
            state,
            dueTime,    //The amount of time to delay before firing the reminder
            period);    //The time interval between firing of reminders
}
```

W tym przykładzie `"Pay cell phone bill"` jest nazwą monitu. Jest to ciąg, który aktora używany do jednoznacznego identyfikowania przypomnienia. `BitConverter.GetBytes(amountInDollars)`(C#) jest kontekstem, który jest skojarzony z monitu. Go zostaną przekazane do aktora jako argument do wywołania zwrotnego monitu, tj. `IRemindable.ReceiveReminderAsync`(C#) lub `Remindable.receiveReminderAsync`(Java).

Aktorów używających przypomnienia muszą implementować `IRemindable` interfejsu, jak pokazano w poniższym przykładzie.

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public ToDoListActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```
```Java
public class ToDoListActorImpl extends FabricActor implements ToDoListActor, Remindable
{
    public ToDoListActor(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture receiveReminderAsync(String reminderName, byte[] context, Duration dueTime, Duration period)
    {
        if (reminderName.equals("Pay cell phone bill"))
        {
            int amountToPay = ByteBuffer.wrap(context).getInt();
            System.out.println("Please pay your cell phone bill of " + amountToPay);
        }
        return CompletableFuture.completedFuture(true);
    }

```

Po wyzwoleniu przypomnienie wywoła środowisko uruchomieniowe Reliable Actors `ReceiveReminderAsync`(C#) lub `receiveReminderAsync`(Java) metoda aktora. Aktora można zarejestrować wiele przypomnienia i `ReceiveReminderAsync`(C#) lub `receiveReminderAsync`(Java) metoda jest wywoływana po żadnego z tych przypomnienia wyzwoleniu. Aktor można użyć nazwy monitu, który jest przekazywany do `ReceiveReminderAsync`(C#) lub `receiveReminderAsync`— metoda (Java), aby dowiedzieć się, które monitu zostało wyzwolone.

Podmiotów środowiska uruchomieniowego zapisuje aktora stan, kiedy `ReceiveReminderAsync`(C#) lub `receiveReminderAsync`zakończeniu wywołania (Java). Jeśli wystąpi błąd podczas zapisywania stanu, ten obiekt aktora spowoduje wyłączenie i nowe wystąpienie zostanie aktywowany.

Wyrejestrować przypomnienie, wywołuje aktora `UnregisterReminderAsync`(C#) lub `unregisterReminderAsync`— metoda (Java), jak pokazano w poniższych przykładach.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Jak pokazano powyżej, `UnregisterReminderAsync`(C#) lub `unregisterReminderAsync`— metoda (Java) akceptuje `IActorReminder`(C#) lub `ActorReminder`interfejsu (Java). Obsługuje klasy podstawowej aktora `GetReminder`(C#) lub `getReminder`— metoda (Java), który może służyć do pobierania `IActorReminder`(C#) lub `ActorReminder`interfejsu (Java), przekazując w nazwie monitu. Jest to wygodna, ponieważ aktora nie potrzeba utrwalenia `IActorReminder`(C#) lub `ActorReminder`interfejsu (Java), który został zwrócony z `RegisterReminder`(C#) lub `registerReminder`wywołanie metody (Java).

## <a name="next-steps"></a>Następne kroki
Więcej informacji na temat zdarzeń niezawodnego aktora i ponowne wejście:
* [Zdarzenia aktora](service-fabric-reliable-actors-events.md)
* [Wielobieżność aktora](service-fabric-reliable-actors-reentrancy.md)
