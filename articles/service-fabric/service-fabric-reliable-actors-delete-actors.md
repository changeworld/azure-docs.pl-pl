---
title: Usuń sieć szkieletowa usług Azure podmiotów | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ręcznie usunąć Reliable Actors sieci szkieletowej usług i ich stan.
services: service-fabric
documentationcenter: .net
author: amanbha
manager: timlt
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: fa4fe018a9e6b32158f5bbd13c44ff57069cb1cf
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208339"
---
# <a name="delete-reliable-actors-and-their-state"></a>Usuń Reliable Actors i ich stan
Wyrzucanie elementów bezużytecznych dezaktywowane podmiotów tylko czyści obiektu aktora, ale nie powoduje usunięcia danych przechowywanych w Menedżerze stanu aktora. Po ponownym uaktywnieniu aktora jego danych jest ponownie udostępnione do niego za pośrednictwem Menedżera stanu. W przypadku gdy podmiotów przechowywania danych w Menedżerze stanu i są dezaktywowane ale nigdy nie ponownej aktywacji może być konieczne wyczyścić swoje dane.

[Usługi aktora](service-fabric-reliable-actors-platform.md) udostępnia funkcję usuwania złośliwych użytkowników z zdalnego procesu wywołującego:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

Usuwanie aktora ma następujące skutki w zależności od tego, czy aktora jest obecnie aktywna:

* **Aktywne aktora**
  * Aktora zostanie usunięty z listy active złośliwych użytkowników, a jest nieaktywne.
  * Jego stan jest trwale usunięte.
* **Nieaktywne aktora**
  * Jego stan jest trwale usunięte.

Nie można wywołać aktora usunąć na samym sobie z jednego z jego metody aktora, ponieważ aktora nie można usunąć podczas wykonywania w kontekście wywołań aktora, w którym środowiska uruchomieniowego uzyskał blokady wokół wymuszenia dostępu jednowątkowe wywołanie aktora.

Aby uzyskać więcej informacji o Reliable Actors, przeczytaj następujące artykuły:
* [Czasomierze aktora i przypomnieniami](service-fabric-reliable-actors-timers-reminders.md)
* [Zdarzenia aktora](service-fabric-reliable-actors-events.md)
* [Wielobieżność aktora](service-fabric-reliable-actors-reentrancy.md)
* [Aktora Diagnostyka i monitorowanie wydajności](service-fabric-reliable-actors-diagnostics.md)
* [Dokumentację referencyjną aktora interfejsu API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# przykładowy kod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java przykładowy kod](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
