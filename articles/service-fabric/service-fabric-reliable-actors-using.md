---
title: Implementowanie funkcji w aktorów usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak napisać własne usługi aktora, który implementuje funkcje na poziomie usługi w taki sam sposób jak w przypadku dziedziczenia z klasy StatefulService.
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
ms.openlocfilehash: 6aff9e9599d31942f994f3cb4e5e9219f33dc7e1
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205524"
---
# <a name="implementing-service-level-features-in-your-actor-service"></a>Implementowanie funkcji na poziomie usługi w usłudze aktora
Zgodnie z opisem w [warstw usługi](service-fabric-reliable-actors-platform.md#service-layering), sama usługa aktora jest niezawodnej usługi.  Można napisać własne usługa, która jest pochodną `ActorService` i zaimplementuj funkcje na poziomie usługi tak samo jak w przypadku dziedziczenia z klasy StatefulService, takich jak:

- Usługa tworzenia kopii zapasowych i przywracania.
- Udostępnione funkcje dla wszystkich podmiotów, na przykład wyłącznika.
- Zdalne wywołania procedur na samą usługę aktora i każdego poszczególnych aktora.

## <a name="using-the-actor-service"></a>Przy użyciu usługi aktora
Wystąpienia aktora mają dostęp do usługi aktora, w którym jest uruchomiony. Wystąpienia aktora programowo uzyskać kontekst usługi za pośrednictwem usługi aktora. Kontekst usługi ma Identyfikatora partycji, nazwę usługi, nazwa aplikacji i innych informacji specyficznych dla platformy Service Fabric:

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

Podobnie jak wszystkie usług Reliable Services usługa aktora należy zarejestrować typ usługi, w środowisku uruchomieniowym usługi Service Fabric. Usługa aktora uruchamiała wystąpienia aktora Twój typ aktora należy również zarejestrować przy użyciu usługi aktora. Metoda rejestracji `ActorRuntime` wykonuje tę pracę dla aktorów. W najprostszym przypadku możesz po prostu zarejestrować Twój typ aktora i niejawnie zostanie użyta usługa aktora, z ustawieniami domyślnymi:

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

Alternatywnie można użyć wyrażenia lambda dostarczone przez metodę rejestracji do konstruowania usługa aktora, samodzielnie. Można następnie skonfigurować usługę aktora i jednoznacznego skonstruowania wystąpienia aktora, w którym należy wstrzyknąć zależności do usługi aktora za pośrednictwem jej konstruktora:

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
Implementuje usługi aktora `IActorService` (C#) lub `ActorService` (Java), który z kolei implementuje `IService` (C#) lub `Service` (Java). Jest to interfejs używany przez zdalną usług Reliable Services umożliwia zdalne wywołania procedur na metody usługi. Zawiera metody poziomu usług, które może być wywoływany zdalnie za pomocą komunikacji zdalnej usługi i pozwalają na [wyliczyć](service-fabric-reliable-actors-enumerate.md) i [Usuń](service-fabric-reliable-actors-delete-actors.md) aktorów.


## <a name="custom-actor-service"></a>Usługa aktora niestandardowe
Za pomocą wyrażenia lambda rejestracji aktora, można rejestrować własnej usługi aktora niestandardowych, która pochodzi od klasy `ActorService` (C#) i `FabricActorService` (Java). W tej usłudze aktora niestandardowe można zaimplementować własnej funkcji poziomu usług, pisząc klasy usługi, która dziedziczy `ActorService` (C#) lub `FabricActorService` (Java). Usługa aktora niestandardowe dziedziczy wszystkie funkcje środowiska uruchomieniowego aktora z `ActorService` (C#) lub `FabricActorService` (Java) i może służyć do implementowania metod usługi.

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

## <a name="implementing-actor-backup-and-restore"></a>Implementowanie i przywracania kopii zapasowych aktora
Usługa aktora niestandardowe mogą uwidocznić metodę, aby utworzyć kopię zapasową danych aktora, wykorzystując już istnieje na odbiornik komunikacji zdalnej `ActorService`.  Aby uzyskać przykład, zobacz [kopii zapasowej i przywracanie aktorów](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-using-remoting-v2interfacecompatible-stack"></a>Za pomocą komunikacji zdalnej V2(InterfaceCompatible) stosu aktora
Wersji 2 zdalnej komunikacji stosu (InterfaceCompatible alias V2_1) zawiera wszystkie funkcje usług zdalnych V2 stosu oprócz interfejsu zgodne stosu do stosu komunikacji zdalnej w wersji 1, ale nie jest zgodny z w wersji 2 i w wersji 1. Aby wykonać uaktualnienie z wersji 1 V2_1 bez wywierania wpływu na dostępność usługi, wykonaj poniższe [artykułu](#actor-service-upgrade-to-remoting-v2interfacecompatible-stack-without-impacting-service-availability).

Następujące zmiany są wymagane do korzystania ze stosu V2_1 komunikacji zdalnej.
 1. Dodaj następujący atrybut w zestawie na interfejsów aktora.
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

 2. Projekty kompilacji i uaktualniania ActorService aktora klienta i można uruchomić za pomocą stosu w wersji 2.

#### <a name="actor-service-upgrade-to-remoting-v2interfacecompatible-stack-without-impacting-service-availability"></a>Aktor usługi przeprowadzić uaktualnienie do komunikacji zdalnej V2(InterfaceCompatible) stosu, bez wywierania wpływu na dostępność usług.
Ta zmiana będzie uaktualnienie krok 2. Wykonaj kroki opisane w tej samej sekwencji zgodnie z opisem.

1.  Dodaj następujący atrybut w zestawie na interfejsów aktora. Ten atrybut zostanie uruchomione dwóch detektorów dla ActorService, wersja 1 (istniejące) i V2_1 odbiornika. Uaktualnij ActorService dzięki tej zmianie.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
  ```

2. Uaktualnij ActorClients po zakończeniu uaktualniania powyżej.
Ten krok zapewnia, że serwer Proxy aktora używa stosu V2_1 komunikacji zdalnej.

3. Ten krok jest opcjonalny. Zmień atrybut powyżej, aby usunąć odbiornik V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-using-remoting-v2-stack"></a>Za pomocą komunikacji zdalnej V2 stosu aktora
Przy użyciu pakietu nuget 2,8 użytkownicy mogą teraz używać wersji 2 zdalnej komunikacji stosu, w którym jest wydajniej i udostępnia funkcje, takie jak niestandardowej serializacji. Wersji 2 zdalnej komunikacji nie jest zgodne z poprzednimi wersjami z istniejącego stosu komunikacji zdalnej (dzwonimy teraz go jako stosu komunikacji zdalnej w wersji 1).

Następujące zmiany są wymagane do korzystania ze stosu V2 komunikacji zdalnej.
 1. Dodaj następujący atrybut w zestawie na interfejsów aktora.
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

 2. Projekty kompilacji i uaktualniania ActorService aktora klienta i można uruchomić za pomocą stosu w wersji 2.

#### <a name="actor-service-upgrade-to-remoting-v2-stack-without-impacting-service-availability"></a>Aktor usługi przeprowadzić uaktualnienie do komunikacji zdalnej V2 stosu, bez wywierania wpływu na dostępność usług.
Ta zmiana będzie uaktualnienie krok 2. Wykonaj kroki opisane w tej samej sekwencji zgodnie z opisem.

1.  Dodaj następujący atrybut w zestawie na interfejsów aktora. Ten atrybut zostanie uruchomione dwóch detektorów dla ActorService, wersja 1 (istniejące) i odbiornika V2. Uaktualnij ActorService dzięki tej zmianie.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
  ```

2. Uaktualnij ActorClients po zakończeniu uaktualniania powyżej.
Ten krok zapewnia, że serwer Proxy aktora używa stosu V2 komunikacji zdalnej.

3. Ten krok jest opcjonalny. Zmień atrybut powyżej, aby usunąć odbiornik V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
    ```

## <a name="next-steps"></a>Kolejne kroki
* [Zarządzanie stanem aktora](service-fabric-reliable-actors-state-management.md)
* [Kolekcja aktora cykl życia i odzyskiwanie](service-fabric-reliable-actors-lifecycle.md)
* [Dokumentacja referencyjna interfejsu API aktorów](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Przykładowy kod .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykładowego kodu Java](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
