---
title: Implementuje funkcje w aktorów usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak napisać własne usługi aktora, który implementuje funkcje na poziomie usługi w taki sam sposób jak przy dziedziczeniu z klasy StatefulService.
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
ms.openlocfilehash: 57894770ad9d27430d5803c9a93ce6973355878a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123250"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>Implementuje funkcje na poziomie usługi w usłudze aktora

Zgodnie z opisem w [warstw usługi](service-fabric-reliable-actors-platform.md#service-layering), sama usługa aktora jest niezawodnej usługi. Można napisać własne usługa, która jest pochodną `ActorService`. Można również wdrożyć funkcje na poziomie usługi w taki sam sposób jak podczas dziedziczą stanowej usługi, takie jak:

- Usługa tworzenia kopii zapasowych i przywracania.
- Udostępnione funkcje dla wszystkich podmiotów, na przykład wyłącznika.
- Zdalne wywołania procedur na samą usługę aktora i każdego poszczególnych aktora.

## <a name="use-the-actor-service"></a>Usługa aktora

Wystąpienia aktora mają dostęp do usługi aktora, w którym jest uruchomiony. Wystąpienia aktora programowo uzyskać kontekst usługi za pośrednictwem usługi aktora. Kontekst usługi ma Identyfikatora partycji, nazwę usługi, nazwa aplikacji i innych informacji specyficznych dla platformy Azure Service Fabric.

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

Podobnie jak wszystkie usług Reliable Services usługa aktora należy zarejestrować typ usługi, w środowisku uruchomieniowym usługi Service Fabric. Usługa aktora uruchamiała wystąpienia aktora, aby uzyskać swój typ aktora również musi być zarejestrowany w usłudze aktora. Metoda rejestracji `ActorRuntime` wykonuje tę pracę dla aktorów. W najprostszym przypadku można zarejestrować Twój typ aktora, a następnie usługa aktora używa ustawień domyślnych.

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

Alternatywnie można użyć wyrażenia lambda dostarczone przez metodę rejestracji do konstruowania usługa aktora, samodzielnie. Następnie można skonfigurować usługi aktora i jednoznacznego skonstruowania wystąpienia aktora. Należy wstrzyknąć zależności, aby usługi aktora za pośrednictwem jej konstruktora.

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

Implementuje usługi aktora `IActorService` (C#) lub `ActorService` (Java), który z kolei implementuje `IService` (C#) lub `Service` (Java). Ten interfejs jest wykorzystywany przez zdalnych usług Reliable Services, który umożliwia zdalne wywołania procedur na metody usługi. Zawiera ona metody poziomu usług, które może być wywoływany zdalnie za pośrednictwem zdalna komunikacja usług. Możesz użyć go do [wyliczyć](service-fabric-reliable-actors-enumerate.md) i [Usuń](service-fabric-reliable-actors-delete-actors.md) aktorów.


## <a name="custom-actor-service"></a>Usługa aktora niestandardowe

Za pomocą wyrażenia lambda rejestracji aktora, można rejestrować własnej usługi aktora niestandardowych, która pochodzi od klasy `ActorService` (C#) i `FabricActorService` (Java). Następnie można wdrożyć własne funkcje poziomu usług, pisząc klasy usługi, która dziedziczy `ActorService` (C#) lub `FabricActorService` (Java). Usługa aktora niestandardowe dziedziczy wszystkie funkcje środowiska uruchomieniowego aktora z `ActorService` (C#) lub `FabricActorService` (Java). Może służyć do implementowania metod usługi.

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

## <a name="implement-actor-backup-and-restore"></a>Implementowanie aktora w kopii zapasowej i przywracania

Usługa aktora niestandardowe mogą uwidocznić metodę, aby utworzyć kopię zapasową danych aktora, wykorzystując już istnieje na odbiornik komunikacji zdalnej `ActorService`. Aby uzyskać przykład, zobacz [kopii zapasowej i przywracanie aktorów](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>Aktora, który używa stosu komunikacji zdalnej w wersji 2 (zgodny z interfejsu)

Komunikacja zdalna V2 (interfejs zgodne, znane jako V2_1) stos zawiera wszystkie funkcje w wersji 2 stosu komunikacji zdalnej. Interfejs jest zgodny ze stosem V1 komunikacji zdalnej, ale nie jest zgodne z poprzednimi wersjami z wersji 2 i w wersji 1. Uaktualnienie z wersji 1, do V2_1 z bez wpływu na dostępność usługi, wykonaj kroki opisane w następnej sekcji.

Następujące zmiany są wymagane do korzystania ze stosu V2_1 komunikacji zdalnej:

1. Dodaj następujący atrybut w zestawie na interfejsów aktora.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Twórz i przeprowadź uaktualnienie usługi aktora i projektów klienckich aktora, aby rozpocząć korzystanie z stack w wersji 2.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>Uaktualnianie usługi aktora do komunikacji zdalnej w wersji 2 (zgodny z interfejsu) stosu, bez wywierania wpływu na dostępność usługi

Ta zmiana ma dwuetapową uaktualnienie. Wykonaj kroki opisane w tej sekwencji.

1. Dodaj następujący atrybut w zestawie na interfejsów aktora. Ten atrybut rozpoczyna się dwa odbiorniki usługi aktora, wersja 1 (istniejące) i odbiornika V2_1. Uaktualnij usługę aktora dzięki tej zmianie.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Uaktualnij klientów aktora, po ukończeniu poprzedniego uaktualnienia.
   Ten krok pozwala się upewnić, że proxy aktora używa stosu komunikacji zdalnej V2_1.

3. Ten krok jest opcjonalny. Zmień atrybut poprzedniej, aby usunąć odbiornik V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>Aktora, który używa stosu komunikacji zdalnej w wersji 2

Przy użyciu pakietu NuGet w wersji 2.8 użytkownicy mogą teraz używać stosu V2 wywołaniem funkcji zdalnych, w którym działa lepiej i udostępnia funkcje, takie jak niestandardowej serializacji. Wersji 2 zdalnej komunikacji nie jest zgodne z poprzednimi wersjami z istniejącego stosu komunikacji zdalnej (obecnie nazywanego stosu komunikacji zdalnej w wersji 1).

Następujące zmiany są wymagane do korzystania ze stosu V2 komunikacji zdalnej.

1. Dodaj następujący atrybut w zestawie na interfejsów aktora.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Twórz i uaktualnianie usługi aktora i projektów klienckich aktora, aby rozpocząć korzystanie z stack w wersji 2.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>Uaktualnij usługę aktora stos komunikacji zdalnej V2 bez wywierania wpływu na dostępność usługi

Ta zmiana ma dwuetapową uaktualnienie. Wykonaj kroki opisane w tej sekwencji.

1. Dodaj następujący atrybut w zestawie na interfejsów aktora. Ten atrybut rozpoczyna się dwa odbiorniki usługi aktora, wersja 1 (istniejące) i odbiornika V2. Uaktualnij usługę aktora dzięki tej zmianie.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Uaktualnij klientów aktora, po ukończeniu poprzedniego uaktualnienia.
   Ten krok pozwala się upewnić, że proxy aktora używa stosu komunikacji zdalnej w wersji 2.

3. Ten krok jest opcjonalny. Zmień atrybut poprzedniej, aby usunąć odbiornik V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
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
