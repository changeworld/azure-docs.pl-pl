---
title: Sieć szkieletowa usług Azure automatycznie skalowania usługi i kontenery | Dokumenty Microsoft
description: Sieć szkieletowa usług Azure umożliwia ustawienie automatycznego skalowania zasady dla usługi i kontenerów.
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
ms.openlocfilehash: cd19c0e51ca1ac7863058d7c3944400719508f9b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34213201"
---
# <a name="introduction-to-auto-scaling"></a>Wprowadzenie do skalowania automatycznego
Automatyczne skalowanie jest dodatkowe możliwości sieci szkieletowej usług można dynamicznie skalować usług opartych na obciążenia, które usługi są raportowania lub na podstawie ich użycia zasobów. Automatyczne skalowanie zapewnia dużą elastyczność i umożliwia inicjowanie obsługi administracyjnej dodatkowe wystąpienia lub partycji usługi na żądanie. Automatycznie cały proces skalowania jest automatyczne i przejrzysty, a po skonfigurowaniu zasad w usłudze nie jest konieczne ręczne operacji skalowania na poziomie usługi. Automatyczne skalowanie można włączyć w czasie tworzenia usługi lub w dowolnym momencie przez aktualizację usługi.

Jest typowy scenariusz, w którym automatyczne skalowanie jest przydatne w przypadku obciążenia na określonej usługi różni się w czasie. Na przykład usługi takie jak bramy można skalować na podstawie ilości zasobów potrzebnych do obsługi żądań przychodzących. Spójrzmy na przykład jak może wyglądać tych reguł skalowania:
* Wszystkie wystąpienia bramy Moje średnio używania więcej niż dwa rdzenie, skalowanie usługi bramy się przez dodanie jednego wystąpienia więcej. W tym celu co godzinę, ale nigdy nie ma więcej niż siedmiu wystąpień w sumie.
* Jeśli wszystkie wystąpienia Moje bramy z mniej niż 0,5 rdzeni średnio, skalować usługę w przez usunięcie jednego wystąpienia. W tym celu co godzinę, ale nigdy nie ma w sumie mniejszej liczby wystąpień.

Automatyczne skalowanie jest obsługiwana dla kontenerów i regularne usługi sieć szkieletowa usług. Dalszej części tego artykułu opisano zasady skalowania, sposób, aby włączyć lub wyłączyć automatyczne skalowanie oraz przedstawiono przykłady dotyczące sposobu używania tej funkcji.

## <a name="describing-auto-scaling"></a>Opisujące automatyczne skalowanie
Automatyczne skalowanie zasady mogą być definiowane dla każdej usługi w klastrze usługi sieć szkieletowa usług. Każda zasada skalowania składa się z dwóch części:
* **Skalowanie wyzwalacza** opisuje podczas skalowania usługi będą wykonywane. Aby określić, czy usługa powinien być skalowany są sprawdzane w warunki, które są zdefiniowane w wyzwalaczu okresowo.

* **Skalowanie mechanizm** w tym artykule opisano, jak skalowanie będzie można wykonać, gdy zostaje wyzwolony. Mechanizm jest stosowany tylko wtedy, gdy są spełnione warunki z wyzwalacza.

Wszystkich wyzwalaczy, które są obecnie obsługiwane pracy za pomocą [metryki obciążenia logicznej](service-fabric-cluster-resource-manager-metrics.md), lub za pomocą fizycznych metryk, takich jak użycie procesora CPU lub pamięci. W obu przypadkach sieci szkieletowej usług będzie monitorował zgłoszone obciążenia metryki i będą oceniać wyzwalacza okresowo, aby określić, czy jest potrzebna skalowania.

Istnieją dwa mechanizmy, które są obecnie obsługiwane przez skalowanie automatyczne. Pierwsza z nich jest przeznaczony dla usług bezstanowych lub dla kontenerów, gdy automatyczne skalowanie odbywa się przez dodanie lub usunięcie [wystąpień](service-fabric-concepts-replica-lifecycle.md). W przypadku usług zarówno stanowe i bezstanowe automatyczne skalowanie można również przeprowadzić przez dodanie lub usunięcie o nazwie [partycje](service-fabric-concepts-partitioning.md) usługi.

> [!NOTE]
> Obecnie jest obsługiwane tylko jedne zasady skalowania dla danej usługi.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Wyzwalacz obciążenia partycji średnią z skalowanie wystąpienia na podstawie
Pierwszy typ wyzwalacza, bazuje na obciążenia wystąpień w partycji usługi bezstanowej. Metryka obciążenia są najpierw wygładzane uzyskanie obciążenia dla każdego wystąpienia partycji, a następnie te wartości zostały uśrednione we wszystkich wystąpieniach partycji. Istnieją trzy czynników, które określają, kiedy skalowania usługi:

* _Dolny próg obciążenia_ to wartość, która określa, kiedy usługa będzie **skalowany w**. Jeśli obciążenie średnią wszystkich wystąpień partycji jest niższa niż ta wartość, usługi będą skalowane w.
* _Próg ładowania górny_ to wartość, która określa, kiedy usługa będzie **skalowana w poziomie**. Jeśli obciążenie średnią wszystkich wystąpień partycji jest niższa niż ta wartość, usługi będą skalowane wychodzących.
* _Interwał skalowania_ Określa, jak często będą sprawdzane wyzwalacza. Po wyzwalacza jest zaznaczone, jeśli wymagane jest skalowanie mechanizmu zostaną zastosowane. Jeśli skalowania nie jest wymagana żadna akcja ze strony nastąpi przekierowanie. W obu przypadkach wyzwalacz nie będzie sprawdzana ponownie wygaśnięcia skalowania interwał ponownie.

Wyzwalacz mogą być używane tylko w usługi bezstanowej (bezstanowych kontenery lub usług sieci szkieletowej usług). W przypadku, gdy usługa ma wiele partycji, wyzwalacza jest szacowana osobno dla każdej partycji, a każda partycja będzie mieć określony mechanizm zastosować dla niego niezależnie. W związku z tym w tym przypadku jest możliwe, że niektóre partycje usługi będzie można skalować w poziomie, niektóre będą skalowane w i niektóre nie można skalować w ogóle w tym samym czasie, oparte na ich obciążenia.

Tylko mechanizm, który może być używany z tego wyzwalacza jest PartitionInstanceCountScaleMechanism. Istnieją trzy czynników, które określają, jak stosowany jest ten mechanizm:
* _Skalowanie przyrostu_ określa liczbę wystąpień zostaną dodane lub usunięte po wyzwoleniu mechanizmu.
* _Maksymalna liczba wystąpień_ definiuje górny limit skalowania. Jeśli liczba wystąpień partycji osiągnie ten limit, następnie usługa nie będzie skalowana, niezależnie od obciążenia. Istnieje możliwość pominąć ten limit, określając wartość -1, a w tym przypadku usługi będą skalowane out możliwie (limit to liczba węzłów, które są dostępne w klastrze).
* _Minimalna liczba wystąpień_ definiuje dolna granica skalowania. Jeśli liczba wystąpień partycji osiągnie ten limit, następnie usługa nie będzie skalowana w niezależnie od obciążenia.

## <a name="setting-auto-scaling-policy"></a>Ustawienie automatyczne skalowanie zasad

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

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Średnia usługi obciążenia wyzwalacza z partycji na podstawie skalowania
Drugi wyzwalacz zależy od obciążenia wszystkie partycje z jedną usługę. Metryka obciążenia są wygładzane najpierw uzyskać obciążenia dla każdej repliki lub wystąpienie partycji. Dla stanowych usług obciążenia partycji uważa się obciążenia repliką podstawową, gdy dla usług bezstanowych obciążenia partycji jest średnią obciążenia wszystkich wystąpień partycji. Te wartości są średnio wszystkich partycji usługi, a ta wartość służy do wyzwalania Skalowanie automatyczne. Takie same jak w poprzednich mechanizmu, istnieją trzech czynników, które określają podczas skalowania usługi:

* _Dolny próg obciążenia_ to wartość, która określa, kiedy usługa będzie **skalowany w**. Jeśli obciążenie średnią wszystkich partycji usługi jest starsza niż ta wartość, usługi będą skalowane w.
* _Próg ładowania górny_ to wartość, która określa, kiedy usługa będzie **skalowana w poziomie**. Jeśli obciążenie średnią wszystkich partycji usługi jest starsza niż ta wartość, usługi będą skalowane wychodzących.
* _Interwał skalowania_ Określa, jak często będą sprawdzane wyzwalacza. Po wyzwalacza jest zaznaczone, jeśli wymagane jest skalowanie mechanizmu zostaną zastosowane. Jeśli skalowania nie jest wymagana żadna akcja ze strony nastąpi przekierowanie. W obu przypadkach wyzwalacz nie będzie sprawdzana ponownie wygaśnięcia skalowania interwał ponownie.

Wyzwalacz może być używana jednocześnie z usługami stanowe i bezstanowe. Tylko mechanizm, który może być używany z tego wyzwalacza jest AddRemoveIncrementalNamedParitionScalingMechanism. Gdy usługa jest skalowana w poziomie, a następnie dodaniu nowej partycji i skalowania w jednym z istniejących partycji usługi zostaną usunięte. Ma ograniczeń, które będą sprawdzane podczas tworzenia lub aktualizowania usługi i usługi tworzenia/aktualizacji zakończy się niepowodzeniem, jeśli nie są spełnione następujące warunki:
* Schemat partycji o nazwie musi być używane przez usługę.
* Nazwy partycji muszą być liczbami kolejnych liczba całkowita, tak samo, jak "0", "1",...
* Pierwsza partycja musi mieć nazwę "0".

Na przykład jeśli usługa jest początkowo utworzony ze trzy partycje, możliwość jedyne prawidłowe nazwy partycji jest "0", "1" i "2".

Rzeczywiste automatycznego skalowania operację wykonywaną szanuje tego nazewnictwa schematu:
* Jeśli bieżącej partycji usługi są nazywane "0", "1" i "2", następnie partycji, który zostanie dodany do skalowania będzie miał nazwę "3".
* Jeśli bieżącej partycji usługi są nazywane "0", "1" i "2", partycja zostanie usunięta skalowania w jest partycja o nazwie "2".

Takie same jak w przypadku mechanizm, który używa skalowania przez dodanie lub usunięcie wystąpień, istnieją trzy parametry, które określają, jak stosowany jest ten mechanizm:
* _Skalowanie przyrostu_ Określa, ile partycje zostaną dodane lub usunięte po wyzwoleniu mechanizmu.
* _Maksymalna liczba partycji_ definiuje górny limit skalowania. Jeśli liczba partycji usługi osiągnie ten limit, następnie usługa nie będzie skalowana, niezależnie od obciążenia. Istnieje możliwość pominąć ten limit, określając wartość -1, a w tym przypadku usługi będą skalowane out możliwie (limit to rzeczywista wydajność klastra).
* _Minimalna liczba wystąpień_ definiuje dolna granica skalowania. Jeśli liczba partycji usługi osiągnie ten limit, następnie usługa nie będzie skalowana w niezależnie od obciążenia.

## <a name="setting-auto-scaling-policy"></a>Ustawienie automatyczne skalowanie zasad

### <a name="using-application-manifest"></a>Przy użyciu manifest aplikacji
``` xml
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedParitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Za pomocą interfejsów API języka C#
```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
AveragePartitionLoadScalingTrigger trigger = new AverageServiceLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new AddRemoveIncrementalNamedParitionScalingMechanism();
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
$mechanism = New-Object -TypeName System.Fabric.Description.AddRemoveIncrementalNamedParitionScalingMechanism
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

Aby włączyć usługę monitor zasobów można skalować na podstawie rzeczywistych zasobów

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Istnieją dwa metryk, które reprezentują rzeczywistych zasobów fizycznych. Jeden z nich jest servicefabric: / _CpuCores reprezentujące rzeczywistego użycia procesora cpu (aby 0,5 reprezentuje połowa podstawowa), a druga jest servicefabric: / _MemoryInMB, który reprezentuje użycia pamięci w MB.
ResourceMonitorService jest odpowiedzialny za śledzenie użycia procesora cpu i pamięci użytkownika usług. Ta usługa zostanie zastosowana ważonej średniej ruchomej, aby uwzględnić potencjalną nagłego krótkim okresie. Monitorowanie zasobów jest obsługiwane zarówno konteneryzowanych, jak i konteneryzowanych aplikacji w systemie Windows oraz konteneryzowanych widocznych w systemie Linux. Automatyczne skalowanie zasobów jest włączona tylko dla usług aktywowana w [model procesu wyłącznego](service-fabric-hosting-model.md#exclusive-process-model).

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [skalowalność aplikacji](service-fabric-concepts-scalability.md).