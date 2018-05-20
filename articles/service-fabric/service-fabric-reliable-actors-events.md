---
title: Zdarzenia w podstawie aktora Azure mikrousług | Dokumentacja firmy Microsoft
description: Wprowadzenie do zdarzeń dla elementów Reliable Actors sieci szkieletowej usług.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: aa01b0f7-8f88-403a-bfe1-5aba00312c24
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: ed920c8d4ff7254b19c6eef8f5961593bb56bacf
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="actor-events"></a>Zdarzenia aktora
Zdarzenia aktora umożliwiają wysyłanie powiadomień optymalnych z aktora do klientów. Aktora zdarzeń są przeznaczone dla komunikacji aktora do klienta i nie można używać do komunikacji aktora aktora.

Poniższe fragmenty kodu przedstawiają sposób korzystanie ze zdarzeń aktora w aplikacji.

Zdefiniuj interfejs, który opisuje zdarzenia opublikowanych przez aktora. Ten interfejs musi pochodzić od `IActorEvents` interfejsu. Argumenty metod muszą być [serializacji kontraktu danych](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Metody musi zwracać typ void, jako zdarzenie powiadomienia są jednym ze sposobów i optymalnego.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```
```Java
public interface GameEvents implements ActorEvents
{
    void gameScoreUpdated(UUID gameId, String currentScore);
}
```
Deklarowanie zdarzeń, opublikowanych przez aktora w interfejsie aktora.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```
```Java
public interface GameActor extends Actor, ActorEventPublisherE<GameEvents>
{
    CompletableFuture<?> updateGameStatus(GameStatus status);

    CompletableFuture<String> getGameScore();
}
```
Po stronie klienta implementacji programu obsługi zdarzeń.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

```Java
class GameEventsHandler implements GameEvents {
    public void gameScoreUpdated(UUID gameId, String currentScore)
    {
        System.out.println("Updates: Game: "+gameId+" ,Score: "+currentScore);
    }
}
```

Na komputerze klienckim utworzyć proxy aktora, która publikuje zdarzenia i subskrybowanie jego zdarzeń.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

W przypadku przełączenia do trybu failover aktora może przełączyć się inny proces lub węzeł. Serwer proxy aktora zarządza aktywne subskrypcje i automatycznie ponownie subskrybuje je. Można kontrolować interwał ponownej subskrypcji za pośrednictwem `ActorProxyEventExtensions.SubscribeAsync<TEvent>` interfejsu API. Aby anulować subskrypcję, należy użyć `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` interfejsu API.

Publikowanie zdarzenia w aktora, po ich wprowadzeniu. W przypadku subskrybentów w zdarzeniu środowiska wykonawczego podmiotów wysyła je powiadomienia.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Kolejne kroki
* [Wielobieżność aktora](service-fabric-reliable-actors-reentrancy.md)
* [Aktora Diagnostyka i monitorowanie wydajności](service-fabric-reliable-actors-diagnostics.md)
* [Dokumentację referencyjną aktora interfejsu API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# przykładowy kod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C# .NET Core przykładowy kod](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Java przykładowy kod](http://github.com/Azure-Samples/service-fabric-java-getting-started)
