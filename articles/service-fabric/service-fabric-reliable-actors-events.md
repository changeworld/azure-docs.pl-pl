---
title: Zdarzenia w aktorów aktorów usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Wprowadzenie do zdarzeń dla elementów Reliable Actors usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: aa01b0f7-8f88-403a-bfe1-5aba00312c24
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: 9075fc8391e8afa21e3963c1eff6a630c586d647
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60726404"
---
# <a name="actor-events"></a>Zdarzenia aktora
Zdarzenia aktora udostępniają sposób wysyłania powiadomień optymalnych z aktora do klientów. Zdarzenia aktora są przeznaczone dla komunikacji aktora do klienta i nie powinien być używany do komunikacji aktora do aktora.

Poniższe fragmenty kodu przedstawiają sposób korzystanie ze zdarzeń aktora w aplikacji.

Zdefiniuj interfejs, który opisuje zdarzenia opublikowane przez aktora. Ten interfejs musi pochodzić od `IActorEvents` interfejsu. Argumenty metody muszą być [kontraktu danych serializacji](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Metody musi zwracać typ void, jako zdarzenie powiadomienia są jednym ze sposobów i najlepszy nakład pracy.

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
Deklarowanie zdarzeń publikowanych przez actor interfejs aktora.

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

Na komputerze klienckim Utwórz serwer proxy do aktora, który publikuje zdarzenie i subskrypcję ze zdarzeniami.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

W przypadku przejścia w tryb failover Aktor może przełączyć w tryb failover do innego procesu lub węzeł. Serwer proxy aktora zarządza aktywne subskrypcje i automatycznie ponownie subskrybuje je. Można kontrolować interwał ponownej subskrypcji za pośrednictwem `ActorProxyEventExtensions.SubscribeAsync<TEvent>` interfejsu API. Aby anulować subskrypcję, należy użyć `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` interfejsu API.

Publikowanie zdarzeń w aktora, po ich wprowadzeniu. W przypadku subskrybentów zdarzenia środowiska uruchomieniowego aktorów wysyła do nich powiadomienia.

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
* [Aktor Diagnostyka i monitorowanie wydajności](service-fabric-reliable-actors-diagnostics.md)
* [Dokumentacja interfejsu API aktora](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Kod przykładowy w języku C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C# .NET Core przykładowy kod](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Kod przykładowy w języku Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)
