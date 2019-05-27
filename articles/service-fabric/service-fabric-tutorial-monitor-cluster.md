---
title: Monitorowanie klastra usługi Service Fabric na platformie Azure | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak monitorować klastra przez wyświetlanie zdarzeń usługi Service Fabric, wykonywanie zapytań do interfejsów API bazy danych EventStore, monitorowanie liczników wydajności i wyświetlania raportów o kondycji.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/13/2019
ms.author: srrengar
ms.custom: mvc
ms.openlocfilehash: 9838c6e31e3bb7031d98e615fd96049f22dd8d30
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66158056"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>Samouczek: Monitorowanie klastra usługi Service Fabric na platformie Azure

Monitorowanie i Diagnostyka są kluczowe do programowania, testowania i wdrażania obciążeń w dowolnym środowisku chmury. Ten samouczek to druga część serii część, a następnie pokazano, jak monitorować i diagnozować klaster usługi Service Fabric przy użyciu zdarzenia, liczniki wydajności i raportów o kondycji.   Aby uzyskać więcej informacji, zapoznaj się z omówieniem o [monitorowanie klastrów](service-fabric-diagnostics-overview.md#platform-cluster-monitoring) i [monitorowania infrastruktury](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetl zdarzenia dotyczące usługi Service Fabric
> * Wykonanie kwerendy interfejsów API bazy danych EventStore zdarzenia klastra
> * Monitorowanie infrastruktury/zbieranie liczników wydajności
> * Wyświetlanie raportów o kondycji klastra

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie bezpiecznego [klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) na platformie Azure przy użyciu szablonu
> * Monitorowanie klastra
> * [Skalowanie klastra na zewnątrz lub do wewnątrz](service-fabric-tutorial-scale-cluster.md)
> * [Uaktualnianie środowiska uruchomieniowego klastra](service-fabric-tutorial-upgrade-cluster.md)
> * [Usuwanie klastra](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Zainstaluj [programu Azure Powershell](https://docs.microsoft.com/powershell/azure/install-Az-ps) lub [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* Tworzenie bezpiecznego [klastra Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 
* Instalator [zbieranie diagnostyki](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor) klastra
* Włącz [usługi bazy danych EventStore](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor) w klastrze
* Konfigurowanie [dzienniki usługi Azure Monitor i agenta usługi Log Analytics](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor) klastra

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Wyświetl zdarzenia usługi Service Fabric przy użyciu dzienników usługi Azure Monitor

Dzienniki platformy Azure Monitor zbiera i analizuje dane telemetryczne z aplikacji i usług w chmurze i udostępnia narzędzia do analizy ułatwiają maksymalne wykorzystanie ich dostępności i wydajności. Zapytania można uruchomić w dzienniki usługi Azure Monitor do uzyskiwania szczegółowych informacji i rozwiązywanie problemów z tym, co dzieje się w klastrze.

Aby uzyskać dostęp do rozwiązania analiza usługi Service Fabric, przejdź do [witryny Azure portal](https://portal.azure.com) i wybierz grupę zasobów, w którym utworzono rozwiązania analiza usługi Service Fabric.

Wybierz zasób **ServiceFabric(mysfomsworkspace)**.

W **Przegląd** zobaczysz Kafelki w formie wykresu dla każdego rozwiązania włączone, w tym dla usługi Service Fabric. Kliknij przycisk **usługi Service Fabric** wykres, aby kontynuować do rozwiązania analiza usługi Service Fabric.

![Rozwiązania usługi Service Fabric](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

Na poniższej ilustracji przedstawiono strony głównej rozwiązania analiza usługi Service Fabric. Ta strona główna zawiera widoku migawki, co się dzieje w klastrze.

![Rozwiązania usługi Service Fabric](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 Włączenie diagnostyki po utworzeniu klastra można wyświetlić zdarzenia dla 

* [Zdarzenia klastra usługi Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* [Elementy Reliable Actors zdarzeń modelu programowania](service-fabric-reliable-actors-diagnostics.md)
* [Zdarzeń modelu programowania usług Reliable Services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Oprócz zdarzeń usługi Service Fabric gotowych, bardziej szczegółowe zdarzenia systemowe mogą być zbierane przez [aktualizowanie konfiguracji rozszerzenia diagnostyki](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Wyświetl usługi Service Fabric zdarzenia, w tym akcje w węzłach

Na stronie analiza usługi Service Fabric, kliknij wykres dla **zdarzenia klastra**.  Pojawiają się w dziennikach wszystkie zdarzenia systemowe, które zostały zebrane. Odwołanie, są to aplikacje **WADServiceFabricSystemEventsTable** w usłudze Azure Storage konta i podobnie zdarzenia interfejsu reliable services i actors zostanie wyświetlony obok pochodzą z tych tabel odpowiednich.
    
![Kanał operacyjny zapytania](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

Zapytanie używa języka zapytania Kusto, którą można modyfikować, aby doprecyzować, czego szukasz. Na przykład aby znaleźć wszystkie akcje wykonywane w węzłach w klastrze, można użyć następującego zapytania. Identyfikatory zdarzeń, poniżej znajdują się w [informacje o zdarzeniach kanał operacyjny](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Język zapytania Kusto jest skuteczna. Poniżej przedstawiono niektóre przydatne zapytania.

Tworzenie *ServiceFabricEvent* tabeli odnośników jako funkcja zdefiniowana przez użytkownika, zapisać zapytanie jako funkcję z aliasu ServiceFabricEvent:

```kusto
let ServiceFabricEvent = datatable(EventId: int, EventName: string)
[
    ...
    18603, 'NodeUpOperational',
    18604, 'NodeDownOperational',
    ...
];
ServiceFabricEvent
```

Zarejestrowane w ciągu ostatniej godziny zwracany zdarzenia operacyjne:
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

Zwraca zdarzenia operacyjne przy użyciu EventId == 18604 i EventName == "NodeDownOperational":
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

Zwraca zdarzenia operacyjne przy użyciu EventId == 18604 i EventName == "NodeUpOperational":
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 
 
Zwraca raportów o kondycji z atrybutem HealthState == 3 (błąd) i prowadzenie dodatkowe właściwości pola EventMessage:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| extend HealthStateId = extract(@"HealthState=(\S+) ", 1, EventMessage, typeof(int))
| where TaskName == 'HM' and HealthStateId == 3
| extend SourceId = extract(@"SourceId=(\S+) ", 1, EventMessage, typeof(string)),
         Property = extract(@"Property=(\S+) ", 1, EventMessage, typeof(string)),
         HealthState = case(HealthStateId == 0, 'Invalid', HealthStateId == 1, 'Ok', HealthStateId == 2, 'Warning', HealthStateId == 3, 'Error', 'Unknown'),
         TTL = extract(@"TTL=(\S+) ", 1, EventMessage, typeof(string)),
         SequenceNumber = extract(@"SequenceNumber=(\S+) ", 1, EventMessage, typeof(string)),
         Description = extract(@"Description='([\S\s, ^']+)' ", 1, EventMessage, typeof(string)),
         RemoveWhenExpired = extract(@"RemoveWhenExpired=(\S+) ", 1, EventMessage, typeof(bool)),
         SourceUTCTimestamp = extract(@"SourceUTCTimestamp=(\S+)", 1, EventMessage, typeof(datetime)),
         ApplicationName = extract(@"ApplicationName=(\S+) ", 1, EventMessage, typeof(string)),
         ServiceManifest = extract(@"ServiceManifest=(\S+) ", 1, EventMessage, typeof(string)),
         InstanceId = extract(@"InstanceId=(\S+) ", 1, EventMessage, typeof(string)),
         ServicePackageActivationId = extract(@"ServicePackageActivationId=(\S+) ", 1, EventMessage, typeof(string)),
         NodeName = extract(@"NodeName=(\S+) ", 1, EventMessage, typeof(string)),
         Partition = extract(@"Partition=(\S+) ", 1, EventMessage, typeof(string)),
         StatelessInstance = extract(@"StatelessInstance=(\S+) ", 1, EventMessage, typeof(string)),
         StatefulReplica = extract(@"StatefulReplica=(\S+) ", 1, EventMessage, typeof(string))
```

Zwróć wykres czasu zdarzeń za pomocą EventId! = 17523:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

Uzyskaj usługi Service Fabric zdarzenia operacyjne zagregowane z określonej usługi i węzła:

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

Liczba zdarzeń usługi Service Fabric wg identyfikatora zdarzenia renderowania / EventName przy użyciu zapytania obejmujące wiele zasobów:

```kusto
app('PlunkoServiceFabricCluster').traces
| where customDimensions.ProviderName == 'Microsoft-ServiceFabric'
| extend EventId = toint(customDimensions.EventId), TaskName = tostring(customDimensions.TaskName)
| where EventId != 17523
| join kind=leftouter ServiceFabricEvent on EventId
| extend EventName = case(EventName != '', EventName, 'Undocumented')
| summarize ["Event Count"]= count() by bin(timestamp, 30m), EventName = strcat(tostring(EventId), " - ", EventName)
| render timechart
```

### <a name="view-service-fabric-application-events"></a>Wyświetl zdarzenia dotyczące aplikacji usługi Service Fabric

Można wyświetlać zdarzenia interfejsu reliable services i reliable actors w aplikacji wdrożonych w klastrze.  Na stronie analiza usługi Service Fabric, kliknij wykres na **zdarzenia aplikacji**.

Uruchom następujące zapytanie, aby wyświetlić zdarzenia z aplikacji interfejsu reliable services:
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

Możesz zobaczyć różne zdarzenia dla, gdy runasync usługi zostanie rozpoczęte i zakończone, która zwykle ma miejsce na wdrażania i uaktualniania.

![Usług Reliable Services usługi Service Fabric rozwiązania](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

Możesz również znaleźć zdarzenia dla usługi reliable service za pomocą ServiceName == "Service fabric: / strażnika/WatchdogService":

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
Reliable actors zdarzenia mogą być wyświetlane w podobny sposób:

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
Aby skonfigurować więcej szczegółowych informacji o zdarzeniach do elementów reliable actors, można zmienić `scheduledTransferKeywordFilter` w konfiguracji rozszerzenia diagnostyki w szablonie klastra. Szczegółowe informacje na podstawie wartości dla zostały one [informacje o zdarzeniach elementów reliable actors](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

## <a name="view-performance-counters-with-azure-monitor-logs"></a>Liczniki wydajności widoku przy użyciu dzienników usługi Azure Monitor
Aby wyświetlić liczniki wydajności, przejdź do [witryny Azure portal](https://portal.azure.com) i grupę zasobów, w którym utworzono rozwiązania analiza usługi Service Fabric. 

Wybierz zasób **ServiceFabric(mysfomsworkspace)**, następnie **obszar roboczy usługi Log Analytics**, a następnie **Zaawansowane ustawienia**.

Kliknij przycisk **danych**, następnie kliknij przycisk **liczniki wydajności Windows**. Znajduje się lista domyślne liczniki, które użytkownik może włączyć i interwał dla kolekcji można ustawić także. Można również dodać [dodatkowe liczniki wydajności](service-fabric-diagnostics-event-generation-perf.md) do zbierania. W tym odwołuje się do prawidłowego formatu [artykułu](/windows/desktop/PerfCtrs/specifying-a-counter-path). Kliknij przycisk **Zapisz**, następnie kliknij przycisk **OK**.

Zamknij blok ustawienia zaawansowane i zaznacz **podsumowanie obszaru roboczego** w obszarze **ogólne** nagłówka. Dla każdego z rozwiązań ma włączone jest graficzny kafelka, w tym dla usługi Service Fabric. Kliknij przycisk **usługi Service Fabric** wykres, aby kontynuować do rozwiązania analiza usługi Service Fabric.

Ma graficznego Kafelki kanał operacyjny i zdarzenia interfejsu reliable services. Graficzna reprezentacja danych przepływających w przypadku liczników wybrano, pojawią się w **metryki węzła**. 

Wybierz **metryki kontenera** wykres, aby wyświetlić dodatkowe szczegóły. Można także badać dane licznika wydajności, podobnie jak filtrować dane według węzłów, nazwa licznika wydajności i wartości za pomocą języka zapytania Kusto i zdarzenia klastra.

## <a name="query-the-eventstore-service"></a>Zapytanie usługi bazy danych EventStore
[Usługi bazy danych EventStore](service-fabric-diagnostics-eventstore.md) zapewnia sposób, aby sprawdzić stan klastra lub obciążeń w danym punkcie w czasie. Bazy danych EventStore jest stanowej usługi Service Fabric, która utrzymuje zdarzenia z klastra. Zdarzenia są udostępniane za pośrednictwem [narzędzia Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), REST i interfejsów API. Bazy danych EventStore zapytań klastra bezpośrednio danych diagnostycznych wszystkie jednostki w klastrze, aby zobaczyć pełną listę zdarzeń, które są dostępne w bazie danych EventStore, można znaleźć [zdarzenia usługi Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

Interfejsy API bazy danych EventStore może być odpytywany programowo przy użyciu [biblioteki klienckiej usługi Service Fabric](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Poniżej przedstawiono przykładowe żądanie, wszystkie zdarzenia dla klastra między 2018-04-03T18:00:00Z i 2018 r.-04-04T18:00:00Z za pomocą funkcji GetClusterEventListAsync.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Oto inny przykład, który wysyła zapytanie o kondycji klastra i wszystkich zdarzeń węzła w września 2018 r i wyświetla je.

```csharp
const int timeoutSecs = 60;
var clusterUrl = new Uri(@"http://localhost:19080"); // This example is for a Local cluster
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl);

var clusterHealth = sfhttpClient.Cluster.GetClusterHealthAsync().GetAwaiter().GetResult();
Console.WriteLine("Cluster Health: {0}", clusterHealth.AggregatedHealthState.Value.ToString());


Console.WriteLine("Querying for node events...");
var nodesEvents = sfhttpClient.EventsStore.GetNodesEventListAsync(
    "2018-09-01T00:00:00Z",
    "2018-09-30T23:59:59Z",
    timeoutSecs,
    "NodeDown,NodeUp")
    .GetAwaiter()
    .GetResult()
    .ToList();
Console.WriteLine("Result Count: {0}", nodesEvents.Count());

foreach (var nodeEvent in nodesEvents)
{
    Console.Write("Node event happened at {0}, Node name: {1} ", nodeEvent.TimeStamp, nodeEvent.NodeName);
    if (nodeEvent is NodeDownEvent)
    {
        var nodeDownEvent = nodeEvent as NodeDownEvent;
        Console.WriteLine("(Node is down, and it was last up at {0})", nodeDownEvent.LastNodeUpAt);
    }
    else if (nodeEvent is NodeUpEvent)
    {
        var nodeUpEvent = nodeEvent as NodeUpEvent;
        Console.WriteLine("(Node is up, and it was last down at {0})", nodeUpEvent.LastNodeDownAt);
    }
}
```


## <a name="monitor-cluster-health"></a>Monitorowanie kondycji klastra
Usługa Service Fabric wprowadza [modelu kondycji](service-fabric-health-introduction.md) przy użyciu jednostek kondycji, na którym składników systemu i watchdogs można raportu lokalnego warunki, które są monitorowania. [Magazynu kondycji](service-fabric-health-introduction.md#health-store) agreguje wszystkie dane kondycji, aby określić, czy jednostki są w dobrej kondycji.

Klaster zostanie automatycznie wypełniona raportów o kondycji wysyłane przez składniki systemu. Dowiedz się więcej o [Rozwiązywanie problemów przy użyciu raportów kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Usługa Service Fabric udostępnia zapytań o kondycję dla każdej z obsługiwanych [typów jednostek](service-fabric-health-introduction.md#health-entities-and-hierarchy). Są one dostępne za pośrednictwem interfejsu API, za pomocą metod na [FabricClient.HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), w przypadku poleceń cmdlet programu PowerShell i REST. Te zapytania zwracają kondycji pełne informacje dotyczące jednostki: zagregowanej kondycji, zdarzenia dotyczące kondycji jednostki, kondycja podrzędnych (jeśli ma zastosowanie), oceny złej kondycji (jeśli jednostki nie jest w dobrej kondycji) i statystyki kondycji elementy podrzędne (gdy ma to zastosowanie).

### <a name="get-cluster-health"></a>Pobierz stan klastra
[Polecenia cmdlet Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) zwraca kondycji jednostki klastra i zawiera stany kondycji aplikacji i węzłów (children klastra).  Najpierw należy połączyć się przy użyciu klastra [polecenia cmdlet Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps).

Stan klastra jest 11 węzłów, aplikacja systemowa i Service fabric: / Voting skonfigurowana zgodnie z opisem.

Poniższy przykład pobiera stan klastra przy użyciu domyślnej zasady dotyczące kondycji. 11 węzły są w dobrej kondycji, ale stan kondycji klastra agregowana jest błąd, ponieważ sieci szkieletowej: / aplikacja do głosowania w wyniku błędu. Należy pamiętać o tym, jak oceny złej kondycji zawierają szczegółowe informacje dotyczące warunków, które wyzwolony, zagregowanej kondycji.

```powershell
Get-ServiceFabricClusterHealth

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            33% (1/3) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                          
NodeHealthStates        : 
                          NodeName              : _nt2vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_4
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt3vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_4
                          AggregatedHealthState : Ok
                          
ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok
                          
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
HealthStatistics        : 
                          Node                  : 11 Ok, 0 Warning, 0 Error
                          Replica               : 4 Ok, 0 Warning, 0 Error
                          Partition             : 2 Ok, 0 Warning, 0 Error
                          Service               : 2 Ok, 0 Warning, 0 Error
                          DeployedServicePackage : 3 Ok, 1 Warning, 1 Error
                          DeployedApplication   : 1 Ok, 1 Warning, 1 Error
                          Application           : 0 Ok, 0 Warning, 1 Error
```

Poniższy przykład pobiera kondycji klastra za pomocą zasad niestandardowych aplikacji. Filtruje wyniki, aby pobrać tylko aplikacji i węzły w błąd lub ostrzeżenie. W tym przykładzie żadnych węzłów są zwracane są wszystkie dobrej kondycji. Tylko Service fabric: / aplikacja do głosowania szanuje filtr aplikacji. Ponieważ określa zasady niestandardowe, należy wziąć pod uwagę ostrzeżenia jako błędy w sieci szkieletowej: / aplikacja do głosowania, aplikacja jest oceniane jak błąd i dlatego jest klaster.

```powershell
$appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/Voting"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            100% (5/5) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_2' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2466f2f9-d5fd-410c-a6a4-5b1e00630cca' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376486201388'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_4' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '5faa5201-eede-400a-865f-07f7f886aa32' is in Error.
                          
                                    'System.Hosting' reported Warning for property 'CodePackageActivation:Code:SetupEntryPoint:131959376207396204'. The evaluation treats 
                          Warning as Error.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_0' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '204f1783-f774-4f3a-b371-d9983afaf059' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959375885791093'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt3vm_0' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2533ae95-2d2a-4f8b-beef-41e13e4c0081' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376108346272'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1                         
                          
NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
```

### <a name="get-node-health"></a>Pobierz kondycji węzła
[Polecenia cmdlet Get-ServiceFabricNodeHealth](/powershell/module/servicefabric/get-servicefabricnodehealth) zwraca kondycji jednostki węzła i zawiera zdarzenia kondycji zgłoszone w węźle. Najpierw połącz się z klastrem przy użyciu [polecenia cmdlet Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps). Poniższy przykład pobiera kondycję określonego węzła przy użyciu domyślnej zasady dotyczące kondycji:

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

Poniższy przykład pobiera kondycję wszystkich węzłów w klastrze:
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>Pobierz system usługi kondycji 

Pobierz zagregowaną kondycję usług systemowych:

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetl zdarzenia dotyczące usługi Service Fabric
> * Wykonanie kwerendy interfejsów API bazy danych EventStore zdarzenia klastra
> * Monitorowanie infrastruktury/zbieranie liczników wydajności
> * Wyświetlanie raportów o kondycji klastra

Następnie przejdź do poniższego samouczka, aby dowiedzieć się, jak skalować klaster.
> [!div class="nextstepaction"]
> [Skalowanie klastra](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
