---
title: Ponownego rozpoczęcia w podstawie aktora Azure mikrousług | Dokumentacja firmy Microsoft
description: Wprowadzenie do ponownego rozpoczęcia dla elementów Reliable Actors sieci szkieletowej usług
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: be23464a-0eea-4eca-ae5a-2e1b650d365e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 40f52cb399f2d7391657ce4356a0c30921d46e5f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207098"
---
# <a name="reliable-actors-reentrancy"></a>Niezawodne wielobieżność złośliwych użytkowników
Środowisko uruchomieniowe Reliable Actors, domyślnie umożliwia wywołanie logiczną na podstawie kontekstu wielobieżność. Dzięki temu podmiotów być współużytkowane, jeśli są w tym samym łańcuchu kontekstu wywołania. Na przykład aktora A wysyła komunikat do aktora B, który wysyła wiadomość do aktora. Podczas przetwarzania komunikatu C aktora wywołuje aktora A, wiadomość jest współużytkowane, więc może być. Komunikaty, które są częścią kontekstu wywołania różnych zostaną zablokowane na aktora A zakończenie przetwarzania.

Dostępne są dwie opcje do ponownego rozpoczęcia aktora zdefiniowane w `ActorReentrancyMode` wyliczenia:

* `LogicalCallContext` (domyślnie)
* `Disallowed` — Wyłącza ponowne wejście

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```
```Java
public enum ActorReentrancyMode
{
    LogicalCallContext(1),
    Disallowed(2)
}
```
Wielobieżność można skonfigurować w `ActorService`tego ustawienia podczas rejestracji. Ustawienie ma zastosowanie do wszystkich wystąpień aktora utworzone w usłudze aktora.

W poniższym przykładzie przedstawiono usługi aktora, która ustawia tryb wielobieżność `ActorReentrancyMode.Disallowed`. W tym przypadku aktora wysyła komunikat współużytkowane do innego aktora, wyjątek typu `FabricException` zostanie wygenerowany.

```csharp
static class Program
{
    static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<Actor1>(
                (context, actorType) => new ActorService(
                    context,
                    actorType, () => new Actor1(),
                    settings: new ActorServiceSettings()
                    {
                        ActorConcurrencySettings = new ActorConcurrencySettings()
                        {
                            ReentrancyMode = ActorReentrancyMode.Disallowed
                        }
                    }))
                .GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}
```
```Java
static class Program
{
    static void Main()
    {
        try
        {
            ActorConcurrencySettings actorConcurrencySettings = new ActorConcurrencySettings();
            actorConcurrencySettings.setReentrancyMode(ActorReentrancyMode.Disallowed);

            ActorServiceSettings actorServiceSettings = new ActorServiceSettings();
            actorServiceSettings.setActorConcurrencySettings(actorConcurrencySettings);

            ActorRuntime.registerActorAsync(
                Actor1.getClass(),
                (context, actorType) -> new FabricActorService(
                    context,
                    actorType, () -> new Actor1(),
                    null,
                    stateProvider,
                    actorServiceSettings, timeout);

            Thread.sleep(Long.MAX_VALUE);
        }
        catch (Exception e)
        {
            throw e;
        }
    }
}
```


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej na temat ponownego rozpoczęcia w [dokumentacji interfejsu API aktora](https://msdn.microsoft.com/library/azure/dn971626.aspx)
