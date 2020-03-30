---
title: Niezawodne czasomierze aktorów i przypomnienia
description: Wprowadzenie do czasomierzy i przypomnienia dla sieci szkieletowej usług wiarygodnych podmiotów, w tym wskazówki dotyczące tego, kiedy należy używać każdego z nich.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 02d6220b31ee9c991e8450759bf46759af6177a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639619"
---
# <a name="actor-timers-and-reminders"></a>Czasomierze aktorów i przypomnienia
Aktorzy mogą planować okresowe prace nad sobą, rejestrując czasomierze lub przypomnienia. W tym artykule pokazano, jak używać czasomierzy i przypomnień oraz wyjaśniono różnice między nimi.

## <a name="actor-timers"></a>Czasomierze aktorów
Czasomierze aktora zapewniają proste otoki wokół czasomierza .NET lub Java, aby upewnić się, że metody wywołania zwrotnego są zgodne z koncelisami, które zapewnia środowisko wykonawcze Actors.

Aktorzy mogą `RegisterTimer`używać (C#) `registerTimer`lub `UnregisterTimer`(Java) `unregisterTimer`i (C#) lub (Java) w swojej klasie podstawowej do rejestrowania i wyrejestrowania swoich czasomierzy. Poniższy przykład pokazuje użycie interfejsów API czasomierza. Interfejsy API są bardzo podobne do czasomierza .NET lub czasomierza Java. W tym przykładzie, gdy czasomierz jest należny, środowisko wykonawcze Actors wywoła `MoveObject`metodę (C#) lub `moveObject`(Java). Metoda jest gwarantowana do przestrzegania współbieżności turowej. Oznacza to, że żadne inne metody aktora lub czasomierz/przypomnienie wywołania zwrotne będą w toku, dopóki to wywołanie zwrotne zakończy wykonanie.

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

Następny okres czasomierza rozpoczyna się po zakończeniu wykonywania wywołania zwrotnego. Oznacza to, że czasomierz jest zatrzymany podczas wywoływania zwrotnego jest wykonywany i jest uruchamiany po zakończeniu wywołania zwrotnego.

Środowisko wykonawcze Actors zapisuje zmiany wprowadzone w Menedżerze stanu aktora po zakończeniu wywołania zwrotnego. Jeśli wystąpi błąd podczas zapisywania stanu, ten obiekt aktora zostanie dezaktywowany i zostanie aktywowane nowe wystąpienie.

Wszystkie czasomierze są zatrzymywane, gdy aktor jest dezaktywowany jako część wyrzucania elementów bezużytecznych. Po tym wywołaniu zwrotnym czasomierza nie są wywoływane żadne wywołania zwrotne. Ponadto środowisko wykonawcze Actors nie zachowuje żadnych informacji o czasomierzach, które były uruchomione przed dezaktywacją. To do aktora, aby zarejestrować wszelkie czasomierze, które potrzebuje, gdy jest reaktywowany w przyszłości. Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [wyrzucania elementów bezużytecznych aktora](service-fabric-reliable-actors-lifecycle.md).

## <a name="actor-reminders"></a>Przypomnienia o aktorach
Przypomnienia są mechanizmem wyzwalania trwałych wywołań zwrotnych na aktora w określonych godzinach. Ich funkcjonalność jest podobna do czasomierzy. Ale w przeciwieństwie do czasomierzy przypomnienia są wyzwalane w każdych okolicznościach, dopóki aktor jawnie wyrejestruje je lub aktor zostanie jawnie usunięty. W szczególności przypomnienia są wyzwalane przez dezaktywacji aktora i pracy awaryjnej, ponieważ środowisko wykonawcze Actors utrzymuje informacje o przypomnienia aktora przy użyciu dostawcy stanu aktora. Należy pamiętać, że wiarygodność przypomnień jest związana z gwarancjami niezawodności państwa dostarczonymi przez dostawcę stanu aktora. Oznacza to, że dla podmiotów, których trwałość stanu jest ustawiona na Brak, przypomnienia nie będą uruchamiane po przemijanie awaryjne. 

Aby zarejestrować przypomnienie, aktor `RegisterReminderAsync` wywołuje metodę podaną w klasie podstawowej, jak pokazano w poniższym przykładzie:

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

W tym `"Pay cell phone bill"` przykładzie jest nazwa przypomnienia. Jest to ciąg używany przez aktora do jednoznacznej identyfikacji przypomnienia. `BitConverter.GetBytes(amountInDollars)`(C#) jest kontekstem skojarzonym z przypomnieniem. Zostanie on przekazany z powrotem do aktora jako argument do `IRemindable.ReceiveReminderAsync`wywołania zwrotnego `Remindable.receiveReminderAsync`przypomnienia, czyli (C#) lub (Java).

Podmioty korzystające z przypomnień muszą implementować `IRemindable` interfejs, jak pokazano w poniższym przykładzie.

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

Po wyzwoleniu przypomnienia środowisko uruchomieniowe Reliable Actors `ReceiveReminderAsync`wywoła metodę `receiveReminderAsync`(C#) lub (Java) na aktora. Aktor może zarejestrować wiele przypomnień, a metoda `ReceiveReminderAsync`(C#) lub `receiveReminderAsync`(Java) jest wywoływana po wyzwoleniu któregokolwiek z tych przypomnień. Aktor może użyć nazwy przypomnienia, która `ReceiveReminderAsync`jest przekazywana `receiveReminderAsync`do (C#) lub (Java), aby dowiedzieć się, które przypomnienie zostało wyzwolone.

Środowisko wykonawcze Actors zapisuje stan aktora `ReceiveReminderAsync`po zakończeniu `receiveReminderAsync`wywołania (C#) lub (Java). Jeśli wystąpi błąd podczas zapisywania stanu, ten obiekt aktora zostanie dezaktywowany i zostanie aktywowane nowe wystąpienie.

Aby wyrejestrować przypomnienie, `UnregisterReminderAsync`aktor wywołuje `unregisterReminderAsync`metodę (C#) lub (Java), jak pokazano w poniższych przykładach.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Jak `UnregisterReminderAsync`pokazano powyżej, metoda (C#) lub `unregisterReminderAsync`(Java) akceptuje interfejs `IActorReminder`(C#) lub `ActorReminder`(Java). Klasa podstawowa aktora `GetReminder`obsługuje (C#) lub `getReminder`(Java), która może `IActorReminder`służyć do `ActorReminder`pobierania (C#) lub (Java) interfejs, przekazując w nazwie przypomnienia. Jest to wygodne, ponieważ aktor nie `IActorReminder`musi utrwalić (C#) `ActorReminder`lub `RegisterReminder`(Java) `registerReminder`interfejs, który został zwrócony z (C#) lub (Java) wywołanie metody.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o zdarzeniach niezawodnego aktora i reentrancy:
* [Wydarzenia aktorskie](service-fabric-reliable-actors-events.md)
* [Ponowne entrancy aktora](service-fabric-reliable-actors-reentrancy.md)
