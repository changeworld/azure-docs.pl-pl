---
title: Tworzenie kopii zapasowych i przywracanie podmiotów sieci szkieletowej usług Azure
description: Dowiedz się, jak zaimplementować tworzenie kopii zapasowych i przywracanie w podmiotach sieci szkieletowej usług Azure.
author: vturecek
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vturecek
ms.openlocfilehash: 41ba3f9c7d362756b800005d0c140c23dd96caa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75370463"
---
# <a name="implement-reliable-actors-backup-and-restore"></a>Implementowanie kopii zapasowych i przywracania niezawodnych aktorów

> [!NOTE]
> Firma Microsoft zaleca użycie [okresowej kopii zapasowej i przywracania](service-fabric-backuprestoreservice-quickstart-azurecluster.md) do konfigurowania kopii zapasowej danych niezawodnych usług stanowych i wiarygodnych aktorów. 
> 

W poniższym przykładzie usługa aktora niestandardowego udostępnia metodę do utworzenia kopii zapasowej danych aktora, korzystając z odbiornika komunikacji zdalnej już obecnego w: `ActorService`

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

W tym `IMyActorService` przykładzie jest kontrakt komunikacji `IService` zdalnej, który `Service` implementuje (C#) `MyActorService`i (Java), a następnie jest implementowany przez . Dodając ten kontrakt komunikacji zdalnej, metody są `IMyActorService` teraz również dostępne dla klienta, `ActorServiceProxy`tworząc serwer proxy komunikacji zdalnej za pośrednictwem:

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

Aby uzyskać więcej informacji na temat wiarygodnych aktorów, przeczytaj następujące artykuły:
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
