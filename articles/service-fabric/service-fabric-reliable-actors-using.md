---
title: Implementowanie funkcji w podmiotów sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak napisać własny usługa aktora, która implementuje funkcje na poziomie usługi taki sam sposób jak w przypadku dziedziczenia z klasy StatefulService.
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
ms.openlocfilehash: 41548c3395fa0c8f56e62cfcfb7338a2d53f040f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212895"
---
# <a name="implementing-service-level-features-in-your-actor-service"></a>Implementowanie funkcji poziomu usług w usłudze aktora
Zgodnie z opisem w [warstwy usługi](service-fabric-reliable-actors-platform.md#service-layering), sama usługa aktora jest niezawodnej usługi.  Można pisać własne usługi, która pochodzi z `ActorService` i implementowania funkcji poziomu usług w taki sam sposób jak podczas dziedziczenia z klasy StatefulService, takich jak:

- Usługa tworzenia kopii zapasowej i przywracania.
- Udostępnione funkcji dla wszystkich podmiotów, na przykład wyłącznika.
- Zdalne wywoływanie procedur w samej usługi aktora i na każdym poszczególnych aktora.

## <a name="using-the-actor-service"></a>Przy użyciu usługi aktora
Wystąpienia aktora mają dostęp do usługi aktora, w którym jest uruchomiony. Za pośrednictwem usługi aktora wystąpień aktora programowo może uzyskać kontekstu usługi. Kontekst usługi zawiera identyfikator partycji, nazwę usługi, nazwa aplikacji i inne informacje specyficzne dla platformy Service Fabric:

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```

Podobnie jak wszystkie niezawodnej usługi usługi aktora musi być zarejestrowany w środowisku uruchomieniowym usługi sieć szkieletowa typ usługi. Dla usługi aktora do uruchomienia wystąpień z aktorem danego typu aktora musi być zarejestrowany w usłudze aktora. Metoda rejestracji `ActorRuntime` wykonuje tę pracę dla aktorów. Ogólnie rzecz biorąc można zarejestrować tylko danego typu aktora i usługi aktora z ustawieniami domyślnymi niejawnie będą używane:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

Alternatywnie można użyć wyrażenia lambda udostępniane przez metodę rejestracji do utworzenia usługi aktora samodzielnie. Można następnie skonfigurować usługi aktora i utworzenia jawnie swoich wystąpień aktora, w którym można wstrzyknięcia zależności do Twojej aktora za pośrednictwem jej konstruktora:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="actor-service-methods"></a>Metody usługi aktora
Implementuje usługi aktora `IActorService` (C#) lub `ActorService` (języka Java), który z kolei implementuje `IService` (C#) lub `Service` (Java). Jest to interfejs używany przez niezawodnych usług zdalnych, co pozwala na metody usług zdalnych wywołań procedur. Zawiera metody poziomu usług, może zostać wywołana zdalnie za pomocą usług zdalnych, które umożliwiają [wyliczyć](service-fabric-reliable-actors-enumerate.md) i [usunąć](service-fabric-reliable-actors-delete-actors.md) złośliwych użytkowników.


## <a name="custom-actor-service"></a>Usługa aktora niestandardowych
Za pomocą aktora lambda rejestracji, możesz zarejestrować własnej usługi aktora niestandardowej, która pochodzi z `ActorService` (C#) i `FabricActorService` (Java). W tej usłudze aktora niestandardowych można zaimplementować własnej funkcji poziomu usług pisząc klasy usługi, która dziedziczy `ActorService` (C#) lub `FabricActorService` (Java). Usługi aktora niestandardowych dziedziczy wszystkie funkcje środowiska uruchomieniowego aktora z `ActorService` (C#) lub `FabricActorService` (Java) i może służyć do wykonania metody usługi.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="implementing-actor-backup-and-restore"></a>Implementowanie aktora kopii zapasowej i przywracania
Usługi aktora niestandardowe mogą uwidaczniać metody kopii zapasowej danych aktora dzięki wykorzystaniu odbiornika usługi zdalne znajduje się już w `ActorService`.  Na przykład zobacz [podmiotów kopii zapasowej i przywracania](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-using-remoting-v2-stack"></a>Przy użyciu usług zdalnych V2 stosu aktora
Przy użyciu 2,8 pakietu nuget użytkownicy mogą teraz używać usług zdalnych V2 stosu, w którym jest więcej wydajności i udostępnia funkcje, takie jak niestandardowej serializacji. V2 usług zdalnych nie jest zgodny z istniejącego stosu komunikacji zdalnej (wywołania teraz go jako stosu komunikacji zdalnej V1).

Następujące zmiany muszą korzystać z usług zdalnych stosu V2.
 1. Dodaj następujący atrybut zestawu w interfejsach aktora.
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
   ```

 2. Projekty kompilacji i uaktualniania ActorService aktora klienta i aby rozpocząć korzystanie z stosu V2.

### <a name="actor-service-upgrade-to-remoting-v2-stack-without-impacting-service-availability"></a>Aktora usługi uaktualnienie do komunikacji zdalnej V2 stosu bez wpływu na dostępność usługi.
Ta zmiana będzie uaktualnienie krok 2. Wykonaj kroki opisane w tej samej sekwencji wymienione.

1.  Dodaj następujący atrybut zestawu w interfejsach aktora. Ten atrybut zostanie uruchomione dwa odbiorniki dla ActorService, V1 (istniejące) i odbiornika V2. Uaktualnij ActorService z tą zmianą.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.CompatListener,RemotingClient = RemotingClient.V2Client)]
  ```

2. Uaktualnij ActorClients po zakończeniu uaktualniania powyżej.
Ten krok zapewnia, że serwer Proxy aktora używa stosu V2 komunikacji zdalnej.

3. Ten krok jest opcjonalny. Zmień atrybut powyżej, aby usunąć odbiornik V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
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
