---
title: Wyliczanie złośliwych użytkowników na sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyliczyć Reliable Actors i ich metadanych.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: d5d6ac87db18815aa945d6964338626365b08e64
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="enumerate-service-fabric-reliable-actors"></a>Wyliczanie Reliable Actors sieci szkieletowej usług
Usługa Reliable Actors umożliwia klientowi wyliczyć metadane dotyczące osób, które obsługuje usługę. Ponieważ usługa aktora jest partycjonowany usługi stanowej, wyliczenie jest wykonywane dla każdej partycji. Ponieważ każda partycja może zawierać wiele osób, wyliczenia jest zwracana jako zestaw wyników stronicowania. Strony są zwracane przez zapoznaniem wszystkich stron. Poniższy przykład przedstawia sposób tworzenia listy wszystkich podmiotów active w jednej partycji usługi aktora:

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```



## <a name="next-steps"></a>Kolejne kroki
* [Zarządzanie stanem aktora](service-fabric-reliable-actors-state-management.md)
* [Aktora cykl życia i odzyskiwanie pamięci](service-fabric-reliable-actors-lifecycle.md)
* [Dokumentacji interfejsu API złośliwych użytkowników](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET przykładowy kod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java przykładowy kod](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
