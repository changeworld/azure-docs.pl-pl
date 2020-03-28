---
title: Monitorowanie klastra sieci szkieletowej usług na platformie Azure
description: W tym samouczku dowiesz się, jak monitorować klaster, wyświetlając zdarzenia sieci szkieletowej usług, odpytując interfejsy API eventstore, monitorując liczniki kondycji i wyświetlając raporty dotyczące kondycji.
author: srrengar
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: srrengar
ms.custom: mvc
ms.openlocfilehash: ab58d622511e0d5793eb6df312bc3fd6dd15bfd6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75376634"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>Samouczek: Monitorowanie klastra sieci szkieletowej usług na platformie Azure

Monitorowanie i diagnostyka mają kluczowe znaczenie dla tworzenia, testowania i wdrażania obciążeń w dowolnym środowisku chmury. Ten samouczek jest częścią drugiej serii i pokazuje, jak monitorować i diagnozować klaster sieci szkieletowej usług przy użyciu zdarzeń, liczników wydajności i raportów kondycji.   Aby uzyskać więcej informacji, przeczytaj omówienie [monitorowania klastra](service-fabric-diagnostics-overview.md#platform-cluster-monitoring) i [monitorowania infrastruktury](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring).

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetlanie zdarzeń sieci szkieletowej usług
> * Kwerendy eventstore interfejsów API dla zdarzeń klastra
> * Monitorowanie infrastruktury/zbieranie liczników perf
> * Wyświetlanie raportów kondycji klastra

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

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Zainstaluj [usługę Azure Powershell](https://docs.microsoft.com/powershell/azure/install-Az-ps) lub [platformę Azure CLI](/cli/azure/install-azure-cli).
* Tworzenie bezpiecznego [klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 
* Kolekcja [diagnostyki instalacji](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor) dla klastra
* Włączanie [usługi EventStore](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor) w klastrze
* Konfigurowanie [dzienników usługi Azure Monitor i agenta usługi Log Analytics](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor) dla klastra

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Wyświetlanie zdarzeń sieci szkieletowej usług przy użyciu dzienników usługi Azure Monitor

Dzienniki usługi Azure Monitor zbierają i analizują dane telemetryczne z aplikacji i usług hostowanych w chmurze i udostępniają narzędzia do analizy, które pomagają zmaksymalizować ich dostępność i wydajność. Można uruchamiać kwerendy w dziennikach usługi Azure Monitor, aby uzyskać szczegółowe informacje i rozwiązać problemy z tym, co dzieje się w klastrze.

Aby uzyskać dostęp do rozwiązania usługi Analizy sieci szkieletowej, przejdź do [witryny Azure portal](https://portal.azure.com) i wybierz grupę zasobów, w której utworzono rozwiązanie analizy sieci szkieletowej usług.

Wybierz zasób **ServiceFabric(mysfomsworkspace).**

W **przeglądzie** widać kafelki w postaci wykresu dla każdego z włączonych rozwiązań, w tym jednego dla sieci szkieletowej usług. Kliknij wykres **sieci szkieletowej usług,** aby przejść do rozwiązania analizy sieci szkieletowej usług.

![Rozwiązanie sieci szkieletowej usług](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

Na poniższej ilustracji przedstawiono stronę główną rozwiązania usługi Analizy sieci szkieletowej. Ta strona główna zawiera widok migawki tego, co dzieje się w klastrze.

![Rozwiązanie sieci szkieletowej usług](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 Jeśli diagnostyka została włączona podczas tworzenia klastra, można zobaczyć zdarzenia 

* [Zdarzenia klastra sieci szkieletowej usług](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors modelowanie zdarzeń modelu](service-fabric-reliable-actors-diagnostics.md)
* [Zdarzenia modelu programowania niezawodnych usług](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Oprócz zdarzeń sieci szkieletowej usług po wyjęciu z pudełka, bardziej szczegółowe zdarzenia systemowe mogą być zbierane przez [aktualizację konfiguracji rozszerzenia diagnostyki](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Wyświetlanie zdarzeń sieci szkieletowej usług, w tym akcji w węzłach

Na stronie Analiza sieci szkieletowej usług kliknij wykres **zdarzeń klastra**.  Pojawią się dzienniki dla wszystkich zdarzeń systemowych, które zostały zebrane. Dla odwołania są one z **WADServiceFabricSystemEventsTable** na koncie usługi Azure Storage i podobnie niezawodne usługi i zdarzenia podmiotów, które widzisz dalej są z tych odpowiednich tabel.
    
![Zapytanie kanał operacyjny](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

Kwerenda używa języka zapytania Kusto, który można zmodyfikować, aby uściślić to, czego szukasz. Na przykład, aby znaleźć wszystkie akcje podjęte w węzłach w klastrze, można użyć następującej kwerendy. Identyfikatory zdarzeń użyte poniżej znajdują się w [odwołaniu do zdarzeń kanału operacyjnego](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Język zapytania Kusto jest potężny. Oto kilka innych przydatnych zapytań.

Utwórz tabelę odnośnych *ServiceFabricEvent* jako funkcję zdefiniowaną przez użytkownika, zapisując kwerendę jako funkcję z aliasem ServiceFabricEvent:

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

Zwraca zdarzenia operacyjne zarejestrowane w ostatniej godzinie:
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

Zwraca zdarzenia operacyjne z EventId == 18604 i EventName == 'NodeDownOperational':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

Zwraca zdarzenia operacyjne z EventId == 18604 i EventName == 'NodeUpOperational':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 
 
Zwraca raporty kondycji ze stanem kondycji == 3 (błąd) i wyodrębnia dodatkowe właściwości z pola EventMessage:

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

Zwraca wykres czasu zdarzeń z EventId != 17523:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

Pobierz zdarzenia operacyjne sieci szkieletowej usług zagregowane z określoną usługą i węzłem:

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

Renderowanie liczby zdarzeń sieci szkieletowej usług według identyfikatora zdarzeń / zdarzenia przy użyciu kwerendy między zasobami:

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

### <a name="view-service-fabric-application-events"></a>Wyświetlanie zdarzeń aplikacji sieci szkieletowej usług

Można wyświetlić zdarzenia dla niezawodnych usług i niezawodnych aplikacji podmiotów wdrożonych w klastrze.  Na stronie Analiza sieci szkieletowej usług kliknij wykres **zdarzeń aplikacji**.

Uruchom następującą kwerendę, aby wyświetlić zdarzenia z niezawodnych aplikacji usługowych:
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

Można zobaczyć różne zdarzenia, gdy runasync usługi jest uruchamiany i ukończony, co zwykle dzieje się na wdrożeniach i uaktualnień.

![Niezawodne usługi w zakresie sieci szkieletowej usług](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

Można również znaleźć zdarzenia dla niezawodnej usługi z ServiceName == "fabric:/Watchdog/WatchdogService":

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
Wiarygodne wydarzenia aktor można oglądać w podobny sposób:

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
Aby skonfigurować bardziej szczegółowe zdarzenia dla wiarygodnych podmiotów, można zmienić `scheduledTransferKeywordFilter` w konfiguracji rozszerzenia diagnostycznego w szablonie klastra. Szczegóły dotyczące wartości dla nich znajdują się w [odwołaniu do zdarzeń wiarygodnych aktorów](service-fabric-reliable-actors-diagnostics.md#keywords).

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

## <a name="view-performance-counters-with-azure-monitor-logs"></a>Wyświetlanie liczników wydajności za pomocą dzienników usługi Azure Monitor
Aby wyświetlić liczniki wydajności, przejdź do [witryny Azure portal](https://portal.azure.com) i grupy zasobów, w której utworzono rozwiązanie analizy sieci szkieletowej usług. 

Wybierz obszar roboczy **ServiceFabric(mysfomsworkspace),** a następnie **obszar roboczy usługi Log Analytics**, a następnie ustawienia **zaawansowane**.

Kliknij **pozycję Dane**, a następnie kliknij pozycję **Liczniki wydajności systemu Windows**. Istnieje lista liczników domyślnych, które można włączyć i można ustawić interwał dla kolekcji zbyt. Można również dodać [dodatkowe liczniki wydajności](service-fabric-diagnostics-event-generation-perf.md) do zbierania. W tym [artykule](/windows/desktop/PerfCtrs/specifying-a-counter-path)odwołuje się odpowiedni format . Kliknij **pozycję Zapisz**, a następnie kliknij przycisk **OK**.

Zamknij bloku Ustawienia zaawansowane i wybierz **podsumowanie obszaru roboczego** w nagłówku **Ogólne.** Dla każdego z włączonych rozwiązań jest kafelek graficzny, w tym jeden dla sieci szkieletowej usług. Kliknij wykres **sieci szkieletowej usług,** aby przejść do rozwiązania analizy sieci szkieletowej usług.

Istnieją kafelki graficzne dla kanałów operacyjnych i niezawodnych zdarzeń usługowych. Graficzna reprezentacja danych płynących dla wybranych liczników pojawi się w obszarze **Metryki węzłów**. 

Wybierz wykres **metryki kontenera,** aby wyświetlić dodatkowe szczegóły. Można również kwerendy na dane licznika wydajności podobnie do zdarzeń klastra i filtrować na węzłach, nazwę licznika perf i wartości przy użyciu języka zapytania Kusto.

## <a name="query-the-eventstore-service"></a>Zapytanie o usługę EventStore
[Usługa EventStore](service-fabric-diagnostics-eventstore.md) umożliwia zrozumienie stanu klastra lub obciążeń w danym momencie. EventStore jest usługą sieci szkieletowej usług stanowych, która przechowuje zdarzenia z klastra. Zdarzenia są udostępniane za pośrednictwem [Eksploratora sieci szkieletowej usług,](service-fabric-visualizing-your-cluster.md)REST i interfejsów API. EventStore wysyła zapytanie do klastra bezpośrednio, aby uzyskać dane diagnostyczne dla dowolnej jednostki w klastrze Aby wyświetlić pełną listę zdarzeń dostępnych w magazynie zdarzeń, zobacz [Zdarzenia sieci szkieletowej usług.](service-fabric-diagnostics-event-generation-operational.md)

Interfejsy API eventstore można wyszukiwać programowo za pomocą [biblioteki klienta sieci szkieletowej usług.](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library)

Oto przykładowe żądanie dla wszystkich zdarzeń klastra między 2018-04-03T18:00:00Z i 2018-04-04T18:00:00Z, za pośrednictwem funkcji GetClusterEventListAsync.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Oto kolejny przykład, że kwerendy dotyczące kondycji klastra i wszystkich zdarzeń węzła we wrześniu 2018 r. i drukuje je.

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
Sieć szkieletowa usług wprowadza [model kondycji](service-fabric-health-introduction.md) z jednostkami kondycji, na których składniki systemu i watchdogs mogą zgłaszać lokalne warunki, które są monitorujące. [Magazyn kondycji](service-fabric-health-introduction.md#health-store) agreguje wszystkie dane dotyczące kondycji, aby ustalić, czy jednostki są w dobrej kondycji.

Klaster jest automatycznie wypełniany raportami kondycji wysyłanymi przez składniki systemu. Dowiedz się więcej na [temat Używanie raportów kondycji systemu do rozwiązywania problemów](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Sieci szkieletowej usług udostępnia kwerendy kondycji dla każdego z obsługiwanych [typów jednostek](service-fabric-health-introduction.md#health-entities-and-hierarchy). Można uzyskać do nich dostęp za pośrednictwem interfejsu API, przy użyciu metod na [FabricClient.HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), polecenia cmdlet programu PowerShell i REST. Te kwerendy zwracają pełne informacje o kondycji jednostki: zagregowany stan kondycji, zdarzenia kondycji jednostki, stany kondycji dziecka (w stosownych przypadkach), oceny w złej kondycji (gdy jednostka nie jest w dobrej kondycji) i statystyki zdrowia dzieci (gdy w stosownych przypadkach).

### <a name="get-cluster-health"></a>Uzyskaj kondycję klastra
[Polecenie cmdlet Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) zwraca kondycję jednostki klastra i zawiera stany kondycji aplikacji i węzłów (elementy podrzędne klastra).  Najpierw połącz się z klastrem za pomocą [polecenia cmdlet Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps).

Stan klastra to 11 węzłów, aplikacja systemowa i fabric:/Voting skonfigurowane zgodnie z opisem.

Poniższy przykład pobiera kondycję klastra przy użyciu domyślnych zasad kondycji. 11 węzłów jest w dobrej kondycji, ale stan kondycji zagregowane klastra jest błąd, ponieważ fabric:/Voting aplikacji jest w błąd. Należy zauważyć, jak oceny w złej kondycji zawierają szczegółowe informacje na temat warunków, które wyzwoliły zagregowane kondycji.

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

Poniższy przykład pobiera kondycję klastra przy użyciu zasad aplikacji niestandardowej. Filtruje wyniki, aby uzyskać tylko aplikacje i węzły w błąd lub ostrzeżenie. W tym przykładzie nie węzły są zwracane, ponieważ wszystkie one są w dobrej kondycji. Tylko fabric:/Aplikacja głosowania szanuje filtr aplikacji. Ponieważ zasady niestandardowe określa, aby wziąć pod uwagę ostrzeżenia jako błędy dla aplikacji fabric:/Voting, aplikacja jest oceniana jako błąd i tak jest klastra.

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

### <a name="get-node-health"></a>Uzyskaj kondycję węzła
[Polecenie cmdlet Get-ServiceFabricNodeHealth](/powershell/module/servicefabric/get-servicefabricnodehealth) zwraca kondycję jednostki węzła i zawiera zdarzenia kondycji zgłoszone w węźle. Najpierw połącz się z klastrem za pomocą [polecenia cmdlet Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps). Poniższy przykład pobiera kondycję określonego węzła przy użyciu domyślnych zasad kondycji:

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

Poniższy przykład pobiera kondycję wszystkich węzłów w klastrze:
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>Uzyskaj kondycję usługi systemowej 

Uzyskaj zagregowane kondycje usług systemowych:

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetlanie zdarzeń sieci szkieletowej usług
> * Kwerendy eventstore interfejsów API dla zdarzeń klastra
> * Monitorowanie infrastruktury/zbieranie liczników perf
> * Wyświetlanie raportów kondycji klastra

Następnie przejdź do następującego samouczka, aby dowiedzieć się, jak skalować klaster.
> [!div class="nextstepaction"]
> [Skalowanie klastra](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
