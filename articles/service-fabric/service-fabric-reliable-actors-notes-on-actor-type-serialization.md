---
title: Serializacja typów Reliable Actors dodatkowej aktora | Dokumentacja firmy Microsoft
description: W tym artykule omówiono podstawowe wymagania dotyczące definiowania klas możliwych do serializacji, które mogą służyć do zdefiniowania stanów elementów Reliable Actors usługi Service Fabric i interfejsy
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 6e50e4dc-969a-4a1c-b36c-b292d964c7e3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: c8eeeb0ade6ca002adf3211cbf49127be9b76edb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725656"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Uwagi dotyczące usługi Service Fabric Reliable Actors — serializacja typów
Argumenty wszystkie metody, typy wyników zadania zwracane przez poszczególnych metod interfejsu aktora i obiekty przechowywane w Menedżer stanu aktora należy [kontraktu danych serializacji](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer). Dotyczy to również argumenty metody zdefiniowane w [interfejsów zdarzeń aktora](service-fabric-reliable-actors-events.md). (Metod interfejsu zdarzenia aktora zawsze zwracać typ void.)

## <a name="custom-data-types"></a>Niestandardowe typy danych
W tym przykładzie następujący interfejs aktora definiuje metodę, która zwraca niestandardowego typu danych o nazwie `VoicemailBox`:

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

Interfejs jest implementowany przez aktora, który używa Menedżera stanu do przechowywania `VoicemailBox` obiektu:

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

W tym przykładzie `VoicemailBox` serializowany jest obiekt po:

* Obiekt są przesyłane między wystąpienia aktora, a obiekt wywołujący.
* Obiekt zostanie zapisany w przez menedżera stanu, w którym jest utrwalony na dysku i replikowane do innych węzłów.

Framework Reliable Actor używa schematu DataContract serializacji. W związku z tym, obiektów danych niestandardowych i ich elementów członkowskich musi być oznaczona przy użyciu **DataContract** i **DataMember** , odpowiednio, atrybutów.

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


## <a name="next-steps"></a>Kolejne kroki
* [Kolekcja aktora cykl życia i odzyskiwanie](service-fabric-reliable-actors-lifecycle.md)
* [Aktor czasomierze i przypomnienia](service-fabric-reliable-actors-timers-reminders.md)
* [Zdarzenia aktora](service-fabric-reliable-actors-events.md)
* [Wielobieżność aktora](service-fabric-reliable-actors-reentrancy.md)
* [Polimorfizm aktora i wzorce programowania obiektowego](service-fabric-reliable-actors-polymorphism.md)
* [Aktor Diagnostyka i monitorowanie wydajności](service-fabric-reliable-actors-diagnostics.md)
