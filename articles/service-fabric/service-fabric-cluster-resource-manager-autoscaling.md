---
title: Usługi i kontenery automatycznego skalowania sieci szkieletowej usług Azure
description: Usługa Azure Service Fabric umożliwia ustawienie zasad skalowania automatycznego dla usług i kontenerów.
author: radicmilos
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: miradic
ms.openlocfilehash: 3660ece7add8f279292340aae9ab445b682fe045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452087"
---
# <a name="introduction-to-auto-scaling"></a>Wprowadzenie do skalowania automatycznego
Skalowanie automatyczne to dodatkowa funkcja sieci szkieletowej usług do dynamicznego skalowania usług na podstawie obciążenia, które usługi zgłaszają, lub na podstawie ich użycia zasobów. Automatyczne skalowanie zapewnia dużą elastyczność i umożliwia inicjowanie obsługi administracyjnej dodatkowych wystąpień lub partycji usługi na żądanie. Cały proces skalowania automatycznego jest zautomatyzowany i przejrzysty, a po skonfigurowaniu zasad w usłudze nie ma potrzeby ręcznego skalowania operacji na poziomie usługi. Automatyczne skalowanie można włączyć w czasie tworzenia usługi lub w dowolnym momencie, aktualizując usługę.

Typowy scenariusz, w którym automatyczne skalowanie jest przydatne jest, gdy obciążenie dla określonej usługi zmienia się w czasie. Na przykład usługa, taka jak brama, może być skalowana na podstawie ilości zasobów niezbędnych do obsługi żądań przychodzących. Przyjrzyjmy się przykładowi tego, jak mogą wyglądać te reguły skalowania:
* Jeśli wszystkie wystąpienia mojej bramy używają średnio więcej niż dwóch rdzeni, skaluj usługę bramy, dodając jeszcze jedno wystąpienie. Zrób to co godzinę, ale nigdy nie mają więcej niż siedem wystąpień w sumie.
* Jeśli wszystkie wystąpienia mojej bramy używają średnio mniej niż 0,5 rdzeni, skalowanie usługi przez usunięcie jednego wystąpienia. Zrób to co godzinę, ale nigdy nie mają mniej niż trzy wystąpienia w sumie.

Automatyczne skalowanie jest obsługiwane zarówno dla kontenerów, jak i zwykłych usług sieci szkieletowej usług. Aby używać skalowania automatycznego, należy uruchomić w wersji 6.2 lub wyższej środowiska uruchomieniowego sieci szkieletowej usług. 

W dalszej części tego artykułu opisano zasady skalowania, sposoby włączania lub wyłączania skalowania automatycznego i podano przykłady dotyczące używania tej funkcji.

## <a name="describing-auto-scaling"></a>Opisywanie skalowania automatycznego
Zasady skalowania automatycznego można zdefiniować dla każdej usługi w klastrze sieci szkieletowej usług. Każda zasada skalowania składa się z dwóch części:
* **Wyzwalacz skalowania** opisuje, kiedy skalowanie usługi zostanie wykonane. Warunki zdefiniowane w wyzwalaczu są okresowo sprawdzane w celu ustalenia, czy usługa powinna być skalowana, czy nie.

* **Mechanizm skalowania** opisuje sposób skalowania będą wykonywane po jego wyzwoleniu. Mechanizm jest stosowany tylko wtedy, gdy spełnione są warunki z wyzwalacza.

Wszystkie wyzwalacze, które są obecnie obsługiwane, działają z [metrykami obciążenia logicznego](service-fabric-cluster-resource-manager-metrics.md)lub z metrykami fizycznymi, takimi jak użycie procesora CPU lub pamięci. Tak czy inaczej, sieci szkieletowej usług będzie monitorować zgłoszone obciążenie dla metryki i będzie oceniać wyzwalacz okresowo, aby ustalić, czy skalowanie jest potrzebne.

Istnieją dwa mechanizmy, które są obecnie obsługiwane dla skalowania automatycznego. Pierwszy z nich jest przeznaczony dla usług bezstanowych lub kontenerów, w których automatyczne skalowanie jest wykonywane przez dodawanie lub usuwanie [wystąpień](service-fabric-concepts-replica-lifecycle.md). W przypadku usług stanowych i bezstanowych skalowanie automatyczne można również wykonać, dodając lub usuwając [nazwane partycje](service-fabric-concepts-partitioning.md) usługi.

> [!NOTE]
> Obecnie istnieje obsługa tylko jednej zasady skalowania na usługę i tylko jeden wyzwalacz skalowania na zasadę skalowania.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Wyzwalacz średniego obciążenia partycji ze skalowaniem opartym na wystąpieniu
Pierwszy typ wyzwalacza opiera się na obciążeniu wystąpień w partycji usługi bezstanowej. Obciążenia metryki są najpierw wygładzone, aby uzyskać obciążenie dla każdego wystąpienia partycji, a następnie te wartości są uśredniane we wszystkich wystąpieniach partycji. Istnieją trzy czynniki, które określają, kiedy usługa będzie skalowana:

* _Niższy próg obciążenia_ jest wartością, która określa, kiedy usługa będzie **skalowana w**. Jeśli średnie obciążenie wszystkich wystąpień partycji jest niższa niż ta wartość, usługa zostanie przeskalowana.
* _Górny próg obciążenia_ jest wartością, która określa, kiedy usługa zostanie **przeskalowana w poziomie.** Jeśli średnie obciążenie wszystkich wystąpień partycji jest wyższa niż ta wartość, usługa zostanie przeskalowana w poziomie.
* _Interwał skalowania_ określa, jak często wyzwalacz będzie sprawdzany. Po zaznaczeniu wyzwalacza, jeśli wymagane jest skalowanie, zostanie zastosowany mechanizm. Jeśli skalowanie nie jest potrzebne, nie zostaną podjęte żadne działania. W obu przypadkach wyzwalacz nie zostanie ponownie sprawdzony przed ponownym wygaśnięciem interwału skalowania.

Ten wyzwalacz może być używany tylko z usługami bezstanowymi (kontenery bezstanowe lub usługi sieci szkieletowej usług). W przypadku, gdy usługa ma wiele partycji, wyzwalacz jest oceniany dla każdej partycji oddzielnie, a każda partycja będzie miała określony mechanizm zastosowany do niej niezależnie. W związku z tym w tym przypadku jest możliwe, że niektóre partycje usługi zostaną skalowane w poziomie, niektóre zostaną skalowane, a niektóre nie będą skalowane w ogóle w tym samym czasie, na podstawie ich obciążenia.

Jedynym mechanizmem, który może służyć z tym wyzwalaczem jest PartitionInstanceCountScaleMechanism. Istnieją trzy czynniki, które określają sposób stosowania tego mechanizmu:
* _Przyrost skali_ określa, ile wystąpień zostanie dodanych lub usuniętych po wyzwoleniu mechanizmu.
* _Maksymalna liczba wystąpień_ definiuje górny limit skalowania. Jeśli liczba wystąpień partycji osiągnie ten limit, usługa nie zostanie skalowana w poziomie, niezależnie od obciążenia. Istnieje możliwość pominięcia tego limitu, określając wartość -1, a w takim przypadku usługa zostanie skalowana w poziomie jak najwięcej (limit to liczba węzłów, które są dostępne w klastrze).
* _Minimalna liczba wystąpień_ definiuje dolny limit skalowania. Jeśli liczba wystąpień partycji osiągnie ten limit, usługa nie będzie skalowana niezależnie od obciążenia.

## <a name="setting-auto-scaling-policy"></a>Ustawianie zasad automatycznego skalowania

### <a name="using-application-manifest"></a>Korzystanie z manifestu aplikacji
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
### <a name="using-c-apis"></a>Korzystanie z interfejsów API języka C#
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
### <a name="using-powershell"></a>Korzystanie z programu Powershell
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

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Wyzwalacz średniego obciążenia usługi ze skalowaniem opartym na partycjach
Drugi wyzwalacz jest oparty na obciążeniu wszystkich partycji jednej usługi. Obciążenia metryki są najpierw wygładzone, aby uzyskać obciążenie dla każdej repliki lub wystąpienia partycji. W przypadku usług stanowych obciążenie partycji jest uważane za obciążenie repliki podstawowej, podczas gdy w przypadku usług bezstanowych obciążenie partycji jest średnim obciążeniem wszystkich wystąpień partycji. Wartości te są uśrednione we wszystkich partycjach usługi, a ta wartość jest używana do wyzwalania skalowania automatycznego. Tak samo jak w poprzednim mechanizmie, istnieją trzy czynniki, które określają, kiedy usługa będzie skalowana:

* _Niższy próg obciążenia_ jest wartością, która określa, kiedy usługa będzie **skalowana w**. Jeśli średnie obciążenie wszystkich partycji usługi jest niższa niż ta wartość, usługa zostanie przeskalowana.
* _Górny próg obciążenia_ jest wartością, która określa, kiedy usługa zostanie **przeskalowana w poziomie.** Jeśli średnie obciążenie wszystkich partycji usługi jest wyższa niż ta wartość, usługa zostanie przeskalowana w poziomie.
* _Interwał skalowania_ określa, jak często wyzwalacz będzie sprawdzany. Po zaznaczeniu wyzwalacza, jeśli wymagane jest skalowanie, zostanie zastosowany mechanizm. Jeśli skalowanie nie jest potrzebne, nie zostaną podjęte żadne działania. W obu przypadkach wyzwalacz nie zostanie ponownie sprawdzony przed ponownym wygaśnięciem interwału skalowania.

Ten wyzwalacz może być używany zarówno z usługami stanowymi, jak i bezstanowymi. Jedynym mechanizmem, który może być używany z tym wyzwalaczem jest AddRemoveIncrementalNamedPartitionScalingMechanism. Gdy usługa jest skalowana w poziomie, a następnie dodaje się nową partycję, a gdy usługa jest skalowana w jednej z istniejących partycji jest usuwany. Istnieją ograniczenia, które będą sprawdzane podczas tworzenia lub aktualizowania usługi, a tworzenie/aktualizowanie usługi zakończy się niepowodzeniem, jeśli te warunki nie są spełnione:
* Nazwany schemat partycji musi być używany dla usługi.
* Nazwy partycji muszą być kolejnymi liczbami całkowitymi, takimi jak "0", "1", ...
* Pierwsza nazwa partycji musi być "0".

Na przykład jeśli usługa jest początkowo tworzona z trzema partycjami, jedyną prawidłową możliwością dla nazw partycji jest "0", "1" i "2".

Rzeczywista operacja automatycznego skalowania, która jest wykonywana będzie przestrzegać tego schematu nazewnictwa, jak również:
* Jeśli bieżące partycje usługi są nazywane "0", "1" i "2", partycja, która zostanie dodana do skalowania w poziomie, zostanie nazwana "3".
* Jeśli bieżące partycje usługi są nazywane "0", "1" i "2", a następnie partycji, która zostanie usunięta do skalowania w jest partycja o nazwie "2".

Tak samo jak w przypadku mechanizmu, który używa skalowania przez dodanie lub usunięcie wystąpień, istnieją trzy parametry, które określają sposób stosowania tego mechanizmu:
* _Przyrost skali_ określa, ile partycji zostanie dodanych lub usuniętych po wyzwoleniu mechanizmu.
* _Maksymalna liczba partycji_ definiuje górny limit skalowania. Jeśli liczba partycji usługi osiągnie ten limit, usługa nie będzie skalowana w poziomie, niezależnie od obciążenia. Istnieje możliwość pominięcia tego limitu, określając wartość -1, a w takim przypadku usługa zostanie skalowana w poziomie jak najwięcej (limit jest rzeczywistą pojemnością klastra).
* _Minimalna liczba wystąpień_ definiuje dolny limit skalowania. Jeśli liczba partycji usługi osiągnie ten limit, usługa nie będzie skalowana niezależnie od obciążenia.

> [!WARNING] 
> Gdy AddRemoveIncrementalNamedPartitionScalingMechanism jest używany z usługami stanowymi, sieci szkieletowej usług doda lub usunie partycje **bez powiadomienia lub ostrzeżenia**. Ponowne partycjonowanie danych nie zostanie wykonane po wyzwoleniu mechanizmu skalowania. W przypadku operacji skalowania w górę nowe partycje będą puste, a w przypadku operacji skalowania w dół **partycja zostanie usunięta wraz ze wszystkimi danymi, które zawiera**.

## <a name="setting-auto-scaling-policy"></a>Ustawianie zasad automatycznego skalowania

### <a name="using-application-manifest"></a>Korzystanie z manifestu aplikacji
``` xml
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedPartitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Korzystanie z interfejsów API języka C#
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
### <a name="using-powershell"></a>Korzystanie z programu Powershell
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

Aby umożliwić usługę monitorowania zasobów skalowanie na podstawie rzeczywistych zasobów

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Istnieją dwie metryki, które reprezentują rzeczywiste zasoby fizyczne. Jednym z nich jest servicefabric:/_CpuCores które reprezentują rzeczywiste użycie procesora (więc 0,5 reprezentuje pół rdzenia), a drugi jest servicefabric:/_MemoryInMB który reprezentuje użycie pamięci w MB.
ResourceMonitorService jest odpowiedzialny za śledzenie użycia procesora i pamięci usług użytkownika. Usługa ta będzie stosować ważoną średnią ruchomą w celu uwzględnienia potencjalnych krótkotrwałych skoków. Monitorowanie zasobów jest obsługiwane zarówno dla konteneryzowanych i nie konteneryzowanych aplikacji w systemie Windows, jak i konteneryzowanych w systemie Linux. Automatyczne skalowanie zasobów jest włączone tylko dla usług aktywowanych w [ekskluzywnym modelu procesu](service-fabric-hosting-model.md#exclusive-process-model).

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
