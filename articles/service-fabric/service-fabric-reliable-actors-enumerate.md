---
title: Wyliczanie aktorów w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyliczanie elementów Reliable Actors i ich metadanych.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 04e2c32b18e6897d6443fea68587aba9ae294be5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60729141"
---
# <a name="enumerate-service-fabric-reliable-actors"></a>Wyliczanie elementów Reliable Actors usługi Service Fabric
Usługi Reliable Actors umożliwia klientowi wyliczanie metadanych dotyczących aktorów, które obsługuje usługę. Ponieważ usługa aktora jest podzielone na partycje usługi stanowej, wyliczenie odbywa się na partycję. Ponieważ każda partycja może zawierać wiele podmiotów, wyliczenia, jest zwracana jako zbiór stronicowane wyniki. Strony są zwracane przez zapoznaniem wszystkich stron. Poniższy przykład pokazuje, jak utworzyć listę wszystkich aktywnych podmiotów w jednej partycji usługi aktora:

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
* [Kolekcja aktora cykl życia i odzyskiwanie](service-fabric-reliable-actors-lifecycle.md)
* [Dokumentacja referencyjna interfejsu API aktorów](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Przykładowy kod .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykładowego kodu Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
