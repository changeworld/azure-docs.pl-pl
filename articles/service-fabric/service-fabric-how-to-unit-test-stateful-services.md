---
title: Tworzenie testów jednostkowych dla usług stanowych w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć jednostki testowe dla usług stanowych w usłudze Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/04/2018
ms.author: atsenthi
ms.openlocfilehash: b066296ca52d3067f8985245161eb4fa7b484a07
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669116"
---
# <a name="create-unit-tests-for-stateful-services"></a>Tworzenie testów jednostkowych dla usług stanowych
Jednostki testowania usług stanowych usługi Service Fabric udostępnia typowych błędów, które będą nie musi być przechwycony przez konwencjonalne aplikacji lub testy jednostkowe specyficznego dla domeny. Podczas tworzenia testów jednostkowych dla usług stanowych, istnieją pewne specjalne zagadnienia, które powinny być przechowywane w uwadze.

1. Każda replika wykonuje kod aplikacji, ale w innym kontekście. Jeśli usługa używa trzech replik, trzy węzły równolegle w ramach innego kontekstu/roli jest wykonywany kod usługi.
2. Stan przechowywane w ramach usługi stanowej powinna być spójna wśród wszystkich replik. Zapewnia to spójność out-of--box state manager i elementów reliable collections. Jednak stan w pamięci, należy być zarządzane przez kod aplikacji.
3. Każda replika ulegnie zmianie role w pewnym momencie podczas uruchamiania w klastrze. Replika pomocnicza stanie się podstawową, w przypadku, gdy węzeł, w którym podstawowy staje się niedostępna lub przeciążona. Jest to naturalny zachowanie dla usługi Service Fabric, w związku z tym usługi należy zaplanować po pewnym czasie wykonywania przy użyciu innej roli.

W tym artykule założono, że [Unit testing usług stanowych w usłudze Service Fabric](service-fabric-concepts-unit-testing.md) został odczytany.

## <a name="the-servicefabricmocks-library"></a>Biblioteka ServiceFabric.Mocks
Począwszy od wersji 3.3.0 [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) zapewnia interfejs API dla pozorowanie organizowania repliki i zarządzania stanem. To posłuży w przykładach.

[Nuget](https://www.nuget.org/packages/ServiceFabric.Mocks/)
[GitHub](https://github.com/loekd/ServiceFabric.Mocks)

*ServiceFabric.Mocks nie jest właścicielem lub obsługiwane przez firmę Microsoft. Jednak ta funkcja jest obecnie zalecane biblioteki dla usług stanowych testów jednostkowych firmy Microsoft.*

## <a name="set-up-the-mock-orchestration-and-state"></a>Konfigurowanie makiety aranżacji i stanu
W ramach części Rozmieść testu zestawu replik makiety i Menedżera stanu, który zostanie utworzony. Następnie zestawu replik będą właścicielami, tworzenia wystąpienia usługi przetestowane dla każdej repliki. On również właścicielem wykonywanie zdarzenia cyklu życia takich jak `OnChangeRole` i `RunAsync`. Menedżer stanów makiety zapewni dowolne operacje wykonywane względem przez menedżera stanu są uruchamiania i przechowywane, tak jak Menedżer rzeczywistego stanu.

1. Utwórz delegata fabryki usługi, który będzie wystąpienia usługi poddawana testom. Powinno to być ta sama lub podobne jak wywołania zwrotnego fabryki usługi zwykle znajdują się w `Program.cs` dla usługi Service Fabric lub aktora. Należy to wykonać następujący podpis:
   ```csharp
   MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
   ```
2. Utwórz wystąpienie obiektu `MockReliableStateManager` klasy. Spowoduje to testowanie wszystkie interakcje z przez menedżera stanu.
3. Utwórz wystąpienie obiektu `MockStatefulServiceReplicaSet<TStatefulService>` gdzie `TStatefulService` jest typ usługi, testowany. Wymaga to delegata utworzonego w kroku #1 i Menedżer stanu wystąpienia w #2
4. Dodawanie replik do zestawu replik. Określ rolę (na przykład podstawowego, ActiveSecondary, IdleSecondary) i identyfikator repliki
   > Przytrzymaj repliki identyfikatorów! Te będą prawdopodobnie używane podczas czynność i asercja części testu jednostkowego.

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

## <a name="execute-service-requests"></a>Wykonywanie żądania obsługi
Żądania obsługi mogą być wykonywane na replice określone przy użyciu właściwości jako udogodnienie i wyszukiwania.
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

## <a name="execute-a-service-move"></a>Wykonaj przejście usługi
Zestawu replik makiety udostępnia kilka metod wygody do wyzwolenia różnego rodzaju przenosi usługi.
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
Następującego testu pokazuje Konfigurowanie zestawu replik trzema węzłami i weryfikowanie, czy dane są dostępne z pomocniczego, po zmianie roli. Jest to typowy problem, to może wychwycić, jeśli dane są dodawane podczas `InsertAsync` została zapisana coś w pamięci lub reliable collection bez uruchamiania `CommitAsync`. W obu przypadkach pomocniczej będzie zsynchronizowany z podstawowego. Może to prowadzić do niezgodne odpowiedzi po przenosi usługa.

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

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak przetestować [komunikacji między usługami](service-fabric-testability-scenarios-service-communication.md) i [symulowanie błędów przy użyciu kontrolowanego chaosu](service-fabric-controlled-chaos.md).
