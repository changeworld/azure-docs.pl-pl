---
title: Zdarzenia u aktorów usługi Azure fabric oparte na aktorach
description: Dowiedz się więcej o zdarzeniach dla niezawodnych aktorów sieci szkieletowej usług, skutecznym sposobem komunikowania się między aktorem a klientem.
author: vturecek
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: 73c149a0d0992fecd1acf633891057570285df64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639670"
---
# <a name="actor-events"></a>Wydarzenia aktorskie
Zdarzenia aktora umożliwiają wysyłanie powiadomień o najlepszym wysiłku od aktora do klientów. Zdarzenia aktora są przeznaczone do komunikacji między aktorem a klientem i nie powinny być używane do komunikacji między aktorami.

Poniższe fragmenty kodu pokazują, jak używać zdarzeń aktora w aplikacji.

Zdefiniuj interfejs opisujący zdarzenia opublikowane przez aktora. Ten interfejs musi pochodzić `IActorEvents` z interfejsu. Argumenty metod muszą być [serializable umowy danych](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Metody muszą zwracać void, jak powiadomienia o zdarzeniach są jednym ze sposobów i najlepszych starań.

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
Zadeklaruj zdarzenia opublikowane przez aktora w interfejsie aktora.

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
Po stronie klienta zaimplementuj program obsługi zdarzeń.

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

Na kliencie utwórz serwer proxy dla aktora, który publikuje zdarzenie i subskrybować jego zdarzenia.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

W przypadku pracy awaryjnej aktora może awaryjnie do innego procesu lub węzła. Serwer proxy aktora zarządza aktywnymi subskrypcjami i automatycznie subskrybuje je ponownie. Interwał ponownej subskrypcji można `ActorProxyEventExtensions.SubscribeAsync<TEvent>` kontrolować za pośrednictwem interfejsu API. Aby zrezygnować z subskrypcji, użyj `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` interfejsu API.

Na aktora, publikować wydarzenia, jak się zdarzają. Jeśli istnieją subskrybenci zdarzenia, środowisko wykonawcze Actors wysyła im powiadomienie.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Następne kroki
* [Ponowne entrancy aktora](service-fabric-reliable-actors-reentrancy.md)
* [Diagnostyka aktora i monitorowanie wydajności](service-fabric-reliable-actors-diagnostics.md)
* [Dokumentacja referencyjna interfejsu API aktora](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# Przykładowy kod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykładowy kod C# .NET Core](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Przykładowy kod Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)
