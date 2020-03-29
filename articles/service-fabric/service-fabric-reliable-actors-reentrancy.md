---
title: Ponowne entranowanie w podmiotach sieci szkieletowej usług Azure
description: Wprowadzenie do ponownego wniesienia dla usługi Sieci szkieletowej wiarygodnych aktorów, sposób logicznie uniknąć blokowania na podstawie kontekstu wywołania.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 46ce91e607341e2fbdc0b6a3018e74cb24e76839
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645535"
---
# <a name="reliable-actors-reentrancy"></a>Niezawodni aktorzy ponownie
Środowisko uruchomieniowe Reliable Actors domyślnie umożliwia ponowne wymierzanie kontekstu oparte na wywołaniu logicznym. Pozwala to na podmioty być reentrant, jeśli są one w tym samym łańcuchu kontekstu wywołania. Na przykład aktor A wysyła wiadomość do aktora B, który wysyła wiadomość do aktora C. W ramach przetwarzania wiadomości, jeśli aktor C wywołuje aktora A, wiadomość jest reentrant, więc będzie dozwolone. Wszystkie inne wiadomości, które są częścią kontekstu różnych wywołań zostaną zablokowane na aktora A, dopóki nie zakończy przetwarzania.

Istnieją dwie opcje dostępne dla ponownego wniesienia aktora zdefiniowane w wyliczenia: `ActorReentrancyMode`

* `LogicalCallContext`(zachowanie domyślne)
* `Disallowed`- wyłącza reentrancy

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
Ponowne entrancy można skonfigurować w ustawieniach `ActorService`'s podczas rejestracji. Ustawienie dotyczy wszystkich wystąpień aktora utworzonych w usłudze aktora.

W poniższym przykładzie przedstawiono usługę aktora, `ActorReentrancyMode.Disallowed`która ustawia tryb ponownego wniesienia na . W takim przypadku jeśli aktor wysyła wiadomość wielokrotnego wniesienia do innego aktora, zostanie zgłoszony wyjątek typu. `FabricException`

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


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o reentrancy w [dokumentacji referencyjnej interfejsu API aktora](https://msdn.microsoft.com/library/azure/dn971626.aspx)
