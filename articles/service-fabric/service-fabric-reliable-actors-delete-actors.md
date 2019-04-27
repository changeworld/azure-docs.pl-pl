---
title: Usuwanie aktorów usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ręczne usunięcie elementów Reliable Actors usługi Service Fabric i ich stan.
services: service-fabric
documentationcenter: .net
author: amanbha
manager: chackdan
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: e297a6f42774f29e2eca4a410b695d5bbb636300
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60726608"
---
# <a name="delete-reliable-actors-and-their-state"></a>Usuwanie elementów Reliable Actors i ich stan
Wyrzucanie elementów bezużytecznych dezaktywowane aktorów tylko Czyści obiekt aktora, ale nie powoduje usunięcia danych, która jest przechowywana w Menedżer stanu aktora. Podczas ponownego uaktywniania Aktor jego dane ponownie stają się dostępne za pośrednictwem Menedżera stanu. W przypadku gdy aktorów przechowywanie danych w State Manager i są dezaktywowane, ale nigdy nie ponownie aktywować może być niezbędne do wyczyszczenia ich danych.

[Usługa aktora](service-fabric-reliable-actors-platform.md) udostępnia funkcję usuwania aktorów z zdalnego obiektu wywołującego:

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

Usuwanie Aktor ma następujące skutki w zależności od tego, czy jest obecnie aktywny aktora:

* **Aktywne aktora**
  * Aktor zostanie usunięty z listy aktywnych aktorów i dezaktywacji.
  * Jego stan jest trwale usunięte.
* **Nieaktywne aktora**
  * Jego stan jest trwale usunięte.

Nie można wywołać Aktor usunąć na siebie z jednego z jego metod aktora, ponieważ aktora nie można usunąć podczas wykonywania w kontekście wywołań aktora, w której środowisko uruchomieniowe uzyskał blokady wokół wywołania aktora i wymuszanie jednowątkowe dostępu.

Aby uzyskać więcej informacji na temat elementów Reliable Actors przeczytaj następujące:
* [Aktor czasomierze i przypomnienia](service-fabric-reliable-actors-timers-reminders.md)
* [Zdarzenia aktora](service-fabric-reliable-actors-events.md)
* [Wielobieżność aktora](service-fabric-reliable-actors-reentrancy.md)
* [Aktor Diagnostyka i monitorowanie wydajności](service-fabric-reliable-actors-diagnostics.md)
* [Dokumentacja interfejsu API aktora](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Kod przykładowy w języku C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Kod przykładowy w języku Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
