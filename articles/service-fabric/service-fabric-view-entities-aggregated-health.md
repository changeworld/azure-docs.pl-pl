---
title: Jak wyświetlić jednostki sieci szkieletowej usług Azure zagregowane kondycji | Dokumentacja firmy Microsoft
description: Opisuje sposób zapytania, widoków i oceniania kondycji zagregowane jednostki sieci szkieletowej usług Azure, za pomocą zapytań o kondycję i ogólne zapytania.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: ''
ms.assetid: fa34c52d-3a74-4b90-b045-ad67afa43fe5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 2e5d1045edbbc3c71cb0ccff34d2ba327a98a409
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="view-service-fabric-health-reports"></a>Wyświetl raporty dotyczące kondycji sieci szkieletowej usług
Sieć szkieletowa usług Azure wprowadza [model kondycji](service-fabric-health-introduction.md) z jednostek kondycji, na którym składników systemu i watchdogs można raportu lokalnego warunki, które są monitorowania. [Magazynu kondycji](service-fabric-health-introduction.md#health-store) agreguje wszystkie dane kondycji, aby określić, czy jednostki są w dobrej kondycji.

Klaster jest automatycznie wypełniana raportów o kondycji wysyłane przez składniki systemu. Dowiedz się więcej o [użyć raportów o kondycji systemu w celu rozwiązania](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Sieć szkieletowa usług zawiera wiele sposobów uzyskania zagregowane kondycji jednostek:

* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) lub innych narzędzi wizualizacji
* Zapytań o kondycję (za pośrednictwem programu PowerShell, interfejsu API lub REST)
* Ogólne zapytania to zwracany listę obiektów, które mają kondycji jako jedna z właściwości (przy użyciu programu PowerShell, interfejsu API lub REST)

Aby zademonstrować tych opcji, Użyjmy lokalny klaster z pięcioma węzłami i [fabric: / WordCount aplikacji](http://aka.ms/servicefabric-wordcountapp). **Fabric: / WordCount** aplikacji zawiera dwie domyślne usługi, usługi stanowej typu `WordCountServiceType`i usługi bezstanowej typu `WordCountWebServiceType`. Po zmianie `ApplicationManifest.xml` wymagające siedem target replik dla usługi stanowej i jedną partycję. Ponieważ istnieje tylko pięć węzłów w klastrze, składniki systemu raport ostrzeżenie na partycji usługi, ponieważ jest on poniżej docelowa liczba.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Kondycji w narzędziu Service Fabric Explorer
Service Fabric Explorer oferuje czytelny klastra. Na poniższej ilustracji można stwierdzić, że:

* Aplikacja **fabric: / WordCount** jest czerwony (w wyniku błędu), ponieważ ma ona zdarzenie błędu zgłoszony przez **MyWatchdog** dla właściwości **dostępności**.
* Jedna z jego usług **fabric: / WordCount/usługi WordCountService** jest żółty (w ostrzeżenie). Usługa jest skonfigurowana z replikami siedem i klastra ma pięć węzłów, więc nie może zostać umieszczona repicas dwa. Wprawdzie nie pokazano poniżej, partycji usługi jest żółty, ze względu na raport dotyczący systemu z `System.FM` informacją, że `Partition is below target replica or instance count`. Żółty partycji wyzwala żółty usługi.
* Z powodu red aplikacji jest czerwony klastra.

Obliczanie korzysta z domyślnych zasad z manifestu klastra i manifest aplikacji. Są ścisłe zasady i nieodpowiednie jakiekolwiek niepowodzenie.

Widok klastra Service Fabric Explorer:

![Widok klastra z Eksploratora usługi sieć szkieletowa.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Przeczytaj więcej na temat [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Zapytań o kondycję
Usługa sieci szkieletowej udostępnia zapytań o kondycję dla każdej z obsługiwanych [typów jednostek](service-fabric-health-introduction.md#health-entities-and-hierarchy). Są one dostępne za pośrednictwem interfejsu API, przy użyciu metod na [FabricClient.HealthManager](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), poleceń cmdlet programu PowerShell i REST. Te zapytania zwraca pełną kondycję informacje o jednostce: stan kondycji zagregowane, zdarzenia kondycji jednostki, stanów kondycji podrzędnych (jeśli jest to wymagane), zła ocen (jeśli jednostki nie jest w dobrej kondycji) i statystyki kondycji elementy podrzędne (gdy ma zastosowanie).

> [!NOTE]
> Jednostki kondycji jest zwracany, gdy pełni znajduje się w magazynie kondycji. Obiekt musi być aktywne (nie usunięte) i ma raportu system. Jej nadrzędnej jednostki w łańcuchu hierarchii musi mieć również raporty systemu. Jeśli te warunki nie są spełnione, kondycję kwerendy powrotu [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) z [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` który pokazuje, dlaczego nie są zwracane jednostki.
>
>

Zapytań o kondycję musi upłynąć w identyfikator jednostki, który jest zależny od typu jednostki. Zapytania zaakceptować kondycji opcjonalne parametry zasad. Jeśli nie określono żadnych zasad dotyczących kondycji, [zasady dotyczące kondycji](service-fabric-health-introduction.md#health-policies) w manifeście klastra lub aplikacji są używane do oceny. Jeśli manifesty nie zawiera definicji dla zasad dotyczących kondycji, domyślne zasady dotyczące kondycji służą do oceny. Domyślne zasady dotyczące kondycji nieodpowiednie zakończą się niepowodzeniem. Zapytania także zaakceptować filtry dla zwracania tylko częściowe elementy podrzędne lub zdarzenia — te, które przestrzegać określonych filtrów. Inny filtr zezwala, z wyłączeniem statystyki elementów podrzędnych.

> [!NOTE]
> Filtry wyjściowe są stosowane po stronie serwera, zmniejsza rozmiar komunikatu odpowiedzi. Zaleca się, że możesz użyć filtrów danych wyjściowych, aby ograniczyć dane zwrócone, zamiast zastosować filtry po stronie klienta.
>
>

Prawidłowość jednostki zawiera:

* Stan kondycji zagregowane jednostki. Obliczone przez magazynu kondycji na podstawie raportów o kondycji jednostki, stanów kondycji podrzędnych (jeśli jest to wymagane) i zasad dotyczących kondycji. Przeczytaj więcej na temat [oceny kondycji jednostki](service-fabric-health-introduction.md#health-evaluation).  
* Zdarzenia kondycji jednostki.
* Kolekcja stanów kondycji wszystkich elementów podrzędnych dla jednostek, które mogą mieć elementów podrzędnych. Stany kondycji zawiera identyfikatorów jednostki i stan kondycji zagregowanych. Aby uzyskać pełną kondycję dziecka, wywołaj kondycji zapytania dla typu obiektu podrzędnego i podaj identyfikator podrzędnych.
* Zła oceny wskazujące do raportu, która wyzwoliła stanu jednostki, jeśli obiekt nie jest w dobrej kondycji. Oceny są cykliczne zawierające oceny kondycji elementy podrzędne, które wywołały bieżącego stanu kondycji. Na przykład programu alarmowego zgłosił błąd przed repliki. Kondycja aplikacji zawiera ocenę niezdrowy z powodu złej kondycji usługi; Usługa jest niezdrowe, ponieważ partycja w błąd; partycja jest niezdrowe, ponieważ replika błąd; Replika jest niezdrowe, ponieważ raport o kondycji programu alarmowego błędu.
* Statystyki kondycji dla wszystkich elementów podrzędnych typów obiektów, które mają elementy podrzędne. Na przykład stan klastra jest wyświetlana łączna liczba aplikacji, usług, partycji i replik i wdrażane jednostek w klastrze. Kondycja usługi zawiera całkowitą liczbę partycji i replik w obszarze określonej usługi.

## <a name="get-cluster-health"></a>Pobierz stan klastra
Zwraca kondycji jednostki klastra i zawiera stanów kondycji aplikacji i węzły (elementy podrzędne klastra). Dane wejściowe:

* [Opcjonalnie] Zasad dotyczących kondycji klastra używane do analizowania węzły i zdarzenia klastra.
* [Opcjonalnie] Aplikacja kondycji zasad mapy, zasady dotyczące kondycji służy do zastępowania zasad manifestu aplikacji.
* [Opcjonalnie] Filtry dla zdarzeń, węzły i aplikacje, które określają zapisy, które są interesujące i powinny być zwracane w wynikach (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia, węzły i aplikacje są używane do oceny kondycji jednostki zagregowane, niezależnie od tego filtru.
* [Opcjonalnie] Filtr, aby wykluczyć statystyki kondycji.
* [Opcjonalnie] Filtr w celu uwzględnienia fabric: / statystyki kondycji systemu w statystyki kondycji. Dotyczy tylko gdy statystyki kondycji nie są wyłączone. Domyślnie statystyki kondycji zawierają tylko statystyki dla użytkownika aplikacji i nie aplikacji systemowej.

### <a name="api"></a>Interfejs API
Aby uzyskać stan klastra, Utwórz `FabricClient` i Wywołaj [GetClusterHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) metody na jego **HealthManager**.

Następujące wywołanie pobiera stan klastra:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

Poniższy kod pobiera stan klastra przy użyciu zasad kondycji niestandardowych klastra i filtry dla węzłów i aplikacji. Określa, że statystyki kondycji obejmuje sieci szkieletowej: / statystyk systemu. Tworzy [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthquerydescription), który zawiera informacje wejściowe.

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
Polecenie cmdlet, aby pobrać stan klastra jest [Get-ServiceFabricClusterHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealth). Po pierwsze, połącz się z klastrem przy użyciu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) polecenia cmdlet.

Stan klastra jest pięć węzłów, aplikacja systemu i sieci szkieletowej: / WordCount skonfigurowane zgodnie z opisem.

Następujące polecenie cmdlet pobiera stan klastra za pomocą domyślnych zasad kondycji. Ostrzeżenie zagregowane kondycji, ponieważ sieci szkieletowej: / WordCount aplikacja jest ostrzeżenie. Należy zwrócić uwagę, jak zła oceny zawierają szczegółowe informacje dotyczące warunków, które wywołały zagregowane kondycji.

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

Następujące polecenie cmdlet programu PowerShell pobiera kondycji klastra za pomocą zasad niestandardowych aplikacji. Filtruje wyniki można uzyskać tylko aplikacji i węzły w błąd lub ostrzeżenie. W związku z tym żadnych węzłów są zwracane, ponieważ są one wszystkich dobrej kondycji. Tylko fabric: / aplikacji WordCount szanuje filtru aplikacji. Ponieważ zasady niestandardowe określa wziąć pod uwagę ostrzeżenia jako błędy sieci szkieletowej: / aplikacji WordCount, aplikacja jest oceniany, tak jak błąd, a więc klastra.

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
Możesz uzyskać kondycji klastra z [żądania GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster) lub [żądania POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) zawierającej zasady dotyczące kondycji opisanego w treści.

## <a name="get-node-health"></a>Pobierz węzeł kondycji
Zwraca kondycji jednostek node i zawiera zdarzenia kondycji zgłoszone w węźle. Dane wejściowe:

* [Wymagane] Nazwa węzła, który identyfikuje węzeł.
* [Opcjonalnie] Ustawienia zasad kondycji klastra używane do oceny kondycji.
* [Opcjonalnie] Filtry dla zdarzeń, które określają zapisy, które są interesujące i powinny być zwracane w wynikach (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia są używane do oceny kondycji jednostki zagregowane, niezależnie od tego filtru.

### <a name="api"></a>Interfejs API
Aby uzyskać węzła kondycji za pomocą interfejsu API, Utwórz `FabricClient` i Wywołaj [GetNodeHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) metody na jego HealthManager.

Poniższy kod pobiera kondycji węzła nazwę określonego węzła:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

Poniższy kod pobiera kondycji węzła nazwę określonego węzła i przekazuje filtr zdarzeń i niestandardowych zasad za pośrednictwem [NodeHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.nodehealthquerydescription):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
To polecenie cmdlet, aby pobrać kondycji węzła [Get-ServiceFabricNodeHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnodehealth). Po pierwsze, połącz się z klastrem przy użyciu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) polecenia cmdlet.
Następujące polecenie cmdlet pobiera kondycji węzłów za pomocą domyślnych zasad kondycji:

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
Możesz uzyskać kondycji węzła z [żądania GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node) lub [żądania POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) zawierającej zasady dotyczące kondycji opisanego w treści.

## <a name="get-application-health"></a>Pobierz kondycji aplikacji
Zwraca kondycji jednostki aplikacji. Zawiera ona stanów kondycji wdrożonej aplikacji i usług dzieci. Dane wejściowe:

* [Wymagane] Nazwa aplikacji (URI), który identyfikuje aplikację.
* [Opcjonalnie] Zasady kondycji aplikacji służy do zastępowania zasad manifestu aplikacji.
* [Opcjonalnie] Filtry dla zdarzeń, usług i wdrożone aplikacje, które określają zapisy, które są interesujące i powinny być zwracane w wynikach (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia, usługi i wdrożone aplikacje są używane do oceny kondycji jednostki zagregowane, niezależnie od tego filtru.
* [Opcjonalnie] Filtr, aby wykluczyć statystyki kondycji. Jeśli nie zostanie określony, statystyki kondycji obejmują ok, ostrzeżenia i liczby błędów dla wszystkich aplikacji elementów podrzędnych: usług, partycji, repliki, wdrożonych aplikacji i wdrożonych pakietów usługi.

### <a name="api"></a>Interfejs API
Aby uzyskać kondycji aplikacji, należy utworzyć `FabricClient` i Wywołaj [GetApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) metody na jego HealthManager.

Poniższy kod pobiera kondycji aplikacji dla nazwy określonej aplikacji (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

Poniższy kod pobiera kondycji aplikacji dla nazwy określonej aplikacji (URI) z filtrami i niestandardowych zasad określona za pomocą [ApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.applicationhealthquerydescription).

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
To polecenie cmdlet, aby pobrać kondycji aplikacji [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps). Po pierwsze, połącz się z klastrem przy użyciu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) polecenia cmdlet.

Następujące polecenie cmdlet zwraca kondycji **fabric: / WordCount** aplikacji:

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

Przekazuje następującego polecenia cmdlet programu PowerShell w niestandardowych zasad. Filtruje także elementów podrzędnych i zdarzeń.

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
Możesz uzyskać kondycji aplikacji z [żądania GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application) lub [żądania POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) zawierającej zasady dotyczące kondycji opisanego w treści.

## <a name="get-service-health"></a>Pobierz usługi kondycji
Zwraca kondycji jednostki usługi. Zawiera ona stanów kondycji partycji. Dane wejściowe:

* [Wymagane] Nazwa usługi (URI), który identyfikuje usługę.
* [Opcjonalnie] Zasady kondycji aplikacji służy do zastępowania zasad manifestu aplikacji.
* [Opcjonalnie] Filtry dla zdarzeń i partycje, które określają zapisy, które są interesujące i powinny być zwracane w wynikach (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia i partycji są używane do oceny kondycji jednostki zagregowane, niezależnie od tego filtru.
* [Opcjonalnie] Filtr, aby wykluczyć statystyki kondycji. Jeśli nie jest określony, statystyki kondycji Pokaż ok, ostrzeżenie, i liczby błędów dla wszystkich partycji i replik usługi.

### <a name="api"></a>Interfejs API
Aby uzyskać kondycja usługi za pomocą interfejsu API, Utwórz `FabricClient` i Wywołaj [GetServiceHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) metody na jego HealthManager.

Poniższy przykład pobiera kondycji usługi o nazwie określonej usługi (URI):

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

Poniższy kod pobiera kondycja usługi dla określonej nazwy usługi (URI), filtrów i zasad niestandardowych za pomocą [ServiceHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicehealthquerydescription):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
To polecenie cmdlet, aby pobrać kondycja usługi [Get-ServiceFabricServiceHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicehealth). Po pierwsze, połącz się z klastrem przy użyciu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) polecenia cmdlet.

Następujące polecenie cmdlet pobiera kondycja usługi za pomocą domyślnych zasad kondycji:

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
Można pobrać usługi kondycji z [żądania GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service) lub [żądania POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) zawierającej zasady dotyczące kondycji opisanego w treści.

## <a name="get-partition-health"></a>Pobierz kondycji partycji
Zwraca kondycji jednostki partycji. Zawiera ona stanów kondycji repliki. Dane wejściowe:

* [Wymagane] Partycja identyfikator (GUID), który identyfikuje partycji.
* [Opcjonalnie] Zasady kondycji aplikacji służy do zastępowania zasad manifestu aplikacji.
* [Opcjonalnie] Filtry dla zdarzeń i replik, które określają zapisy, które są interesujące i powinny być zwracane w wynikach (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia i repliki są używane do oceny kondycji jednostki zagregowane, niezależnie od tego filtru.
* [Opcjonalnie] Filtr, aby wykluczyć statystyki kondycji. Jeśli nie zostanie określony, statystyki kondycji pokazują, jak wiele repliki są w ok, ostrzeżenia i błędu stanów.

### <a name="api"></a>Interfejs API
Aby uzyskać partycji kondycji za pomocą interfejsu API, Utwórz `FabricClient` i Wywołaj [GetPartitionHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) metody na jego HealthManager. Aby określić następujące parametry opcjonalne, utworzyć [PartitionHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.partitionhealthquerydescription).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
To polecenie cmdlet, aby pobrać kondycji partycji [Get-ServiceFabricPartitionHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricpartitionhealth). Po pierwsze, połącz się z klastrem przy użyciu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) polecenia cmdlet.

Następujące polecenie cmdlet pobiera kondycji dla wszystkich partycji **fabric: / WordCount/usługi WordCountService** usługi i odfiltrowuje repliki stanów kondycji:

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
Możesz uzyskać kondycji partycji z [żądania GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition) lub [żądania POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) zawierającej zasady dotyczące kondycji opisanego w treści.

## <a name="get-replica-health"></a>Pobierz kondycji repliki
Zwraca kondycji repliki usługi stanowej lub wystąpienie usługi bezstanowej. Dane wejściowe:

* [Wymagane] Identyfikator (GUID) i repliki Identyfikatora partycji identyfikujący repliki.
* [Opcjonalnie] Parametry zasady kondycji aplikacji służy do zastępowania zasad manifestu aplikacji.
* [Opcjonalnie] Filtry dla zdarzeń, które określają zapisy, które są interesujące i powinny być zwracane w wynikach (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia są używane do oceny kondycji jednostki zagregowane, niezależnie od tego filtru.

### <a name="api"></a>Interfejs API
Aby uzyskać kondycji repliki za pomocą interfejsu API, Utwórz `FabricClient` i Wywołaj [GetReplicaHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) metody na jego HealthManager. Aby określić parametry zaawansowane, użyj [ReplicaHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.replicahealthquerydescription).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
To polecenie cmdlet, aby pobrać kondycji repliki [Get-ServiceFabricReplicaHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricreplicahealth). Po pierwsze, połącz się z klastrem przy użyciu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) polecenia cmdlet.

Następujące polecenie cmdlet pobiera kondycji repliką podstawową dla wszystkich partycji usługi:

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
Możesz uzyskać kondycji repliki z [żądania GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica) lub [żądania POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) zawierającej zasady dotyczące kondycji opisanego w treści.

## <a name="get-deployed-application-health"></a>Pobierz kondycji wdrożonej aplikacji
Zwraca kondycji aplikacji wdrożone w ramach jednostki węzła. Zawiera ona stanów kondycji pakietu wdrożonej usługi. Dane wejściowe:

* [Wymagane] Nazwa aplikacji (URI) i nazwę węzła (ciąg) identyfikujących wdrożonej aplikacji.
* [Opcjonalnie] Zasady kondycji aplikacji służy do zastępowania zasad manifestu aplikacji.
* [Opcjonalnie] Filtry dla zdarzeń i wdrożone pakiety usługi określające zapisy, które są interesujące i powinny być zwracane w wynikach (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia i wdrożone pakiety usługi są używane do oceny kondycji jednostki zagregowane, niezależnie od tego filtru.
* [Opcjonalnie] Filtr, aby wykluczyć statystyki kondycji. Jeśli nie zostanie określony, statystyki kondycji Pokaż liczbę wdrożone pakiety usługi ok, ostrzeżenia i błędu stany kondycji.

### <a name="api"></a>Interfejs API
Aby uzyskać kondycję aplikacji wdrożonych na węźle, za pośrednictwem interfejsu API, Utwórz `FabricClient` i Wywołaj [GetDeployedApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) metody na jego HealthManager. Aby określić następujące parametry opcjonalne, użyj [DeployedApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
To polecenie cmdlet, aby pobrać kondycji wdrożonej aplikacji [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps). Po pierwsze, połącz się z klastrem przy użyciu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) polecenia cmdlet. Aby dowiedzieć się, gdy aplikacja jest wdrażana, uruchom [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) i przyjrzyj się dzieci wdrożonej aplikacji.

Następujące polecenie cmdlet pobiera kondycji **fabric: / WordCount** aplikacji wdrożonych na **to węzeł _Node_2**.

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
Możesz uzyskać kondycji wdrożonej aplikacji z [żądania GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application) lub [żądania POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) zawierającej zasady dotyczące kondycji opisanego w treści.

## <a name="get-deployed-service-package-health"></a>Pobierz kondycji pakietu wdrożonej usługi
Zwraca kondycji jednostki pakietu wdrożonej usługi. Dane wejściowe:

* [Wymagane] Nazwa aplikacji (URI), nazwę węzła (ciąg) i nazwa manifestu usługi (ciąg) identyfikujących pakietu wdrożonej usługi.
* [Opcjonalnie] Zasady kondycji aplikacji służy do zastępowania zasad manifestu aplikacji.
* [Opcjonalnie] Filtry dla zdarzeń, które określają zapisy, które są interesujące i powinny być zwracane w wynikach (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia są używane do oceny kondycji jednostki zagregowane, niezależnie od tego filtru.

### <a name="api"></a>Interfejs API
Aby uzyskać kondycji pakietu wdrożonej usługi za pomocą interfejsu API, Utwórz `FabricClient` i Wywołaj [GetDeployedServicePackageHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) metody na jego HealthManager. Aby określić następujące parametry opcjonalne, użyj [DeployedServicePackageHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
Polecenie cmdlet, aby pobrać pakiet wdrożonej usługi kondycji jest [Get-ServiceFabricDeployedServicePackageHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth). Po pierwsze, połącz się z klastrem przy użyciu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) polecenia cmdlet. Aby sprawdzić, gdy aplikacja jest wdrażana, uruchom [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) i przyjrzyj się wdrożone aplikacje. Aby wyświetlić usługi, które pakiety znajdują się w aplikacji, obejrzyj dzieci pakietu wdrożonej usługi w [Get ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps) danych wyjściowych.

Następujące polecenie cmdlet pobiera kondycji **WordCountServicePkg** pakiet usługi **fabric: / WordCount** aplikacji wdrożonych na **to węzeł _Node_2**. Jednostka ma **System.Hosting** raporty dla pomyślnej aktywacji w pakiecie usługi i punktu wejścia i pomyślną rejestrację typu usługi.

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
Możesz uzyskać wdrożonej usługi kondycji pakietu z [żądania GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package) lub [żądania POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) zawierającej zasady dotyczące kondycji opisanego w treści.

## <a name="health-chunk-queries"></a>Fragmentu zapytań o kondycję
Zapytania fragmentu kondycji mogą zwracać elementy podrzędne (rekursywnie), wielopoziomowe klastra na filtry. Obsługuje ona filtrów zaawansowanych, które umożliwiają dużą elastyczność w wyborze dzieci, który ma zostać zwrócona. Filtry można określić elementy podrzędne, za pomocą unikatowego identyfikatora lub przez inne grupy identyfikatorów i/lub stanów kondycji. Domyślnie bez żadnych elementów podrzędnych są uwzględnione, zamiast polecenia kondycji, które zawsze należy uwzględniać pierwszego poziomu elementów podrzędnych.

[Zapytań o kondycję](service-fabric-view-entities-aggregated-health.md#health-queries) zwracać tylko pierwszy poziom dzieci określonej jednostki na wymagane filtrów. Można uzyskać elementów podrzędnych elementu podrzędnego, należy wywołać kondycji dodatkowe interfejsy API dla każdej jednostki zainteresowań. Podobnie Aby uzyskać kondycji konkretnych obiektów, należy wywołać kondycji jednego interfejsu API dla każdej żądanej jednostki. Zapytanie fragmentu zaawansowane filtrowanie umożliwia zażądanie wielu elementy w jednym zapytaniu, minimalizując rozmiaru wiadomości i liczby wiadomości.

Wartość zapytania fragmentu jest, że wyświetlić stan kondycji dla kolejnych jednostek klastra (potencjalnie wszystkich klastra jednostek zaczynając od wymaganego głównego) w jednym wywołaniu. Można wyrazić zapytania kondycji złożonych takich jak:

* Zwracany aplikacji tylko w błąd, a w przypadku aplikacji obejmują wszystkie usługi w ostrzeżenia lub błędu. W przypadku zwróconych usług zawiera wszystkie partycje.
* Zwróć tylko kondycję cztery aplikacji, określonych przez ich nazwy.
* Zwróć tylko kondycję aplikacji typu żądanej aplikacji.
* Zwraca wszystkie wdrożone jednostek w węźle. Zwraca wszystkie aplikacje, wszystkie wdrażane aplikacje na określony węzeł i wszystkie pakiety wdrożonej usługi na tym węźle.
* Zwróć wszystkie repliki na błąd. Zwraca wszystkich aplikacji, usług, partycji i replik tylko z błędami.
* Zwróć wszystkie aplikacje. Dla określonej usługi należy wprowadzić wszystkie partycje.

Obecnie fragmentu zapytania kondycji jest widoczne tylko dla obiektu klastra. Zwraca fragmentu kondycji klastra, który zawiera:

* Stan kondycji jest agregowana w klastrze.
* Lista fragmentu stanu kondycji węzłów, które przestrzegać filtry.
* Lista fragmentu stan kondycji aplikacji, których przestrzeganie filtry. Każdego fragmentu stan kondycji aplikacji znajduje się lista fragmentu ze wszystkich usług, których przestrzeganie filtry i listy fragmentu z wszystkich wdrożonych aplikacji, które przestrzegać filtrów. Wartość taka sama dla dzieci, usług i wdrożone aplikacje. Dzięki temu wszystkie jednostki w klastrze może być potencjalnie zwracany żądanie w hierarchiczny sposób.

### <a name="cluster-health-chunk-query"></a>Klaster kondycji fragmentu zapytania
Zwraca kondycji jednostki klastra i zawiera fragmenty stan kondycji hierarchiczna wymagane elementy podrzędne. Dane wejściowe:

* [Opcjonalnie] Zasad dotyczących kondycji klastra używane do analizowania węzły i zdarzenia klastra.
* [Opcjonalnie] Aplikacja kondycji zasad mapy, zasady dotyczące kondycji służy do zastępowania zasad manifestu aplikacji.
* [Opcjonalnie] Filtry dla węzłów i aplikacje, które określają zapisy, które są interesujące i powinny być zwracane w wynikach. Filtry są specyficzne dla jednostki/grupy jednostek lub mają zastosowanie do wszystkich jednostek na tym poziomie. Lista filtrów może zawierać jeden filtr ogólne i/lub filtrów dla określonych identyfikatorów dla jednostek szczegółowe dzielenie zwróconych przez kwerendę. W przypadku braku domyślnie nie są zwracane elementy podrzędne.
  Przeczytaj więcej na temat filtrów w [NodeHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.nodehealthstatefilter) i [ApplicationHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthstatefilter). Rekursywnie może filtrów aplikacji określ filtry zaawansowane podrzędnych.

Wynik fragmentu zawiera elementy podrzędne, które przestrzegać filtrów.

Obecnie zapytania fragmentu nie zwraca zła ocen lub zdarzeń jednostki. Dodatkowe informacje można uzyskać przy użyciu istniejącego zapytania kondycji klastra.

### <a name="api"></a>Interfejs API
Aby uzyskać fragmentu kondycji klastra, Utwórz `FabricClient` i Wywołaj [GetClusterHealthChunkAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) metody na jego **HealthManager**. Można przekazać [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) do opisu zasady dotyczące kondycji i filtry zaawansowane.

Poniższy kod pobiera fragmentu kondycji klastra z filtry zaawansowane.

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
Polecenie cmdlet, aby pobrać stan klastra jest [Get-ServiceFabricClusterChunkHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealthchunk). Po pierwsze, połącz się z klastrem przy użyciu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) polecenia cmdlet.

Poniższy kod pobiera węzły tylko wtedy, gdy są one błąd z wyjątkiem określonego węzła zawsze ma zostać zwrócony.

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

Następujące polecenie cmdlet pobiera fragmentu klastra z filtrami aplikacji.

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
Możesz uzyskać fragment kondycji klastra z [żądania GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) lub [żądania POST](https://docs.microsoft.com/rest/api/servicefabric/health-of-cluster) zawierającej zasady dotyczące kondycji i filtry zaawansowane opisanego w treści.

## <a name="general-queries"></a>Ogólne zapytań
Ogólne kwerendy zwracają listę jednostki sieci szkieletowej usług określonego typu. Są one dostępne za pośrednictwem interfejsu API (za pomocą metod na **FabricClient.QueryManager**), polecenia cmdlet programu PowerShell i REST. Te zapytania agregować podzapytania z wielu składników. Jeden z nich jest [magazynu kondycji](service-fabric-health-introduction.md#health-store), który wypełnia stan kondycji zagregowane dla każdego wyniku zapytania.  

> [!NOTE]
> Ogólne zapytania zwracać zagregowane kondycja jednostki i nie zawierają danych sformatowanego kondycji. Jeśli jednostka nie jest w dobrej kondycji, można odpowiednio zareagować z zapytania kondycji, aby uzyskać wszystkie jego informacje kondycji, w tym zdarzenia, stanów kondycji podrzędnych i oceny złej kondycji.
>
>

Jeśli ogólne kwerend zwraca nieznany kondycja jednostki, jest to możliwe, że magazynu kondycji nie ma pełnych danych o tej jednostce. Istnieje również możliwość, że podzapytania w magazynie kondycji nie powiodło się (na przykład wystąpił błąd komunikacji lub został ograniczony magazynu kondycji). Dostosuj się zapytania kondycji dla jednostki. Jeśli podzapytanie napotkała błąd przejściowy, takie jak problemy z siecią, to zapytanie kolejnych może się powieść. Go może także zapewniają więcej szczegółowych informacji z magazynu kondycji o Dlaczego jednostki nie jest widoczne.

Zapytania, które zawierają **HealthState** dla jednostki to:

* Listy węzłów: zwraca listy węzłów w klastrze (stronicowanej).
  * API: [FabricClient.QueryClient.GetNodeListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * PowerShell: Get-ServiceFabricNode
* Lista aplikacji: zwraca listę aplikacji w klastrze (stronicowanej).
  * API: [FabricClient.QueryClient.GetApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * PowerShell: Get-ServiceFabricApplication
* Lista usług: zwraca listę usług w aplikacji (stronicowanej).
  * API: [FabricClient.QueryClient.GetServiceListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * PowerShell: Get-ServiceFabricService
* Lista partycji: zwraca listę partycji w usłudze (stronicowanej).
  * API: [FabricClient.QueryClient.GetPartitionListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * PowerShell: Get-ServiceFabricPartition
* Listy replik: zwraca listę replik partycji (stronicowanej).
  * API: [FabricClient.QueryClient.GetReplicaListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * PowerShell: Get-ServiceFabricReplica
* Wdrożone listy aplikacji: zwraca listę wdrożonych aplikacji w węźle.
  * API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication
* Wdrożone usługi listy pakietów: zwraca listę pakietów usług we wdrożonej aplikacji.
  * API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Niektóre z zapytań Zwróć wyników stronicowania. Powrót z tych kwerend znajduje się lista pochodzące z [PagedList<T>](https://docs.microsoft.com/dotnet/api/system.fabric.query.pagedlist-1). Jeśli wyniki nie pasują do wiadomości, jest zwracana tylko stronę i ContinuationToken śledzi gdzie wyliczenie zatrzymana. Nadal wywołanie tej samej kwerendy i przekaż token kontynuacji z poprzedniej kwerendy, aby uzyskać wyniki dalej.
>
>

### <a name="examples"></a>Przykłady
Poniższy kod pobiera złej kondycji aplikacji w klastrze:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Następujące polecenie cmdlet pobiera szczegółów aplikacji w sieci szkieletowej: / WordCount aplikacji. Należy zauważyć, że stan kondycji jest ostrzeżenie.

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

Następujące polecenie cmdlet pobiera usług o stanie kondycji błędu:

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

## <a name="cluster-and-application-upgrades"></a>Uaktualnienia klastra i aplikacji
Podczas uaktualniania monitorowanych klastra i aplikacji sieci szkieletowej usług umożliwia sprawdzenie kondycji, aby zapewnić wszystko działa prawidłowo. Jeśli jednostki jest nieprawidłowy, ponieważ oceniane przy użyciu zasad dotyczących kondycji skonfigurowanego, uaktualnienie ma zastosowanie zasad dla konkretnych uaktualnienia ustalenie następnej akcji. Uaktualnienia może być wstrzymane. Aby zezwolić na interakcję użytkownika (na przykład poprawki błędów lub zmiany zasad) lub może automatycznie przywracać stanu sprzed dobrej poprzedniej wersji.

Podczas *klastra* uaktualnienia, możesz uzyskać stan uaktualnienia klastra. Stan uaktualnienia obejmuje ocen złej kondycji, które wskaż co to jest w złej kondycji w klastrze. Jeśli uaktualnianie zostanie wycofana z powodu problemów kondycji, stan uaktualnienia pamięta ostatniego przyczyny złej kondycji. Te informacje mogą pomóc Administratorzy zbadać, co poszło źle po uaktualnieniu z powrotem obniżyć lub zatrzymana.

Podobnie podczas *aplikacji* uaktualnienia, wszelkie nieprawidłowości oceny są zawarte w stan uaktualnienia aplikacji.

Poniżej pokazano stanu uaktualniania aplikacji dla zmodyfikowanych sieci szkieletowej: / WordCount aplikacji. Programu alarmowego zgłosił błąd na jednym z jego repliki. Uaktualnianie jest stopniowych, ponieważ kontroli kondycji nie są przestrzegane.

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

Przeczytaj więcej na temat [uaktualniania aplikacji sieci szkieletowej usług](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Rozwiązywanie problemów przy użyciu oceny kondycji
Zawsze, gdy występuje problem z klastra lub aplikacji, obejrzyj kondycji klastra lub aplikacji w celu określenia, co jest nieprawidłowe. Zła oceny zawierają szczegółowe informacje o co wyzwoliło bieżący stan złej kondycji. Jeśli zajdzie potrzeba, użytkownik może przejść do jednostek podrzędne o złej kondycji, aby zidentyfikować przyczynę.

Rozważmy na przykład aplikacji nieprawidłowy, ponieważ raport o błędach na jednym z jego repliki. Następujące polecenie cmdlet programu Powershell pokazuje ocen złej kondycji:

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
> Zła ocen Pokaż pierwsze urząd jednostki jest obliczane do bieżącego stanu kondycji. Może istnieć wiele zdarzeń wyzwalających ten stan, ale są one nie zostać zawarte w oceny. Aby uzyskać więcej informacji, przejdź do szczegółów jednostki kondycji, aby poznać wszystkie raporty złej kondycji w klastrze.
>
>

## <a name="next-steps"></a>Kolejne kroki
[Używanie raportów kondycji systemu do rozwiązywania problemów](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Dodawanie niestandardowych raportów kondycji sieci szkieletowej usług](service-fabric-report-health.md)

[Jak zgłosić i Sprawdź kondycję usług](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Monitorowanie i diagnozowania usług lokalnie](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Uaktualnianie aplikacji sieci szkieletowej usług](service-fabric-application-upgrade.md)
