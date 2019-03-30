---
title: Współużytkowania wątkowości w aktorów usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Wprowadzenie do współużytkowania wątkowości dla elementów Reliable Actors usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: amanbha
ms.assetid: be23464a-0eea-4eca-ae5a-2e1b650d365e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 46682787bac2d60d188384a4078ca2fa1f46ae7a
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669031"
---
# <a name="reliable-actors-reentrancy"></a>Reliable Actors współużytkowania wątkowości
Środowisko uruchomieniowe elementów Reliable Actors, domyślnie umożliwia wielobieżność oparte na kontekście wywołań logicznych. Dzięki temu Aktorzy stanowią współużytkowane, gdy mają one ten sam łańcuch wywołań w kontekście. Na przykład A Aktor wysyła komunikat do aktora B, który wysyła wiadomość do aktora. W ramach przetwarzania wiadomości Jeśli C aktora wywołuje aktora A, komunikat jest współużytkowane, dzięki czemu będzie można. Inne komunikaty, które są częścią kontekstu wywołań różnych zostaną zablokowane na A Aktor, do momentu zakończenia przetwarzania.

Dostępne są dwie opcje współużytkowania wątkowości aktora zdefiniowane w `ActorReentrancyMode` wyliczenia:

* `LogicalCallContext` (zachowanie domyślne)
* `Disallowed` — Wyłącza współużytkowania wątkowości

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
Można skonfigurować współużytkowania wątkowości w `ActorService`tego ustawienia podczas rejestracji. To ustawienie ma zastosowanie do wszystkich wystąpień aktora utworzone w usłudze aktora.

Poniższy przykład pokazuje usługi aktora, który ustawia tryb współużytkowania wątkowości `ActorReentrancyMode.Disallowed`. W tym przypadku Aktor wysyła komunikat współużytkowane do innego aktora, wyjątek typu `FabricException` zostanie zgłoszony.

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
* Dowiedz się więcej na temat współużytkowania wątkowości w [dokumentacji interfejsu API aktora](https://msdn.microsoft.com/library/azure/dn971626.aspx)
