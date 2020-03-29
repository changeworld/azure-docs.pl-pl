---
title: Wiarygodne uwagi aktorów na temat serializacji typu aktora
description: W tym artykule omówiono podstawowe wymagania dotyczące definiowania klas serializable, które mogą służyć do definiowania stanów i interfejsów niezawodnych aktorów sieci szkieletowej usług
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 876c4f5f45ff6c81a53274cf32e8bebecc1acfce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349310"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Uwagi dotyczące serializacji typu Reliable Actors typu Service Fabric
Argumenty wszystkich metod, typy wyników zadań zwracanych przez każdą metodę w interfejsie aktora i obiekty przechowywane w menedżerze stanu aktora muszą być [serializowalne umowy danych.](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer) Dotyczy to również argumentów metod zdefiniowanych w [interfejsach zdarzeń aktora](service-fabric-reliable-actors-events.md). (Metody interfejsu zdarzenia aktora zawsze zwracają void.)

## <a name="custom-data-types"></a>Niestandardowe typy danych
W tym przykładzie następujący interfejs aktora definiuje metodę, która `VoicemailBox`zwraca niestandardowy typ danych o nazwie:

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

```Java
public interface VoiceMailBoxActor extends Actor
{
    CompletableFuture<VoicemailBox> getMailBoxAsync();
}
```

Interfejs jest implementowany przez aktora, który używa menedżera `VoicemailBox` stanu do przechowywania obiektu:

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class VoiceMailBoxActorImpl extends FabricActor implements VoicemailBoxActor
{
    public VoiceMailBoxActorImpl(ActorService actorService, ActorId actorId)
    {
         super(actorService, actorId);
    }

    public CompletableFuture<VoicemailBox> getMailBoxAsync()
    {
         return this.stateManager().getStateAsync("Mailbox");
    }
}

```

W tym przykładzie `VoicemailBox` obiekt jest serializowany, gdy:

* Obiekt jest przesyłany między wystąpieniem aktora a obiektem wywołującym.
* Obiekt jest zapisywany w Menedżerze stanu, gdzie jest utrwalony na dysku i replikowany do innych węzłów.

Struktura niezawodnego aktora używa serializacji DataContract. W związku z tym obiekty danych niestandardowych i ich członków muszą być adnotacją z **DataContract** i **DataMember** atrybutów, odpowiednio.

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```
```Java
public class Voicemail implements Serializable
{
    private static final long serialVersionUID = 42L;

    private UUID id;                    //getUUID() and setUUID()

    private String message;             //getMessage() and setMessage()

    private GregorianCalendar receivedAt; //getReceivedAt() and setReceivedAt()
}
```


```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```
```Java
public class VoicemailBox implements Serializable
{
    static final long serialVersionUID = 42L;
    
    public VoicemailBox()
    {
        this.messageList = new ArrayList<Voicemail>();
    }

    private List<Voicemail> messageList;   //getMessageList() and setMessageList()

    private String greeting;               //getGreeting() and setGreeting()
}
```


## <a name="next-steps"></a>Następne kroki
* [Cykl życia aktora i wyrzucanie elementów bezużytecznych](service-fabric-reliable-actors-lifecycle.md)
* [Czasomierze aktorów i przypomnienia](service-fabric-reliable-actors-timers-reminders.md)
* [Wydarzenia aktorskie](service-fabric-reliable-actors-events.md)
* [Ponowne entrancy aktora](service-fabric-reliable-actors-reentrancy.md)
* [Polimorfizm aktorów i zorientowane obiektowo wzory projektowe](service-fabric-reliable-actors-polymorphism.md)
* [Diagnostyka aktora i monitorowanie wydajności](service-fabric-reliable-actors-diagnostics.md)
