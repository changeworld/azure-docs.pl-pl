---
title: Usługa Azure Service Fabric automatyczne skalowanie usług i kontenerów | Dokumentacja firmy Microsoft
description: Usługa Azure Service Fabric umożliwia ustawienie automatycznego skalowania zasad dla usług i kontenerów.
services: service-fabric
documentationcenter: .net
author: radicmilos
manager: ''
editor: nipuzovi
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2018
ms.author: miradic
ms.openlocfilehash: 8e57c071c9fd93a8581d574aeec2b23b38b3ab95
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60844027"
---
# <a name="introduction-to-auto-scaling"></a>Wprowadzenie do automatycznego skalowania
Automatyczne skalowanie jest dodatkowe możliwości usługi Service Fabric dynamicznie skalować swoje usługi, w oparciu o obciążenie, które usługi są raportowania lub na podstawie ich użycia zasobów. Automatyczne skalowanie zapewnia dużą elastyczność i umożliwia inicjowanie obsługi administracyjnej dodatkowych wystąpień lub partycje usługi na żądanie. Cały automatycznego skalowania procesu jest zautomatyzowanych i przejrzystości, a po skonfigurowaniu zasad w usłudze nie ma potrzeby ręcznego operacji skalowania na poziomie usługi. Automatyczne skalowanie może zostać włączona podczas tworzenia usługi lub w dowolnej chwili, aktualizując usługę.

Typowy scenariusz, w których jest użyteczny automatyczne skalowanie jest, gdy obciążenie określonej usługi zmienia się wraz z upływem czasu. Na przykład usługi takie jak bramy można skalować na podstawie ilości zasobów potrzebnych do obsługi żądań przychodzących. Spójrzmy na przykład jak może wyglądać te reguły skalowania:
* Jeśli wszystkie wystąpienia elementu mojej bramy średnio z więcej niż dwa rdzenie, skalować usługi bramy w poziomie przez dodanie jednego wystąpienia więcej. To co godzinę, ale nigdy nie mają więcej niż siedem wystąpień w sumie.
* Jeśli wszystkie wystąpienia elementu mojej bramy średnio z mniej niż 0,5 rdzenia, skalować usługę w przez usunięcie jednego wystąpienia. To co godzinę, ale nigdy nie mają mniej niż trzy wystąpienia w sumie.

Automatyczne skalowanie jest obsługiwana dla kontenerów i regularnego usługi Service Fabric. Aby korzystać z automatycznego skalowania, konieczne będzie działać w wersji 6.2 lub nowszej środowiska uruchomieniowego usługi Service Fabric. 

W pozostałej części tego artykułu opisano zasady skalowania, sposobów, aby włączyć lub wyłączyć automatyczne skalowanie i przedstawiono przykłady dotyczące używania tej funkcji.

## <a name="describing-auto-scaling"></a>Opisujące Autoskalowanie
Automatyczne skalowanie zasady mogą być definiowane dla poszczególnych usług w klastrze usługi Service Fabric. Wszystkie zasady skalowania składa się z dwóch części:
* **Skalowanie wyzwalacza** opisuje, kiedy wykonać skalowanie usługi. Warunki, które są zdefiniowane w wyzwalaczu sprawdzane są okresowo ustalenie, jeśli usługa powinna być skalowana.

* **Skalowanie mechanizm** w tym artykule opisano, jak skalowanie zostanie wykonane po jej wyzwoleniu. Mechanizm są stosowane tylko po spełnieniu warunków z wyzwalacza.

Wszystkie wyzwalacze, które są obecnie obsługiwane pracy za pomocą [miar logiczne obciążenia](service-fabric-cluster-resource-manager-metrics.md), lub za pomocą fizycznych metryk, takich jak użycie procesora CPU lub pamięci. W obu przypadkach usługi Service Fabric będzie monitorował zgłoszonych obciążenia metryki i oceni wyzwalacza okresowo, aby określić, czy skalowanie jest potrzebne.

Istnieją dwa mechanizmy, które są obecnie obsługiwane przez automatyczne skalowanie. Pierwsza z nich jest przeznaczona dla usługi bezstanowej lub kontenery, gdy automatyczne skalowanie odbywa się przez dodanie lub usunięcie [wystąpień](service-fabric-concepts-replica-lifecycle.md). Dla usług stanowych i bezstanowych, automatycznego skalowania można również przeprowadzić przez dodawanie lub usuwanie nazwanej [partycje](service-fabric-concepts-partitioning.md) usługi.

> [!NOTE]
> Obecnie są obsługiwane tylko jedne zasady skalowania na usługę i tylko jeden wyzwalacz skalowania na zasady skalowania.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Wyzwalacz obciążenia średni partycji o skalowanie na podstawie wystąpienia
Pierwszy typ wyzwalacza opiera się na obciążenie wystąpień w partycji o bezstanowa usługa. Metryki obciążenia są najpierw wygładzone uzyskać obciążenia dla każdego wystąpienia partycji, a następnie te wartości są uśredniane we wszystkich wystąpieniach partycji. Istnieją trzy czynniki, które określają, kiedy będzie można skalować usługi:

* _Dolny próg obciążenia_ jest wartością, która określa, kiedy usługa będzie **przeskalować w pionie**. Jeśli średnie obciążenie wszystkie wystąpienia elementu partycji jest niższa niż ta wartość, usługa będzie skalowana w.
* _Próg ładowania górny_ jest wartością, która określa, kiedy usługa będzie **skalowana w poziomie**. Jeśli średnie obciążenie wszystkie wystąpienia partycji jest wyższa niż ta wartość, następnie usługi będzie można skalować w poziomie.
* _Interwału skalowania_ Określa, jak często będą sprawdzane wyzwalacza. Gdy wyzwalacz jest zaznaczone, w razie potrzeby skalowania jest mechanizm zostaną zastosowane. Jeśli skalowanie nie jest wymagana, żadna akcja zostaną wykonane. W obu przypadkach wyzwalacz nie będą sprawdzane ponownie wygaśnięcia interwału skalowania ponownie.

Tego wyzwalacza można używać tylko w przypadku usług bezstanowych (bezstanowe, kontenery lub usługi Service Fabric). W przypadku, gdy usługa jest podzielona na partycje, wyzwalacz jest szacowana osobno dla każdej partycji, a każda partycja będzie mieć określony mechanizm zastosowano niezależnie. W związku z tym w tym przypadku jest możliwe, że niektóre partycje usługi będzie być skalowana w poziomie, niektóre treść będzie skalowana w i niektóre nie będzie można skalować w ogóle w tym samym czasie, na podstawie ich obciążenia.

Tylko mechanizm, który może być używany z tego wyzwalacza jest PartitionInstanceCountScaleMechanism. Istnieją trzy czynniki, które określają, jak ten mechanizm jest stosowany:
* _Skalowanie przyrostu_ Określa, ile wystąpień zostaną dodane lub usunięte po wyzwoleniu mechanizm.
* _Maksymalna liczba wystąpień_ definiuje górny limit skalowania. Jeśli wiele wystąpień partycji osiągnie ten limit, następnie usługa będzie nie być skalowana w poziomie, niezależnie od obciążenia. Istnieje możliwość pominąć ten limit, określając wartość -1, a w tym, że treść będzie skalowana w przypadku usługi out możliwie (limit jest liczba węzłów, które są dostępne w klastrze).
* _Minimalna liczba wystąpień_ określa dolną granicę dla skalowania. Jeśli wiele wystąpień partycji osiągnie ten limit, następnie usługa nie będzie skalowana w niezależnie od obciążenia.

## <a name="setting-auto-scaling-policy"></a>Ustawienie automatycznego skalowania zasad

### <a name="using-application-manifest"></a>Przy użyciu manifest aplikacji
``` xml
<LoadMetrics>
<LoadMetric Name="MetricB" Weight="High"/>
</LoadMetrics>
<ServiceScalingPolicies>
<ScalingPolicy>
    <AveragePartitionLoadScalingTrigger MetricName="MetricB" LowerLoadThreshold="1" UpperLoadThreshold="2" ScaleIntervalInSeconds="100"/>
    <InstanceCountScalingMechanism MinInstanceCount="3" MaxInstanceCount="4" ScaleIncrement="1"/>
</ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Za pomocą interfejsów API języka C#
```csharp
FabricClient fabricClient = new FabricClient();
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//set up the rest of the ServiceDescription
AveragePartitionLoadScalingTrigger trigger = new AveragePartitionLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new PartitionInstanceCountScaleMechanism();
mechanism.MaxInstanceCount = 3;
mechanism.MinInstanceCount = 1;
mechanism.ScaleIncrement = 1;
trigger.MetricName = "servicefabric:/_CpuCores";
trigger.ScaleInterval = TimeSpan.FromMinutes(20);
trigger.LowerLoadThreshold = 1.0;
trigger.UpperLoadThreshold = 2.0;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceDescription.ScalingPolicies.Add(policy);
//as we are using scaling on a resource this must be exclusive service
//also resource monitor service needs to be enabled
serviceDescription.ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```
### <a name="using-powershell"></a>Przy użyciu programu Powershell
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.PartitionInstanceCountScaleMechanism
$mechanism.MinInstanceCount = 1
$mechanism.MaxInstanceCount = 6
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AveragePartitionLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_CpuCores"
$trigger.LowerLoadThreshold = 0.3
$trigger.UpperLoadThreshold = 0.8
$trigger.ScaleInterval = New-TimeSpan -Minutes 10
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
Update-ServiceFabricService -Stateless -ServiceName "fabric:/AppName/ServiceName" -ScalingPolicies $scalingpolicies
```

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Wyzwalacz obciążenia średniej usług o skalowanie na podstawie partycji
Drugi wyzwalacz zależy od obciążenia wszystkich partycji w jednej usłudze. Metryki obciążenia są najpierw wygładzone uzyskać obciążenia dla każdej repliki lub wystąpienie partycji. Dla usług stanowych obciążenia partycji jest uważana obciążenie związane z repliką podstawową, gdy w przypadku usług bezstanowych obciążenia partycji jest średnie obciążenie wszystkie wystąpienia partycji. Te wartości są we wszystkich partycjach usługi, a ta wartość jest używana do wyzwolenia automatycznego skalowania. Takie same jak w poprzednim mechanizmu, istnieją trzy czynniki, które określają, w przypadku skalowania usługi:

* _Dolny próg obciążenia_ jest wartością, która określa, kiedy usługa będzie **przeskalować w pionie**. Jeśli średnie obciążenie wszystkich partycji usługi jest niższa niż ta wartość, usługa będzie skalowana w.
* _Próg ładowania górny_ jest wartością, która określa, kiedy usługa będzie **skalowana w poziomie**. Jeśli średnie obciążenie wszystkich partycji usługi jest wyższa niż ta wartość, następnie usługi będzie można skalować w poziomie.
* _Interwału skalowania_ Określa, jak często będą sprawdzane wyzwalacza. Gdy wyzwalacz jest zaznaczone, w razie potrzeby skalowania jest mechanizm zostaną zastosowane. Jeśli skalowanie nie jest wymagana, żadna akcja zostaną wykonane. W obu przypadkach wyzwalacz nie będą sprawdzane ponownie wygaśnięcia interwału skalowania ponownie.

Tego wyzwalacza można używać zarówno usługi stanowe i bezstanowe. Tylko mechanizm, który może być używany z tego wyzwalacza jest AddRemoveIncrementalNamedPartitionScalingMechanism. Gdy usługa jest skalowana w poziomie, a następnie dodaje się nową partycję i podczas skalowania usługi w jednej z istniejących partycji jest usuwany. Ma ograniczeń, które będą sprawdzane, gdy usługa jest tworzony lub aktualizowany i tworzenia/aktualizacji usługi zakończy się niepowodzeniem, jeśli te warunki nie są spełnione:
* Schemat partycji nazwanej, należy użyć dla usługi.
* Nazwy partycji musi być liczby całkowite kolejnych, takie jak "0", "1"...
* Pierwsza partycji musi mieć nazwę "0".

Na przykład jeśli usługa jest początkowo utworzony ze trzy partycje, możliwość jedyne prawidłowe nazwy partycji jest "0", "1" i "2".

Rzeczywiste automatycznego skalowania operacji, która jest wykonywana będzie uwzględniać ta oraz schemat nazewnictwa:
* Jeśli bieżące partycje usługi są nazywane "0", "1" i "2", następnie partycji, który zostanie dodany do skalowania w poziomie będzie miał nazwę "3".
* Jeśli bieżące partycje usługi są nazywane "0", "1" i "2", partycji, która zostanie usunięta dla skalowania w jest partycja o nazwie "2".

Takie same jak w przypadku mechanizm, który używa skalowania, dodając lub usuwając wystąpienia, istnieją trzy parametry, które określają, jak ten mechanizm jest stosowany:
* _Skalowanie przyrostu_ Określa, ile partycje zostaną dodane lub usunięte po wyzwoleniu mechanizm.
* _Maksymalna liczba partycji_ definiuje górny limit skalowania. Jeśli liczba partycji usługi osiągnie ten limit, następnie usługa będzie nie być skalowana w poziomie, niezależnie od obciążenia. Istnieje możliwość pominąć ten limit, określając wartość -1, a w tym, że treść będzie skalowana w przypadku usługi out możliwie (limit wynosi Rzeczywista wydajność klastra).
* _Minimalna liczba wystąpień_ określa dolną granicę dla skalowania. Jeśli liczba partycji usługi osiągnie ten limit, następnie usługa nie będzie skalowana w niezależnie od obciążenia.

> [!WARNING] 
> Stosowania AddRemoveIncrementalNamedPartitionScalingMechanism usługi stanowej usługi Service Fabric spowoduje dodanie lub usunięcie partycji **bez powiadomienia i ostrzeżenie**. Ponowny podział danych nie zostanie wykonane po wyzwoleniu mechanizm skalowania. W przypadku, gdy operacja skalowania w górę, nowe partycje będzie pusty, a w przypadku skalowania w dół operacji **partycji zostaną usunięte wraz z danymi, które zawiera**.

## <a name="setting-auto-scaling-policy"></a>Ustawienie automatycznego skalowania zasad

### <a name="using-application-manifest"></a>Przy użyciu manifest aplikacji
``` xml
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedPartitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Za pomocą interfejsów API języka C#
```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
AveragePartitionLoadScalingTrigger trigger = new AverageServiceLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new AddRemoveIncrementalNamedPartitionScalingMechanism();
mechanism.MaxPartitionCount = 4;
mechanism.MinPartitionCount = 1;
mechanism.ScaleIncrement = 1;
//expecting that the service already has metric NumberOfConnections
trigger.MetricName = "NumberOfConnections";
trigger.ScaleInterval = TimeSpan.FromMinutes(15);
trigger.LowerLoadThreshold = 10000;
trigger.UpperLoadThreshold = 20000;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceUpdate.ScalingPolicies = new List<ScalingPolicyDescription>;
serviceUpdate.ScalingPolicies.Add(policy);
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```
### <a name="using-powershell"></a>Przy użyciu programu Powershell
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.AddRemoveIncrementalNamedPartitionScalingMechanism
$mechanism.MinPartitionCount = 1
$mechanism.MaxPartitionCount = 3
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AverageServiceLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_MemoryInMB"
$trigger.LowerLoadThreshold = 5000
$trigger.UpperLoadThreshold = 10000
$trigger.ScaleInterval = New-TimeSpan -Minutes 25
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -TargetReplicaSetSize 3 -MinReplicaSetSize 2 -HasPersistedState true -PartitionNames @("0","1") -ServicePackageActivationMode ExclusiveProcess -ScalingPolicies $scalingpolicies
```

## <a name="auto-scaling-based-on-resources"></a>Automatyczne skalowanie na podstawie zasobów

Aby włączyć usługę monitor zasobów w celu skalowania na podstawie rzeczywistych zasobów

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Istnieją dwie metryki, które reprezentują rzeczywistych zasobów fizycznych. Jeden z nich jest servicefabric: _CpuCores, którą reprezentują rzeczywiste użycie procesora cpu (dzięki czemu 0,5 reprezentuje wysokości równej połowie rdzenia), a drugi ma rozmiar servicefabric: / _MemoryInMB, który reprezentuje użycie pamięci w MB.
ResourceMonitorService jest odpowiedzialny za śledzenie użycia procesora cpu i pamięci z usług użytkownika. Ta usługa zostanie zastosowana ważona średnia ruchoma aby uwzględnić potencjalnych wartości szczytowych krótkotrwałe. Monitorowanie zasobów jest obsługiwane dla aplikacji konteneryzowanych oraz niekonteneryzowanych na Windows i konteneryzowanych z nich w systemie Linux. Automatyczne skalowanie zasobów jest włączone tylko dla usług aktywowane w [model procesu wyłączne](service-fabric-hosting-model.md#exclusive-process-model).

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
