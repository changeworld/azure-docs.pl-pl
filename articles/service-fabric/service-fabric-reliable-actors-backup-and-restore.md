---
title: Kopia zapasowa i przywracanie aktorów usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zaimplementować kopii zapasowej i przywracania w aktorów usługi Azure Service Fabric.
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
ms.date: 10/29/2018
ms.author: vturecek
ms.openlocfilehash: cb397141c86f40f02d8046838865106e0fb8992c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60726625"
---
# <a name="implement-reliable-actors-backup-and-restore"></a>Implementowanie Reliable Actors kopia zapasowa i przywracanie

> [!NOTE]
> Firma Microsoft zaleca, aby użyć [okresowych kopii zapasowych i przywracania](service-fabric-backuprestoreservice-quickstart-azurecluster.md) konfigurowania kopii zapasowej danych Reliable Stateful services i Reliable Actors. 
> 

W poniższym przykładzie Usługa aktora niestandardowy uwidacznia metodę, aby utworzyć kopię zapasową danych aktora, wykorzystując już istnieje na odbiornik komunikacji zdalnej `ActorService`:

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(PerformBackupAsync));
    }

    private async Task<bool> PerformBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           Directory.Delete(backupInfo.Directory, recursive: true);
        }
    }
}
```
```Java
public interface MyActorService extends Service
{
    CompletableFuture<?> backupActorsAsync();
}

class MyActorServiceImpl extends ActorService implements MyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<FabricActorService, ActorId, ActorBase> newActor)
    {
       super(context, typeInfo, newActor);
    }

    public CompletableFuture backupActorsAsync()
    {
        return this.backupAsync(new BackupDescription((backupInfo, cancellationToken) -> performBackupAsync(backupInfo, cancellationToken)));
    }

    private CompletableFuture<Boolean> performBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           deleteDirectory(backupInfo.Directory)
        }
    }

    void deleteDirectory(File file) {
        File[] contents = file.listFiles();
        if (contents != null) {
            for (File f : contents) {
               deleteDirectory(f);
             }
        }
        file.delete();
    }
}
```

W tym przykładzie `IMyActorService` jest umowa komunikacji zdalnej, która implementuje `IService` (C#) i `Service` (Java), a następnie jest implementowany przez `MyActorService`. Dodając ten kontrakt komunikacji zdalnej metody na `IMyActorService` są teraz również dostępne dla klienta, tworząc serwer proxy usług zdalnych za pośrednictwem `ActorServiceProxy`:

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```
```Java
MyActorService myActorServiceProxy = ActorServiceProxy.create(MyActorService.class,
    new URI("fabric:/MyApp/MyService"), actorId);

myActorServiceProxy.backupActorsAsync();
```

Aby uzyskać więcej informacji na temat elementów Reliable Actors przeczytaj następujące artykuły:
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
