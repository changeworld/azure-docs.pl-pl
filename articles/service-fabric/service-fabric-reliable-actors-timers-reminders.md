---
title: Reliable Actors czasomierze i przypomnienia | Dokumentacja firmy Microsoft
description: Wprowadzenie do czasomierzy i przypomnień dla elementów Reliable Actors usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: amanbha
ms.assetid: 00c48716-569e-4a64-bd6c-25234c85ff4f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 323de842645cced3c6f490e98112fcbcd184aa64
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667433"
---
# <a name="actor-timers-and-reminders"></a>Aktor czasomierze i przypomnienia
Aktorzy można zaplanować okresowe prace nad samodzielnie, rejestrując czasomierze i przypomnienia. W tym artykule przedstawiono sposób użycia czasomierze i przypomnienia i wyjaśnia różnice między nimi.

## <a name="actor-timers"></a>Czasomierze aktora
Czasomierze aktora zapewniają prosty otokę .NET lub Java czasomierza upewnij się, że metody wywołania zwrotnego przestrzegać współbieżności tury gwarantuje, że Aktorzy środowisko wykonawcze zapewnia.

Można użyć aktorów `RegisterTimer`(C#) lub `registerTimer`(Java) i `UnregisterTimer`(C#) lub `unregisterTimer`metody (Java) w swojej klasie bazowej można rejestrować i wyrejestrowywać swoje czasomierzy. W poniższym przykładzie pokazano użycie czasomierza interfejsów API. Interfejsy API są bardzo podobne do czasomierz .NET lub Java czasomierza. W tym przykładzie, kiedy czasomierz ma zostać, środowisko uruchomieniowe aktorów wywoła `MoveObject`(C#) lub `moveObject`— metoda (Java). Metoda jest gwarantowane do przestrzegania tury współbieżności. Oznacza to, że nie ma innych metod aktora lub wywołań zwrotnych czasomierza/przypomnienie będzie w toku to wywołanie zwrotne zakończenie wykonywania.

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

Na następny okres timer rozpoczyna się po wywołania zwrotnego kończy wykonywanie. Oznacza to, czy czasomierz została zatrzymana, gdy wywołanie zwrotne jest wykonywane i jest uruchomiona, po zakończeniu wywołania zwrotnego.

Środowiska uruchomieniowego aktorów zapisuje zmiany wprowadzone do menedżera stanu aktora, po zakończeniu wywołania zwrotnego. Jeśli wystąpi błąd podczas zapisywania stanu, ten obiekt aktora zostaną dezaktywowane i nowe wystąpienie zostanie uaktywnione.

Zatrzymuje wszystkie czasomierze dezaktywacji aktora w ramach operacji wyrzucania elementów bezużytecznych. Nie czasomierza wywołania zwrotne są wywoływane po tym. Ponadto środowisko uruchomieniowe aktorów nie zachowuje żadnych informacji na temat czasomierzy, które były uruchomione przed dezaktywacji. Jest aktora, aby zarejestrować wszystkie zegary, których potrzebuje, gdy zostanie ponownie aktywowany w przyszłości. Aby uzyskać więcej informacji, zobacz sekcję na [aktora wyrzucania elementów bezużytecznych](service-fabric-reliable-actors-lifecycle.md).

## <a name="actor-reminders"></a>Przypomnienia aktora
Monity są mechanizm do wyzwolenia trwałego wywołań zwrotnych na aktora w określonych godzinach. Ich funkcjonalność jest podobny do czasomierzy. Jednak w przeciwieństwie do czasomierze, przypomnienia są wyzwalane w każdych okolicznościach aż do ich wyrejestrowuje jawnie aktora lub jawnie usuniesz aktora. W szczególności przypomnienia są wyzwalane deactivations aktora i pracy w trybie Failover, ponieważ środowisko uruchomieniowe aktorów będzie się powtarzać, informacje o przypomnienia aktora przy użyciu dostawcy stanu aktora. Należy pamiętać, że niezawodność przypomnienia jest powiązany z gwarancje niezawodności stanu udostępnionych przez dostawcę stanu aktora. Oznacza to, że dla podmiotów, w których utrwalanie stanu jest ustawiona na wartość None, monitów, nie zostanie wyzwolony po przejściu w tryb failover. 

Aby zarejestrować przypomnienie, wywołań aktora `RegisterReminderAsync` metody dostarczone w klasie bazowej, jak pokazano w poniższym przykładzie:

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

W tym przykładzie `"Pay cell phone bill"` nazywa się przypomnienie. Jest to ciąg, który aktora używany do jednoznacznego identyfikowania przypomnienie. `BitConverter.GetBytes(amountInDollars)`(C#) jest kontekst, który jest skojarzony z przypomnienia. Jej zostanie przekazany z powrotem do aktora jako argument wywołania zwrotnego przypomnienie, czyli `IRemindable.ReceiveReminderAsync`(C#) lub `Remindable.receiveReminderAsync`(Java).

Aktorzy używających przypomnienia musi implementować `IRemindable` interfejsu, jak pokazano w poniższym przykładzie.

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

Po wyzwoleniu przypomnienia elementów Reliable Actors środowiska uruchomieniowego wywoła `ReceiveReminderAsync`(C#) lub `receiveReminderAsync`metody aktora (Java). Aktor można zarejestrować wiele przypomnień i `ReceiveReminderAsync`(C#) lub `receiveReminderAsync`(Java), metoda jest wywoływana po dowolnego z tych monitów wyzwoleniu. Aktor można użyć nazwy przypomnienia, który jest przekazywany do `ReceiveReminderAsync`(C#) lub `receiveReminderAsync`metodę (Java), aby zorientować się przypomnienie, które zostały wyzwolone.

Aktorzy środowiska uruchomieniowego zapisuje aktora stan, kiedy `ReceiveReminderAsync`(C#) lub `receiveReminderAsync`zakończy się wywołanie (Java). Jeśli wystąpi błąd podczas zapisywania stanu, ten obiekt aktora zostaną dezaktywowane i nowe wystąpienie zostanie uaktywnione.

Aby wyrejestrować przypomnienie, wywołuje Aktor `UnregisterReminderAsync`(C#) lub `unregisterReminderAsync`metodzie (Java), jak pokazano w poniższych przykładach.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Jak wspomniano powyżej, `UnregisterReminderAsync`(C#) lub `unregisterReminderAsync`(Java) metoda przyjmuje `IActorReminder`(C#) lub `ActorReminder`interfejsu (Java). Obsługuje klasy bazowej aktora `GetReminder`(C#) lub `getReminder`— metoda (Java), który może służyć do pobierania `IActorReminder`(C#) lub `ActorReminder`interfejsu (Java), przekazując nazwę przypomnienia. Jest to wygodne, ponieważ aktora nie musi pozostać `IActorReminder`(C#) lub `ActorReminder`interfejsu (Java), który został zwrócony z `RegisterReminder`(C#) lub `registerReminder`wywołania metody (Java).

## <a name="next-steps"></a>Następne kroki
Więcej informacji na temat zdarzeń Reliable Actor i współużytkowania wątkowości:
* [Zdarzenia aktora](service-fabric-reliable-actors-events.md)
* [Wielobieżność aktora](service-fabric-reliable-actors-reentrancy.md)
