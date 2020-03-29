---
title: Opracowywanie testów jednostkowych dla usług stanowych
description: Dowiedz się więcej o testowaniu jednostkowym w sieci szkieletowej usług Azure dla usług stanowych i specjalnych zagadnieniach, o które należy pamiętać podczas opracowywania.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 9c657bd8295d01a4e0fa4e44e969b33946684bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639840"
---
# <a name="create-unit-tests-for-stateful-services"></a>Tworzenie testów jednostkowych dla usług stanowych
Testowanie jednostek usługi sieci szkieletowej usługi stanowe ujawnia typowe błędy, które niekoniecznie zostaną przechwycone przez konwencjonalne aplikacji lub testowania jednostki specyficzne dla domeny. Podczas opracowywania testów jednostkowych dla usług stanowych, istnieją pewne szczególne kwestie, które powinny być zachowane w uwadze.

1. Każda replika wykonuje kod aplikacji, ale w innym kontekście. Jeśli usługa używa trzech replik, kod usługi jest wykonywany na trzech węzłach równolegle w ramach różnych kontekstu/roli.
2. Stan przechowywany w usłudze stanowej powinny być spójne wśród wszystkich replik. Menedżer stanu i niezawodne kolekcje zapewni tę spójność out-of-the-box. Jednak stan w pamięci będzie musiał być zarządzany przez kod aplikacji.
3. Każda replika zmieni role w pewnym momencie podczas uruchamiania w klastrze. Replika pomocnicza stanie się podstawową, w przypadku, gdy węzeł obsługujący podstawowy staje się niedostępny lub przeciążony. Jest to naturalne zachowanie dla sieci szkieletowej usług, dlatego usługi muszą planować ostatecznie wykonywanie w ramach innej roli.

W tym artykule przyjęto założenie, że [jednostka testowania usług stanowych w sieci szkieletowej usług](service-fabric-concepts-unit-testing.md) został odczytany.

## <a name="the-servicefabricmocks-library"></a>Biblioteka ServiceFabric.Mocks
W wersji 3.3.0, [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) udostępnia interfejs API do szydząc zarówno aranżacji replik i zarządzania stanem. Zostanie to wykorzystane w przykładach.

[Nuget](https://www.nuget.org/packages/ServiceFabric.Mocks/)
GitHub (Nuget[GitHub)](https://github.com/loekd/ServiceFabric.Mocks)

*Usługa ServiceFabric.Mocks nie jest własnością firmy Microsoft ani jej prowadzona. Jednak jest to obecnie zalecana biblioteka firmy Microsoft dla usług stanowych testowania jednostek.*

## <a name="set-up-the-mock-orchestration-and-state"></a>Konfigurowanie makiety aranżacji i stanu
W ramach rozmieszczenia część testu makiety zestaw replik i menedżer stanu zostaną utworzone. Zestaw replik będzie następnie właścicielem tworzenia wystąpienia testowanejednocnej dla każdej repliki. Będzie również właścicielem wykonywania zdarzeń cyklu `OnChangeRole` `RunAsync`życia, takich jak i . Menedżer stanu makiety zapewni, że wszystkie operacje wykonywane względem menedżera stanu są uruchamiane i przechowywane zgodnie z rzeczywistym menedżerem stanu.

1. Utwórz pełnomocnika fabryki usług, który spowoduje utworzenie wystąpienia testowanej usługi. Powinno to być podobne lub takie samo jak `Program.cs` wywołania zwrotnego fabryki usług zwykle znaleźć w dla usługi sieci szkieletowej usług lub aktora. Powinno to być następujące podpisy:
   ```csharp
   MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
   ```
2. Utwórz wystąpienie `MockReliableStateManager` klasy. Będzie to kpina wszystkich interakcji z menedżerem stanu.
3. Utwórz wystąpienie, `MockStatefulServiceReplicaSet<TStatefulService>` gdzie `TStatefulService` jest typem testowana usługa. Będzie to wymagało delegata utworzonego w #1 kroku i utworzonego w #2
4. Dodawanie replik do zestawu replik. Określ rolę (np. podstawową, ActiveSecondary, IdleSecondary) i identyfikator repliki
   > Trzymaj się identyfikatorów replik! Prawdopodobnie będą one używane podczas działania i twierdzą, że części testu jednostkowego.

```csharp
//service factory to instruct how to create the service instance
var serviceFactory = (StatefulServiceContext context, IReliableStateManagerReplica2 stateManager) => new MyStatefulService(context, stateManager);
//instantiate a new mock state manager
var stateManager = new MockReliableStateManager();
//instantiate a new replica set with the service factory and state manager
var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
//add a new Primary replica with id 1
await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
//add a new ActiveSecondary replica with id 2
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
//add a second ActiveSecondary replica with id 3
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);
```

## <a name="execute-service-requests"></a>Wykonywanie żądań serwisowych
Żądania usługi mogą być wykonywane na określonej repliki przy użyciu właściwości wygody i wyszukiwania.
```csharp
const string stateName = "test";
var payload = new Payload(StatePayload);

//execute a request on the primary replica using
await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);

//execute a request against replica with id 2
await replicaSet[2].ServiceInstance.InsertAsync(stateName, payload);

//execute a request against one of the active secondary replicas
await replicaSet.FirstActiveSecondary.InsertAsync(stateName, payload);
```

## <a name="execute-a-service-move"></a>Wykonywanie przeniesienia usługi
Zestaw repliki makiety udostępnia kilka metod wygody wyzwalania różnych typów przenoszenia usługi.
```csharp
//promote the first active secondary to primary
replicaSet.PromoteNewReplicaToPrimaryAsync();
//promote the secondary with replica id 4 to primary
replicaSet.PromoteNewReplicaToPrimaryAsync(4);

//promote the first idle secondary to an active secondary
PromoteIdleSecondaryToActiveSecondaryAsync();
//promote idle secondary with replica id 4 to active secondary
PromoteIdleSecondaryToActiveSecondaryAsync(4);

//add a new replica with randomly assigned replica id and promote it to primary
PromoteNewReplicaToPrimaryAsync()
//add a new replica with replica id 4 and promote it to primary
PromoteNewReplicaToPrimaryAsync(4)
```

## <a name="putting-it-all-together"></a>Zebranie wszystkich elementów
Poniższy test pokazuje konfigurowanie zestawu replik trzech węzłów i sprawdzanie, czy dane są dostępne z pomocniczego po zmianie roli. Typowym problemem, który może zostać `InsertAsync` złapany, jest to, że dane dodane `CommitAsync`podczas zostały zapisane w pamięci lub w niezawodnej kolekcji bez uruchamiania. W obu przypadkach pomocniczy będzie niezsynchronizowany z podstawowym. Prowadziłoby to do niespójnych odpowiedzi po przesuń usługi.

```csharp
[TestMethod]
public async Task TestServiceState_InMemoryState_PromoteActiveSecondary()
{
    var stateManager = new MockReliableStateManager();
    var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
    await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);

    const string stateName = "test";
    var payload = new Payload(StatePayload);

    //insert data
    await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);
    //promote one of the secondaries to primary
    await replicaSet.PromoteActiveSecondaryToPrimaryAsync(2);
    //get data
    var payloads = (await replicaSet.Primary.ServiceInstance.GetPayloadsAsync()).ToList();

    //data should match what was inserted against the primary
    Assert.IsTrue(payloads.Count == 1);
    Assert.IsTrue(payloads[0].Content == payload.Content);

    //verify the data was saved against the reliable dictionary
    var dictionary = await StateManager.GetOrAddAsync<IReliableDictionary<string, Payload>>(MyStatefulService.StateManagerDictionaryKey);
    using(var tx = StateManager.CreateTransaction())
    {
        var payload = await dictionary.TryGetValue(stateName);
        Assert.IsTrue(payload.HasValue);
        Assert.IsTrue(payload.Value.Content == payload.Content);
    }
}
```

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak testować [komunikację między usługami](service-fabric-testability-scenarios-service-communication.md) i [symulować błędy przy użyciu kontrolowanego chaosu.](service-fabric-controlled-chaos.md)
