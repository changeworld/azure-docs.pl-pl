---
title: Usuwanie podmiotów sieci szkieletowej usług Azure
description: Dowiedz się, jak ręcznie i w pełni usunąć niezawodne podmioty i ich stan w aplikacji sieci szkieletowej usług Azure.
author: amanbha
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: b90c5a10c64e273f1c8f48c7bf5713859796db65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645620"
---
# <a name="delete-reliable-actors-and-their-state"></a>Usuń usługi Reliable Actors i jej stanu
Wyrzucanie elementów bezużytecznych dezaktywowanych aktorów tylko czyści obiekt aktora, ale nie usuwa danych, które są przechowywane w menedżerze stanu aktora. Gdy aktor jest reaktywowany, jego dane są ponownie udostępniane za pośrednictwem Menedżera stanu. W przypadkach, gdy podmioty przechowują dane w Menedżerze stanu i są dezaktywowane, ale nigdy nie reaktywowane, może być konieczne wyczyszczone ich dane.

[Usługa aktora](service-fabric-reliable-actors-platform.md) udostępnia funkcję usuwania aktorów ze zdalnego wywoływacza:

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

Usunięcie aktora ma następujące efekty w zależności od tego, czy aktor jest obecnie aktywny:

* **Aktywny aktor**
  * Aktor jest usuwany z listy aktywnych aktorów i jest dezaktywowany.
  * Jego stan jest usuwany na stałe.
* **Nieaktywny aktor**
  * Jego stan jest usuwany na stałe.

Aktor nie może wywołać delete na siebie z jednej z jego metod aktora, ponieważ aktora nie można usunąć podczas wykonywania w kontekście wywołania aktora, w którym środowisko wykonawcze uzyskał blokadę wokół wywołania aktora w celu wymuszenia dostępu jednowątkowego.

Aby uzyskać więcej informacji na temat wiarygodnych aktorów, przeczytaj następujące informacje:
* [Czasomierze aktorów i przypomnienia](service-fabric-reliable-actors-timers-reminders.md)
* [Wydarzenia aktorskie](service-fabric-reliable-actors-events.md)
* [Ponowne entrancy aktora](service-fabric-reliable-actors-reentrancy.md)
* [Diagnostyka aktora i monitorowanie wydajności](service-fabric-reliable-actors-diagnostics.md)
* [Dokumentacja referencyjna interfejsu API aktora](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# Przykładowy kod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykładowy kod Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
