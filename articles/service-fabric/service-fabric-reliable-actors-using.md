---
title: Implementowanie funkcji w podmiotach sieci szkieletowej usług Azure
description: W tym artykule opisano sposób pisania własnej usługi aktora, który implementuje funkcje na poziomie usługi w taki sam sposób, jak po dziedziczeniu StatefulService.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 55ee4c7498dcda3060d4e4221711793b80132bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502288"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>Implementowanie funkcji na poziomie usług w usłudze aktora

Jak opisano w [warstwach usługi,](service-fabric-reliable-actors-platform.md#service-layering)sama usługa aktora jest niezawodną usługą. Możesz napisać własną usługę, która `ActorService`wywodzi się z . Można również zaimplementować funkcje na poziomie usługi w taki sam sposób, jak po dziedziczeniu usługi stanowej, takich jak:

- Kopia zapasowa i przywracanie usługi.
- Udostępnione funkcje dla wszystkich podmiotów, na przykład wyłącznik.
- Procedura zdalna wywołuje samą usługę aktora i każdego aktora.

## <a name="use-the-actor-service"></a>Korzystanie z usługi aktora

Wystąpienia aktora mają dostęp do usługi aktora, w której są uruchomione. Za pośrednictwem usługi aktora wystąpienia aktora można programowo uzyskać kontekst usługi. Kontekst usługi ma identyfikator partycji, nazwę usługi, nazwę aplikacji i inne informacje specyficzne dla platformy sieci szkieletowej usługi Azure.

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

Podobnie jak wszystkie usługi niezawodne, usługa aktora musi być zarejestrowana przy typie usługi w czasie wykonywania sieci szkieletowej usług. Aby usługa aktora uruchamiała wystąpienia aktora, typ aktora również musi być zarejestrowany w usłudze aktora. Metoda rejestracji `ActorRuntime` wykonuje tę pracę dla aktorów. W najprostszym przypadku można zarejestrować typ aktora, a usługa aktora następnie używa ustawień domyślnych.

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

Alternatywnie można użyć lambda dostarczone przez metodę rejestracji do samodzielnego konstruowania usługi aktora. Następnie można skonfigurować usługę aktora i jawnie konstruować wystąpienia aktora. Można wstrzyknąć zależności do aktora za pośrednictwem jego konstruktora.

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

## <a name="actor-service-methods"></a>Metody obsługi aktora

Usługa aktora `IActorService` implementuje (C#) lub `ActorService` (Java), `IService` który z kolei `Service` implementuje (C#) lub (Java). Ten interfejs jest używany przez program komunikacji zdalnej reliable services, który umożliwia zdalne wywołanie procedury na metody usługi. Zawiera metody na poziomie usług, które można wywołać zdalnie za pośrednictwem komunikacji zdalnej usługi. Można go używać do [wyliczanie](service-fabric-reliable-actors-enumerate.md) i [usuwanie](service-fabric-reliable-actors-delete-actors.md) aktorów.


## <a name="custom-actor-service"></a>Niestandardowa usługa aktora

Za pomocą lambda rejestracji aktora, można zarejestrować własną usługę `ActorService` aktora niestandardowego, który pochodzi z (C#) i `FabricActorService` (Java). Następnie można zaimplementować własne funkcje na poziomie `ActorService` usługi, pisząc `FabricActorService` klasę usługi, która dziedziczy (C#) lub (Java). Niestandardowa usługa aktora dziedziczy wszystkie funkcje `ActorService` środowiska wykonawczego `FabricActorService` aktora z (C#) lub (Java). Może służyć do implementowania własnych metod usługi.

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

## <a name="implement-actor-backup-and-restore"></a>Wdrażanie kopii zapasowej i przywracania aktora

Niestandardowa usługa aktora może udostępnić metodę do utworzenia kopii zapasowej danych aktora, korzystając z odbiornika komunikacji zdalnej już obecnego w pliku `ActorService`. Na przykład zobacz [Tworzenie kopii zapasowych i przywracanie aktorów](../synapse-analytics/sql-data-warehouse/backup-and-restore.md).

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>Aktor korzystający ze stosu komunikacji zdalnej V2 (zgodnej z interfejsem)

Stos komunikacji zdalnej V2 (kompatybilny z interfejsem, znany jako V2_1) ma wszystkie funkcje stosu komunikacji zdalnej V2. Jego interfejs jest kompatybilny ze stosem zdalnym V1, ale nie jest wstecznie kompatybilny z V2 i V1. Aby uaktualnić z wersji 1 do V2_1 bez wpływu na dostępność usługi, wykonaj kroki opisane w następnej sekcji.

Następujące zmiany są wymagane do korzystania z V2_1 stosu komunikacji zdalnej:

1. Dodaj następujący atrybut zestawu w interfejsach aktora.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Tworzenie i uaktualnianie usługi aktora i projektów klienta aktora, aby rozpocząć korzystanie ze stosu V2.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>Uaktualnienie usługi aktora do stosu zdalnego formatu V2 (zgodnego z interfejsem) bez wpływu na dostępność usługi

Ta zmiana jest dwuetapowym uaktualnieniem. Wykonaj kroki opisane w tej kolejności.

1. Dodaj następujący atrybut zestawu w interfejsach aktora. Ten atrybut uruchamia dwa odbiorniki dla usługi aktora, V1 (istniejące) i V2_1 odbiornika. Uaktualnienie usługi aktora za pomocą tej zmiany.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Uaktualnij klientów aktora po zakończeniu poprzedniego uaktualnienia.
   Ten krok zapewnia, że serwer proxy aktora używa stosu V2_1 komunikacji zdalnej.

3. Ten krok jest opcjonalny. Zmień poprzedni atrybut, aby usunąć odbiornik V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>Aktor korzystający ze stosu komunikacji zdalnej V2

Z pakietem NuGet w wersji 2.8 użytkownicy mogą teraz używać stosu zdalnego systemu V2, który działa lepiej i zapewnia funkcje, takie jak serializacja niestandardowa. Komunikacji zdalnej V2 nie jest wstecznie kompatybilny z istniejącym stosem komunikacji zdalnej (obecnie nazywanym stosem komunikacji zdalnej V1).

Następujące zmiany są wymagane do korzystania z remoting V2 stosu.

1. Dodaj następujący atrybut zestawu w interfejsach aktora.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Tworzenie i uaktualnianie projektów klienta usługi aktora i aktora, aby rozpocząć korzystanie ze stosu V2.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>Uaktualnienie usługi aktora do stosu komunikacji zdalnej V2 bez wpływu na dostępność usługi

Ta zmiana jest dwuetapowym uaktualnieniem. Wykonaj kroki opisane w tej kolejności.

1. Dodaj następujący atrybut zestawu w interfejsach aktora. Ten atrybut uruchamia dwa odbiorniki dla usługi aktora, V1 (istniejące) i odbiornika V2. Uaktualnienie usługi aktora za pomocą tej zmiany.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Uaktualnij klientów aktora po zakończeniu poprzedniego uaktualnienia.
   Ten krok zapewnia, że serwer proxy aktora używa stosu komunikacji zdalnej V2.

3. Ten krok jest opcjonalny. Zmień poprzedni atrybut, aby usunąć odbiornik V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
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
