---
title: Jak wyświetlić jednostki usługi Azure Service Fabric zagregowaną kondycję | Dokumentacja firmy Microsoft
description: Opisuje sposób wykonywania kwerend, wyświetlić i ocenić zagregowanej kondycji jednostek usługi Azure Service Fabric za pomocą zapytań o kondycję i ogólne zapytania.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: fa34c52d-3a74-4b90-b045-ad67afa43fe5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: e4edcc0aecfbf03aff7cf9bee764522bb1c489f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60716395"
---
# <a name="view-service-fabric-health-reports"></a>Wyświetlanie raportów o kondycji usługi Service Fabric
Usługa Azure Service Fabric wprowadza [modelu kondycji](service-fabric-health-introduction.md) przy użyciu jednostek kondycji, na którym składników systemu i watchdogs można raportu lokalnego warunki, które są monitorowania. [Magazynu kondycji](service-fabric-health-introduction.md#health-store) agreguje wszystkie dane kondycji, aby określić, czy jednostki są w dobrej kondycji.

Klaster zostanie automatycznie wypełniona raportów o kondycji wysyłane przez składniki systemu. Dowiedz się więcej o [Rozwiązywanie problemów przy użyciu raportów kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Usługa Service Fabric udostępnia kilka sposobów uzyskania zagregowaną kondycję jednostek:

* [Narzędzie Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) lub innych narzędzi do wizualizacji
* Zapytania o kondycję (za pośrednictwem programu PowerShell, interfejsu API lub REST)
* Ogólne zapytania oznacza zwracaną listę obiektów, które mają kondycji jako jedna z właściwości (przy użyciu programu PowerShell, interfejsu API lub REST)

Aby zademonstrować tych opcji, Użyjmy lokalny klaster z pięcioma węzłami i [Service fabric: / WordCount aplikacji](https://aka.ms/servicefabric-wordcountapp). **Service fabric: / WordCount** aplikacja zawiera dwie usługi domyślne usługi stanowej typu `WordCountServiceType`i usługę bezstanową typu `WordCountWebServiceType`. Po zmianie `ApplicationManifest.xml` będą musieli siedem docelowej repliki dla usługi stanowej i jedną partycję. Ponieważ istnieje tylko pięć węzłów w klastrze, składników systemu zgłosiło ostrzeżenie na partycji usługi jest mniejsza od liczby docelowych.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Kondycji w narzędziu Service Fabric Explorer
Narzędzie Service Fabric Explorer zawiera widok graficzny klastra. Na poniższej ilustracji widać:

* Aplikacja **Service fabric: / WordCount** jest czerwony (w wyniku błędu), ponieważ ma ona zdarzenie błędu zgłoszony przez **MyWatchdog** właściwości **dostępności**.
* Jeden z jego usług **Service fabric: / WordCount/usługi wordcountservice uległa** żółte (w ostrzeżenie). Usługa jest skonfigurowana z siedmiu replikami, i klaster ma pięć węzłów, więc nie może znajdować się dwie repliki. Mimo że nie został tutaj pokazany, partycji usługi jest żółty, ze względu na raportu systemu z `System.FM` informacją, że `Partition is below target replica or instance count`. Żółty partycji wyzwala żółty usługi.
* Klaster jest czerwony ze względu na czerwony aplikacji.

Ocena używa domyślnych zasad w manifeście klastra i manifest aplikacji. Są surowymi zasadami i nie ma znaczenia jakiekolwiek niepowodzenie.

Widok klastra przy użyciu narzędzia Service Fabric Explorer:

![Widok klastra przy użyciu narzędzia Service Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Przeczytaj więcej na temat [narzędzia Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Zapytania o kondycję
Usługa Service Fabric udostępnia zapytań o kondycję dla każdej z obsługiwanych [typów jednostek](service-fabric-health-introduction.md#health-entities-and-hierarchy). Są one dostępne za pośrednictwem interfejsu API, za pomocą metod na [FabricClient.HealthManager](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), w przypadku poleceń cmdlet programu PowerShell i REST. Te zapytania zwracają kondycji pełne informacje dotyczące jednostki: zagregowanej kondycji, zdarzenia dotyczące kondycji jednostki, kondycja podrzędnych (jeśli ma zastosowanie), oceny złej kondycji (jeśli jednostki nie jest w dobrej kondycji) i statystyki kondycji elementy podrzędne (gdy ma to zastosowanie).

> [!NOTE]
> Jednostki kondycji jest zwracany, gdy zostanie całkowicie wypełniony w magazynie kondycji. Jednostka musi być aktywne (nieusunięty) i ma raportu system. Jej podmioty nadrzędne łańcucha hierarchii musi mieć również raporty systemu. Jeśli którykolwiek z tych warunków nie zostanie spełniony, kondycji kwerendy zwrócenia [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) z [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` w którym pokazano, dlaczego nie są zwracane jednostki.
>
>

Zapytania o kondycję musi pomyślnie przejść w identyfikatorze jednostki, która zależy od typu jednostki. Zapytania zaakceptować kondycji opcjonalne parametry zasad. Jeśli nie określono żadnych zasad dotyczących kondycji, [zasady dotyczące kondycji](service-fabric-health-introduction.md#health-policies) z manifestu klastra lub aplikacji są używane do oceny. Jeśli manifest nie zawiera definicji zasad dotyczących kondycji, domyślnych zasad kondycji służą do oceny. Domyślne zasady kondycji nieodpowiednie zakończą się niepowodzeniem. Zapytania także zaakceptować filtry dla zwracania tylko częściowe elementów podrzędnych lub zdarzeń — te, które przestrzegają określonych filtrów. Inny filtr umożliwia wykluczenie statystyki elementów podrzędnych.

> [!NOTE]
> Filtry dane wyjściowe są stosowane po stronie serwera, więc zmniejszono rozmiar komunikatu odpowiedzi. Zaleca się, że można Użyj filtrów danych wyjściowych, aby ograniczyć dane zwrócone, zamiast stosować filtry po stronie klienta.
>
>

Kondycja jednostki zawiera:

* Stan kondycji zagregowanych podmiotu. Obliczone przez magazynu kondycji na podstawie raportów o kondycji jednostki, kondycja podrzędnych (jeśli ma zastosowanie) i zasady dotyczące kondycji. Przeczytaj więcej na temat [ocenę kondycji jednostki](service-fabric-health-introduction.md#health-evaluation).  
* Zdarzenia kondycji w jednostce.
* Kolekcja stanów kondycji wszystkich elementów podrzędnych dla jednostek, które mogą mieć elementów podrzędnych. Stany kondycji zawiera identyfikatorów jednostki i zagregowane kondycji. Aby uzyskać pełną kondycji dla elementu podrzędnego, wywołaj kondycji zapytania dla typu jednostki podrzędne i przekazać identyfikator podrzędnego.
* Oceny złej kondycji, które wskazują do raportu, który wyzwolił stan jednostki, jeśli jednostki nie jest w dobrej kondycji. Wersje ewaluacyjne są cykliczne, zawierający oceny kondycji elementy podrzędne, które wyzwalane bieżącego stanu kondycji. Na przykład strażnika zgłosił błąd dla repliki. Kondycja aplikacji przedstawia oceny złej kondycji ze względu na usługi w złej kondycji; Usługa jest w złej kondycji ze względu na partycję w wyniku błędu; partycja jest w złej kondycji ze względu na replikę w błąd; Replika jest w złej kondycji ze względu na raport o kondycji błąd strażnika.
* Statystyki kondycji dla wszystkich elementów podrzędnych typów obiektów, które mają elementy podrzędne. Na przykład kondycji klastra zawiera całkowitą liczbę aplikacji, usług, partycji, repliki i wdrożone jednostek w klastrze. Kondycja usługi zawiera całkowitą liczbę partycji i replik w ramach określonej usługi.

## <a name="get-cluster-health"></a>Pobierz stan klastra
Zwraca kondycji obiektu klastra i zawiera stany kondycji aplikacji i węzłów (children klastra). Dane wejściowe:

* [Opcjonalnie] Zasad dotyczących kondycji klastra używane do oceny, węzły i zdarzenia klastra.
* [Opcjonalnie] Kondycja zasad mapy aplikacji przy użyciu zasad kondycji służy do zastępowania zasad manifestu aplikacji.
* [Opcjonalnie] Filtry dla zdarzeń, węzły i aplikacji, które określają, które wpisy są przedmiotem zainteresowania i ma zostać zwrócone w wyniku (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia, węzły i aplikacje są używane do oceny kondycji jednostki zagregowane, niezależnie od tego filtru.
* [Opcjonalnie] Filtruj, aby wykluczyć statystyki kondycji.
* [Opcjonalnie] Filtr w celu uwzględnienia Service fabric: / statystyki kondycji systemu w statystyki kondycji. Dotyczy tylko gdy statystyki kondycji nie są wyłączone. Domyślnie statystyki kondycji zawierają tylko statystyki dla aplikacji użytkownika, a nie aplikację systemu.

### <a name="api"></a>Interfejs API
Aby uzyskać stan klastra, należy utworzyć `FabricClient` i wywołać [GetClusterHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) metody na jego **HealthManager**.

Poniższe wywołanie pobiera kondycji klastra:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

Poniższy kod umożliwia pobranie kondycji klastra przy użyciu zasad kondycji niestandardowego klastra i filtry dla węzłów i aplikacji. Określa, że statystyki kondycji obejmują sieci szkieletowej: / statystyk systemu. Tworzy [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthquerydescription), który zawiera dane wejściowe.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var healthStatisticsFilter = new ClusterHealthStatisticsFilter()
{
    ExcludeHealthStatistics = false,
    IncludeSystemApplicationHealthStatistics = true
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
    HealthStatisticsFilter = healthStatisticsFilter
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
To polecenie cmdlet umożliwiające uzyskanie kondycji klastra [Get-ServiceFabricClusterHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealth). Najpierw połącz się z klastrem przy użyciu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) polecenia cmdlet.

Stan klastra jest pięć węzłów, aplikacja systemowa i Service fabric: / WordCount skonfigurowana zgodnie z opisem.

Następujące polecenie cmdlet pobiera kondycji klastra przy użyciu domyślnej zasady dotyczące kondycji. Ostrzeżenie zagregowanej kondycji, ponieważ sieci szkieletowej: / aplikacja WordCount jest ostrzeżenie. Należy pamiętać o tym, jak oceny złej kondycji zawierają szczegółowe informacje dotyczące warunków, które wyzwolony, zagregowanej kondycji.

```xml
PS D:\ServiceFabric> Get-ServiceFabricClusterHealth


AggregatedHealthState   : Warning
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.


NodeHealthStates        : 
                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
HealthStatistics        : 
                          Node                  : 5 Ok, 0 Warning, 0 Error
                          Replica               : 6 Ok, 0 Warning, 0 Error
                          Partition             : 1 Ok, 1 Warning, 0 Error
                          Service               : 1 Ok, 1 Warning, 0 Error
                          DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                          DeployedApplication   : 5 Ok, 0 Warning, 0 Error
                          Application           : 0 Ok, 1 Warning, 0 Error
```

Następujące polecenie cmdlet programu PowerShell pobiera kondycji klastra za pomocą zasad niestandardowych aplikacji. Filtruje wyniki, aby pobrać tylko aplikacji i węzły w błąd lub ostrzeżenie. W wyniku żadnych węzłów są zwracane, ponieważ są one wszystkie w dobrej kondycji. Tylko Service fabric: / aplikacja WordCount szanuje filtr aplikacji. Ponieważ określa zasady niestandardowe, należy wziąć pod uwagę ostrzeżenia jako błędy w sieci szkieletowej: / aplikacja WordCount, aplikacja jest oceniane jak błąd, a więc klastra.

```powershell
PS D:\ServiceFabric> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics


AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None
```

### <a name="rest"></a>REST
Możesz uzyskać kondycji klastra za pomocą [żądanie GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster) lub [żądania POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) zawierającej zasady dotyczące kondycji, które opisano w treści.

## <a name="get-node-health"></a>Pobierz kondycji węzła
Zwraca kondycji jednostki węzła i zawiera zdarzenia kondycji zgłoszone w węźle. Dane wejściowe:

* [Wymagane] Nazwa węzła, który identyfikuje węzeł.
* [Opcjonalnie] Ustawienia zasad kondycji klastra używane do oceny kondycji.
* [Opcjonalnie] Filtry dla zdarzeń, które określają, które wpisy są przedmiotem zainteresowania i ma zostać zwrócone w wyniku (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia są używane do oceny kondycji jednostki zagregowane, niezależnie od tego filtru.

### <a name="api"></a>Interfejs API
Aby uzyskać kondycji węzła za pośrednictwem interfejsu API, należy utworzyć `FabricClient` i wywołać [GetNodeHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) metody na jego HealthManager.

Poniższy kod umożliwia pobranie kondycji węzła dla nazwy określonego węzła:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

Poniższy kod pobiera nazwę określonego węzła kondycji węzła i przekazuje filtr zdarzeń i zasad niestandardowych za pomocą [NodeHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.nodehealthquerydescription):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
To polecenie cmdlet umożliwiające uzyskanie kondycji węzła [Get ServiceFabricNodeHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnodehealth). Najpierw połącz się z klastrem przy użyciu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) polecenia cmdlet.
Następujące polecenie cmdlet pobiera kondycji węzła przy użyciu domyślnej zasady dotyczące kondycji:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 7/13/2017 4:39:23 PM
                        ReceivedAt            : 7/13/2017 4:40:47 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 4:40:47 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Następujące polecenie cmdlet pobiera kondycję wszystkich węzłów w klastrze:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_4                     Ok
_Node_3                     Ok
_Node_2                     Ok
_Node_1                     Ok
_Node_0                     Ok
```

### <a name="rest"></a>REST
Możesz uzyskać kondycji węzła przy użyciu [żądanie GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node) lub [żądania POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) zawierającej zasady dotyczące kondycji, które opisano w treści.

## <a name="get-application-health"></a>Pobierz stan aplikacji
Zwraca kondycji jednostki aplikacji. Zawiera ona stanów kondycji wdrożonej aplikacji i elementy podrzędne usługi. Dane wejściowe:

* [Wymagane] Nazwa aplikacji (URI), który identyfikuje aplikację.
* [Opcjonalnie] Zasady kondycji aplikacji służy do zastępowania zasad manifestu aplikacji.
* [Opcjonalnie] Filtry dla zdarzeń, usług i wdrożone aplikacje, które określają, które wpisy są przedmiotem zainteresowania i ma zostać zwrócone w wyniku (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia, usług i wdrożone aplikacje są używane do oceny kondycji jednostki zagregowane, niezależnie od tego filtru.
* [Opcjonalnie] Filtruj, aby wykluczyć statystyki kondycji. Jeśli nie zostanie określony, statystyki kondycji obejmują ok, ostrzeżenie, a liczba błędów na wszystkie obiekty podrzędne aplikacji: usług, partycji, replik, wdrożonych aplikacji i wdrożonych pakietów usługi.

### <a name="api"></a>Interfejs API
Aby uzyskać kondycji aplikacji, należy utworzyć `FabricClient` i wywołać [GetApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) metody na jego HealthManager.

Poniższy kod umożliwia pobranie kondycji aplikacji dla nazwy określonej aplikacji (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

Poniższy kod umożliwia pobranie kondycji aplikacji dla nazwy określonej aplikacji (URI), za pomocą filtrów oraz zasady niestandardowe określone za pomocą [ApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.applicationhealthquerydescription).

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
To polecenie cmdlet umożliwiające uzyskanie kondycji aplikacji [Get ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps). Najpierw połącz się z klastrem przy użyciu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) polecenia cmdlet.

Następujące polecenie cmdlet zwraca kondycję **Service fabric: / WordCount** aplikacji:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

DeployedApplicationHealthStates : 
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/13/2017 5:57:05 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics                : 
                                  Replica               : 6 Ok, 0 Warning, 0 Error
                                  Partition             : 1 Ok, 1 Warning, 0 Error
                                  Service               : 1 Ok, 1 Warning, 0 Error
                                  DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                                  DeployedApplication   : 5 Ok, 0 Warning, 0 Error
```

Następujące polecenie cmdlet programu PowerShell przekazuje zasady niestandardowe. Filtruje także elementy podrzędne i zdarzenia.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.

ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>REST
Możesz uzyskać kondycję aplikacji za pomocą [żądanie GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application) lub [żądania POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) zawierającej zasady dotyczące kondycji, które opisano w treści.

## <a name="get-service-health"></a>Pobierz usługę kondycji
Zwraca kondycji jednostki usługi. Zawiera ona stanów kondycji partycji. Dane wejściowe:

* [Wymagane] Nazwa usługi (URI), który identyfikuje usługę.
* [Opcjonalnie] Zasady kondycji aplikacji służy do zastępowania zasad manifestu aplikacji.
* [Opcjonalnie] Filtry dla zdarzeń i partycje, które określają, które wpisy są przedmiotem zainteresowania i ma zostać zwrócone w wyniku (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia i partycji są używane do oceny kondycji jednostki zagregowane, niezależnie od tego filtru.
* [Opcjonalnie] Filtruj, aby wykluczyć statystyki kondycji. Jeśli nie określono, statystyki kondycji pokazują ok, ostrzeżenie, i błąd uznawane za wszystkie partycje i repliki usługi.

### <a name="api"></a>Interfejs API
Aby uzyskać kondycję usługi przy użyciu interfejsu API, należy utworzyć `FabricClient` i wywołać [GetServiceHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) metody na jego HealthManager.

Poniższy przykład pobiera kondycję usługi przy użyciu określonej nazwy usługi (URI):

```csharp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

Poniższy kod umożliwia pobranie kondycja usługi dla określonej nazwy usługi (URI), określając filtry i zasad niestandardowych za pomocą [ServiceHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicehealthquerydescription):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
To polecenie cmdlet umożliwiające uzyskanie kondycja usługi [Get ServiceFabricServiceHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicehealth). Najpierw połącz się z klastrem przy użyciu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) polecenia cmdlet.

Następujące polecenie cmdlet pobiera kondycję usługi przy użyciu domyślnej zasady dotyczące kondycji:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                        Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                            Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

PartitionHealthStates : 
                        PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                        AggregatedHealthState : Warning

HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 15
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
                        Partition             : 0 Ok, 1 Warning, 0 Error
```

### <a name="rest"></a>REST
Możesz uzyskać kondycję usługi przy użyciu [żądanie GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service) lub [żądania POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) zawierającej zasady dotyczące kondycji, które opisano w treści.

## <a name="get-partition-health"></a>Pobierz kondycji partycji
Zwraca kondycji jednostki partycji. Zawiera ona stanów kondycji repliki. Dane wejściowe:

* [Wymagane] Partycja identyfikator (GUID), który identyfikuje partycji.
* [Opcjonalnie] Zasady kondycji aplikacji służy do zastępowania zasad manifestu aplikacji.
* [Opcjonalnie] Filtry dla zdarzeń i replik, które określają, które wpisy są przedmiotem zainteresowania i ma zostać zwrócone w wyniku (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia i repliki są używane do oceny kondycji jednostki zagregowane, niezależnie od tego filtru.
* [Opcjonalnie] Filtruj, aby wykluczyć statystyki kondycji. Jeśli nie zostanie określony, statystyki kondycji pokazują, ile repliki są w ok, ostrzeżenia i błędu stanów.

### <a name="api"></a>Interfejs API
Można pobrać partycji kondycji za pomocą interfejsu API, należy utworzyć `FabricClient` i wywołać [GetPartitionHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) metody na jego HealthManager. Aby określić następujące parametry opcjonalne, należy utworzyć [PartitionHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.partitionhealthquerydescription).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
To polecenie cmdlet umożliwiające uzyskanie kondycji partycji [Get ServiceFabricPartitionHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricpartitionhealth). Najpierw połącz się z klastrem przy użyciu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) polecenia cmdlet.

Następujące polecenie cmdlet pobiera kondycji dla wszystkich partycji **Service fabric: / WordCount/usługi wordcountservice uległa** usługi i odfiltrowuje repliki stanów kondycji:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 72
                        SentAt                : 7/13/2017 5:57:29 PM
                        ReceivedAt            : 7/13/2017 5:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/P RD _Node_2 Up 131444422260002646
                          N/S RD _Node_4 Up 131444422293113678
                          N/S RD _Node_3 Up 131444422293113679
                          N/S RD _Node_1 Up 131444422293118720
                          N/S RD _Node_0 Up 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5.)

                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 7/13/2017 5:57:48 PM, LastError = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131444445174851664
                        SentAt                : 7/13/2017 6:35:17 PM
                        ReceivedAt            : 7/13/2017 6:35:18 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A

                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None

                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/13/2017 5:57:48 PM, LastOk = 1/1/0001 12:00:00 AM

HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Możesz uzyskać kondycję partycji za pomocą [żądanie GET](/rest/api/servicefabric/sfclient-api-getpartitionhealth) lub [żądania POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) zawierającej zasady dotyczące kondycji, które opisano w treści.

## <a name="get-replica-health"></a>Pobierz kondycji repliki
Zwraca kondycję repliki usługi stanowej lub wystąpienie usługi bezstanowej. Dane wejściowe:

* [Wymagane] Identyfikator (GUID) i repliki Identyfikatora partycji identyfikujący repliki.
* [Opcjonalnie] Parametry zasad kondycji aplikacji służy do zastępowania zasad manifestu aplikacji.
* [Opcjonalnie] Filtry dla zdarzeń, które określają, które wpisy są przedmiotem zainteresowania i ma zostać zwrócone w wyniku (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia są używane do oceny kondycji jednostki zagregowane, niezależnie od tego filtru.

### <a name="api"></a>Interfejs API
Aby kondycji repliki za pomocą interfejsu API, należy utworzyć `FabricClient` i wywołać [GetReplicaHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) metody na jego HealthManager. Aby określić parametry zaawansowane, użyj [ReplicaHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.replicahealthquerydescription).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
To polecenie cmdlet umożliwiające uzyskanie kondycji repliki [Get ServiceFabricReplicaHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricreplicahealth). Najpierw połącz się z klastrem przy użyciu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) polecenia cmdlet.

Następujące polecenie cmdlet pobiera kondycję replika podstawowa dla wszystkich partycji usługi:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Możesz uzyskać kondycję repliki za pomocą [żądanie GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica) lub [żądania POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) zawierającej zasady dotyczące kondycji, które opisano w treści.

## <a name="get-deployed-application-health"></a>Pobierz kondycji wdrożonej aplikacji
Zwraca kondycję aplikacji wdrożonej w jednostce węzła. Zawiera ona stanów kondycji pakietu wdrożonej usługi. Dane wejściowe:

* [Wymagane] Nazwa aplikacji (URI) i nazwę węzła (ciąg), które identyfikują wdrożonej aplikacji.
* [Opcjonalnie] Zasady kondycji aplikacji służy do zastępowania zasad manifestu aplikacji.
* [Opcjonalnie] Filtry dla zdarzeń i wdrożone pakiety usługi, które określają, które wpisy są przedmiotem zainteresowania i ma zostać zwrócone w wyniku (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia i wdrożone pakiety usługi służą do oceny kondycji jednostki zagregowane, niezależnie od tego filtru.
* [Opcjonalnie] Filtruj, aby wykluczyć statystyki kondycji. Jeśli nie zostanie określony, statystyki kondycji Pokaż liczbę wdrożone pakiety usługi w kondycja ok, ostrzeżenia i błędu.

### <a name="api"></a>Interfejs API
Aby uzyskać kondycję aplikacji wdrożonej w węźle za pomocą interfejsu API, należy utworzyć `FabricClient` i wywołać [GetDeployedApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) metody na jego HealthManager. Aby określić następujące parametry opcjonalne, należy użyć [DeployedApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
To polecenie cmdlet umożliwiające uzyskanie kondycji wdrożoną aplikację [Get ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps). Najpierw połącz się z klastrem przy użyciu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) polecenia cmdlet. Aby dowiedzieć się, gdy aplikacja zostanie wdrożona, uruchom [Get ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) i przyjrzyj się dzieci wdrożonej aplikacji.

Następujące polecenie cmdlet pobiera kondycję **Service fabric: / WordCount** aplikacja wdrożona na **węzeł _Node_2**.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_0


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_0
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok

HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131444422261848308
                                     SentAt                : 7/13/2017 5:57:06 PM
                                     ReceivedAt            : 7/13/2017 5:57:17 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/13/2017 5:57:17 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics                   : 
                                     DeployedServicePackage : 2 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Możesz uzyskać kondycję wdrożonej aplikacji za pomocą [żądanie GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application) lub [żądania POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) zawierającej zasady dotyczące kondycji, które opisano w treści.

## <a name="get-deployed-service-package-health"></a>Pobierz kondycji pakietu wdrożonej usługi
Zwraca kondycji jednostki pakietu wdrożonej usługi. Dane wejściowe:

* [Wymagane] Nazwa aplikacji (URI), nazwę węzła (ciąg) i nazwy manifestu usługi (ciąg), które identyfikują pakietu wdrożonej usługi.
* [Opcjonalnie] Zasady kondycji aplikacji służy do zastępowania zasad manifestu aplikacji.
* [Opcjonalnie] Filtry dla zdarzeń, które określają, które wpisy są przedmiotem zainteresowania i ma zostać zwrócone w wyniku (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia są używane do oceny kondycji jednostki zagregowane, niezależnie od tego filtru.

### <a name="api"></a>Interfejs API
Aby uzyskać kondycję wdrożony pakiet usługi przy użyciu interfejsu API, należy utworzyć `FabricClient` i wywołać [GetDeployedServicePackageHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) metody na jego HealthManager. Aby określić następujące parametry opcjonalne, należy użyć [DeployedServicePackageHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
To polecenie cmdlet umożliwiające uzyskanie wdrożonej usługi kondycji pakietu [Get ServiceFabricDeployedServicePackageHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth). Najpierw połącz się z klastrem przy użyciu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) polecenia cmdlet. Aby sprawdzić, gdzie aplikacja jest wdrażana, uruchom [Get ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) i przyjrzyj się wdrożone aplikacje. Aby wyświetlić usługi, które pakiety znajdują się w aplikacji, Przyjrzyj się dzieci pakietu wdrożonej usługi w [Get ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps) danych wyjściowych.

Następujące polecenie cmdlet pobiera kondycję **WordCountServicePkg** pakiet usługi **Service fabric: / WordCount** aplikacja wdrożona na **węzeł _Node_2**. Jednostka ma **System.Hosting** raporty dotyczące pomyślnej aktywacji pakietu usługi i punktu wejścia i rejestracji zakończonych powodzeniem typ usługi.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_2
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131444422267693359
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131444422267903345
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131444422272458374
                             SentAt                : 7/13/2017 5:57:07 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Możesz uzyskać wdrożonej usługi kondycji pakietu z [żądanie GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package) lub [żądania POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) zawierającej zasady dotyczące kondycji, które opisano w treści.

## <a name="health-chunk-queries"></a>Zapytania o kondycję fragmentów
Zapytania o kondycję fragmentów może zwrócić dzieci wielopoziomowe klastra (rekursywnie) na filtry. Obsługuje ona zaawansowane filtry umożliwiające dużą elastyczność przy wyborze elementy podrzędne, które mają zostać zwrócone. Filtry można określić elementy podrzędne, za pomocą unikatowego identyfikatora lub przez inne grupy identyfikatorów i/lub stanów kondycji. Domyślnie żadne elementy podrzędne są uwzględnione, w przeciwieństwie do polecenia kondycji, które zawsze zawierać pierwszy poziom elementów podrzędnych.

[Zapytania o kondycję](service-fabric-view-entities-aggregated-health.md#health-queries) zwraca tylko pierwszy poziom dzieci określonej jednostki na wymagane filtrów. Aby uzyskać elementy podrzędne elementy podrzędne, musi wywołać kondycji dodatkowe interfejsy API dla każdej jednostki zainteresowania. Podobnie Aby uzyskać kondycję konkretnych jednostek, należy wywołać jeden interfejs API kondycji dla każdej odpowiedniej jednostki. Filtrowanie zaawansowane zapytania fragmentów umożliwia zażądanie wielu elementy w jednym zapytaniu, minimalizując rozmiaru wiadomości i liczbę komunikatów.

Wartość kwerendy fragmentów jest, czy stan kondycji można uzyskać więcej jednostek klastra (potencjalnie wszystkie klastra jednostki od wymaganego głównego) w jednym wywołaniu. Zapytanie złożone kondycji można wyrazić takich jak:

* Zwracane tylko aplikacje w wyniku błędu, a w przypadku aplikacji, obejmują wszystkie usługi w ostrzeżenia lub błędu. W przypadku zwracane usługi zawiera wszystkich partycji.
* Zwróć tylko kondycji cztery aplikacje, określonego przez ich nazwy.
* Zwróć tylko kondycję aplikacji typu żądaną aplikację.
* Zwracanie wszystkich wdrożonych jednostek w węźle. Zwraca wszystkie aplikacje, wszystkie wdrożone aplikacje w określonym węźle i wszystkich pakietów wdrożonej usługi w tym węźle.
* Zwróć wszystkie repliki na błąd. Zwraca wszystkie aplikacje, usług, partycji i replik tylko w wyniku błędu.
* Zwróć wszystkie aplikacje. W przypadku określonej usługi zawiera wszystkich partycji.

Obecnie zapytanie fragmentów kondycji jest widoczna tylko dla jednostki klastra. Zwraca fragmentów kondycji klastra, który zawiera:

* Stan kondycji klastra agregowane.
* Lista fragmentów stanu kondycji węzłów, które przestrzegają filtry.
* Lista fragmentów stan kondycji aplikacji, które przestrzegają filtry. Każdy fragment stan kondycji aplikacji znajduje się lista fragmentów w przypadku wszystkich usług, które przestrzegają filtry i listę fragmentów z wszystkich wdrożonych aplikacji, które przestrzegają filtrów. Wartość taka sama dla dzieci, usług i wdrożone aplikacje. Dzięki temu wszystkie jednostki w klastrze mogą być potencjalnie zwracane Jeśli jest to wymagane w hierarchiczny sposób.

### <a name="cluster-health-chunk-query"></a>Zapytanie fragmentów kondycji klastra
Zwraca kondycji jednostki klastra i zawiera fragmenty stanu kondycji hierarchiczne wymagane elementy podrzędne. Dane wejściowe:

* [Opcjonalnie] Zasad dotyczących kondycji klastra używane do oceny, węzły i zdarzenia klastra.
* [Opcjonalnie] Kondycja zasad mapy aplikacji przy użyciu zasad kondycji służy do zastępowania zasad manifestu aplikacji.
* [Opcjonalnie] Filtry dla węzłów i aplikacji, które określają, które wpisy są przedmiotem zainteresowania i ma zostać zwrócone w wyniku. Filtry specyficzne dla/grupę jednostek jednostek lub mają zastosowanie do wszystkich obiektów na tym samym poziomie. Lista filtrów może zawierać jeden filtr ogólne i/lub filtrów dla określonych identyfikatorów obiektów dokładną zwracanych przez zapytanie. W przypadku braku domyślnie nie są zwracane elementy podrzędne.
  Przeczytaj więcej na temat filtrów w [NodeHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.nodehealthstatefilter) i [ApplicationHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthstatefilter). Rekursywnie może filtry aplikacji Określ zaawansowane filtry dla dzieci.

W wyniku fragmentu zawiera elementy podrzędne, które przestrzegają filtrów.

Obecnie zapytanie fragmentów nie zwraca oceny złej kondycji lub jednostek zdarzeń. Te dodatkowe informacje można uzyskać przy użyciu istniejącego zapytania kondycji klastra.

### <a name="api"></a>Interfejs API
Aby uzyskać fragmentów kondycji klastra, należy utworzyć `FabricClient` i wywołać [GetClusterHealthChunkAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) metody na jego **HealthManager**. Możesz przekazać [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) do opisu zasady dotyczące kondycji i filtry zaawansowane.

Poniższy kod umożliwia pobranie fragmentów kondycji klastra z zaawansowanych filtrów.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
To polecenie cmdlet umożliwiające uzyskanie kondycji klastra [Get ServiceFabricClusterChunkHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealthchunk). Najpierw połącz się z klastrem przy użyciu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) polecenia cmdlet.

Poniższy kod pobiera węzły, tylko wtedy, gdy są one w wyniku błędu z wyjątkiem określonego węzła, który zawsze ma zostać zwrócony.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters


HealthState                  : Warning
NodeHealthStateChunks        : 
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

Następujące polecenie cmdlet pobiera fragmentów klastra za pomocą filtrów aplikacji.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks : 
                                TotalCount            : 1

                                ServiceName           : fabric:/WordCount/WordCountService
                                HealthState           : Error
                                PartitionHealthStateChunks : 
                                    TotalCount            : 1

                                    PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                                    HealthState           : Error
                                    ReplicaHealthStateChunks : 
                                        TotalCount            : 5

                                        ReplicaOrInstanceId   : 131444422293118720
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293118721
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293113678
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293113679
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422260002646
                                        HealthState           : Error
```

Następujące polecenie cmdlet zwraca wszystkie jednostki wdrożonej w węźle.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1

                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1

                                    ServiceManifestName   : FAS
                                    ServicePackageActivationId : 
                                    HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1

                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1

                                    ServiceManifestName   : WordCountServicePkg
                                    ServicePackageActivationId : 
                                    HealthState           : Ok
```

### <a name="rest"></a>REST
Możesz uzyskać fragment kondycji klastra z [żądanie GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) lub [żądania POST](https://docs.microsoft.com/rest/api/servicefabric/health-of-cluster) zawierającej zasady dotyczące kondycji i filtrów zaawansowanych, które opisano w treści.

## <a name="general-queries"></a>Ogólne zapytań
Ogólne zapytania zwracają listę jednostek usługi Service Fabric określonego typu. Są one udostępniane za pośrednictwem interfejsu API (za pomocą metod na **FabricClient.QueryManager**), w przypadku poleceń cmdlet programu PowerShell i REST. Te zapytania agregować podzapytania z wielu składników. Jeden z nich jest [magazynu kondycji](service-fabric-health-introduction.md#health-store), która wypełnia zagregowanej kondycji każdego wyniku zapytania.  

> [!NOTE]
> Zapytania ogólne zwracać zagregowaną kondycję jednostki i nie zawierają danych sformatowanego kondycji. Jeśli jednostki nie jest w dobrej kondycji, należy wykonać kolejne czynności przy użyciu zapytań o kondycję można pobrać wszystkie jej informacje o kondycji, w tym zdarzeń, stanów kondycji podrzędnych i oceny złej kondycji.
>
>

Jeśli ogólne kwerend zwraca Nieznana kondycja jednostki, istnieje możliwość, że magazynu kondycji nie ma pełnych danych o tej jednostce. Istnieje również możliwość, że podzapytanie sklepie kondycji podjęta (na przykład, wystąpił błąd komunikacji lub magazynu kondycji została ograniczona). Należy wykonać dalsze czynności przy użyciu zapytania kondycji dla tej jednostki. Jeśli podzapytanie napotkane błędy przejściowe, takie jak problemy z siecią, to zapytanie monitowania może się powieść. Go może również zapewniają więcej szczegółowych informacji z magazynu kondycji o Dlaczego jednostki nie jest uwidaczniana.

Zapytania, które zawierają **HealthState** dla jednostki są:

* Lista węzłów: Zwraca listę węzłów w klastrze (stronicowanej).
  * API: [FabricClient.QueryClient.GetNodeListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * Program PowerShell: Get-ServiceFabricNode
* Lista aplikacji: Zwraca listę wszystkich aplikacji w klastrze (stronicowanej).
  * API: [FabricClient.QueryClient.GetApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * Program PowerShell: Get-ServiceFabricApplication
* Lista usług: Zwraca listę usług w aplikacji (stronicowanej).
  * API: [FabricClient.QueryClient.GetServiceListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * Program PowerShell: Get-ServiceFabricService
* Lista partycji: Zwraca listę wszystkich partycji w usłudze (stronicowanej).
  * API: [FabricClient.QueryClient.GetPartitionListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * Program PowerShell: Get-ServiceFabricPartition
* Lista repliki: Zwraca listę replik partycji (stronicowanej).
  * API: [FabricClient.QueryClient.GetReplicaListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * Program PowerShell: Get-ServiceFabricReplica
* Lista wdrożonej aplikacji: Zwraca listę wszystkich wdrożonych aplikacji w węźle.
  * API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * Program PowerShell: Get-ServiceFabricDeployedApplication
* Lista pakietów wdrożonej usługi: Zwraca listę wszystkich pakietów usługi we wdrożonej aplikacji.
  * API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * Program PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Niektóre z zapytań zwracają stronicowane wyniki. Zwracany te zapytania jest lista pochodzi od [PagedList<T>](https://docs.microsoft.com/dotnet/api/system.fabric.query.pagedlist-1). Jeśli wyniki nie odpowiadają na wiadomości, zwracany jest tylko strony i ContinuationToken śledzi którym wyliczenie jest zatrzymana. W dalszym ciągu wywołanie tego samego zapytania i przekazać token kontynuacji z poprzedniej kwerendy w celu uzyskania wyników dalej.

### <a name="examples"></a>Przykłady
Poniższy kod umożliwia pobranie aplikacji w złej kondycji w klastrze:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Następujące polecenie cmdlet pobiera szczegóły aplikacji dla sieci szkieletowej: / WordCount aplikacji. Zwróć uwagę, że stan kondycji znajduje się na ostrzeżenie.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

Następujące polecenie cmdlet pobiera services ze stanem kondycji błędu:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Error"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Error
```

## <a name="cluster-and-application-upgrades"></a>Uaktualnianie klastra i aplikacji
Podczas uaktualniania monitorowanych klastra i aplikacji usługi Service Fabric umożliwia sprawdzenie kondycji, aby upewnić się, że wszystko, co pozostanie w dobrej kondycji. Jeśli obiekt jest w złej kondycji, ponieważ oceniane przy użyciu zasady dotyczące kondycji skonfigurowanego, uaktualnienie dotyczy zasadom specyficznym dla uaktualnienie, aby określić następne działanie. Może być wstrzymane uaktualnienia. Aby zezwolić na interakcję użytkownika (na przykład naprawianie błędów lub zmiana zasad) lub jej może automatycznie przywrócić wcześniejszą dobrą wersję.

Podczas *klastra* uaktualnienia, możesz uzyskać stan uaktualnienia klastra. Stan uaktualnienia obejmuje oceny złej kondycji, które wskazują, co jest w złej kondycji w klastrze. Jeśli uaktualnienie zostanie wycofana z powodu problemów z kondycją, stan uaktualnienia pamięta ostatniego przyczyny złej kondycji. Te informacje mogą ułatwić administratorom badania, co poszło źle po uaktualniania przywracana lub zatrzymana.

Podobnie podczas *aplikacji* uaktualniania, wszystkie oceny złej kondycji są zawarte w stan uaktualnienia aplikacji.

Poniżej pokazano stan uaktualnienia aplikacji dla zmodyfikowanych Service fabric: / WordCount aplikacji. Strażnika zgłosił błąd na jednym z jego repliki. Uaktualnienie jest stopniowe, ponieważ kontrole kondycji nie są przestrzegane.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2017 5:23:26 PM
FailureTimestampUtc           : 4/21/2017 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Przeczytaj więcej na temat [uaktualnianie aplikacji usługi Service Fabric](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Rozwiązywanie problemów przy użyciu oceny kondycji
Zawsze, gdy występuje problem z klastra lub aplikacji, Przyjrzyj się kondycji klastra lub aplikacji do punktu przyczepienia na czym polega problem. Oceny złej kondycji zawierają szczegółowe informacje dotyczące przyczyny ich wyzwolenia bieżący stan złej kondycji. Jeśli zachodzi potrzeba, można przejść do szczegółów jednostki podrzędne o złej kondycji, aby odkryć ich główną przyczynę.

Na przykład rozważmy złej kondycji aplikację, ponieważ raport o błędach w jednym z jego repliki. Następujące polecenie cmdlet programu Powershell pokazuje oceny złej kondycji:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount -EventsFilter None -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                        Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                        Unhealthy replica: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', ReplicaOrInstanceId='131444422260002646', AggregatedHealthState='Error'.

                                            Error event: SourceId='MyWatchdog', Property='Memory'.

ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : None
```

Można przyjrzeć się replikę tak, aby uzyskać więcej informacji:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricReplicaHealth -ReplicaOrInstanceId 131444422260002646 -PartitionId af2e3e44-a8f8-45ac-9f31-4093eb897600


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Error
UnhealthyEvaluations  : 
                        Error event: SourceId='MyWatchdog', Property='Memory'.

HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : MyWatchdog
                        Property              : Memory
                        HealthState           : Error
                        SequenceNumber        : 131444451657749403
                        SentAt                : 7/13/2017 6:46:05 PM
                        ReceivedAt            : 7/13/2017 6:46:05 PM
                        TTL                   : Infinite
                        Description           : 
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 7/13/2017 6:46:05 PM, LastOk = 1/1/0001 12:00:00 AM
```

> [!NOTE]
> Oceny złej kondycji pokazują, że pierwszym powodem jednostki jest oceniany w celu bieżącego stanu kondycji. Może istnieć wiele zdarzeń, które wyzwalają ten stan, ale nie są odzwierciedlane w ocen. Aby uzyskać więcej informacji, przejść do szczegółów jednostki kondycji, aby ustalić wszystkie raporty złej kondycji w klastrze.
>
>

## <a name="next-steps"></a>Kolejne kroki
[Używanie raportów kondycji systemu do rozwiązywania problemów](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Dodawanie niestandardowych raportów o kondycji usługi Service Fabric](service-fabric-report-health.md)

[Jak raportować i sprawdzać kondycję usługi](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Monitorować i diagnozować usługi lokalnie](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Uaktualnianie aplikacji usługi Service Fabric](service-fabric-application-upgrade.md)
