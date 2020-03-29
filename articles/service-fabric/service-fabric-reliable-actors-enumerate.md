---
title: Wyliczaj podmioty w sieci szkieletowej usług Azure
description: Dowiedz się więcej o wyliczeniu wiarygodnych aktorów i ich metadanych w aplikacji sieci szkieletowej usług Azure przy użyciu przykładów.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 1516c9005a7c4dd0adcb279e9954e5f882c575c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645603"
---
# <a name="enumerate-service-fabric-reliable-actors"></a>Wyliczaj wiarygodnych aktorów z sieci szkieletowej usług
Usługa Reliable Actors umożliwia klientowi wyliczać metadane dotyczące podmiotów, które usługa jest hostowana. Ponieważ usługa aktora jest usługą stanową podzieloną na partycje, wyliczenie jest wykonywane na partycję. Ponieważ każda partycja może zawierać wiele aktorów, wyliczenie jest zwracany jako zestaw wyników skonkonerch. Strony są zapętlone, dopóki wszystkie strony nie zostaną odczytane. W poniższym przykładzie pokazano, jak utworzyć listę wszystkich aktywnych aktorów w jednej partycji usługi aktora:

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



## <a name="next-steps"></a>Następne kroki
* [Zarządzanie stanem aktora](service-fabric-reliable-actors-state-management.md)
* [Cykl życia aktora i wyrzucanie elementów bezużytecznych](service-fabric-reliable-actors-lifecycle.md)
* [Dokumentacja referencyjna interfejsu API podmiotów](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Przykładowy kod platformy .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykładowy kod java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
