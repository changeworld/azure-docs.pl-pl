---
title: Occurs Chaos w klastrach usługi Service Fabric | Dokumentacja firmy Microsoft
description: Za pomocą technika wstrzykiwania błędów i interfejsów API usługi analizy klaster do zarządzania Chaos w klastrze.
services: service-fabric
documentationcenter: .net
author: motanv
manager: anmola
editor: motanv
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/05/2018
ms.author: motanv
ms.openlocfilehash: 7a22b17d45218c2f78220f980605b3c211495606
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543740"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Occurs kontrolowanego chaosu w klastrach usługi Service Fabric
Systemów, takich jak infrastruktur chmury są założenia zawodnych rozproszonych na dużą skalę. Usługa Azure Service Fabric umożliwia programistom pisanie usług reliable services rozproszone na szczycie infrastruktury wiarygodne. Można zapisać niezawodnych usług rozproszonych na szczycie infrastruktury zawodnych, deweloperzy muszą można testować stabilność ich usług, podczas gdy podstawowej infrastruktury zawodnych przechodzi skomplikowane stanami z powodu błędów.

[Technika wstrzykiwania błędów i usługa analityczna klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-testability-overview) (znany także jako usługa analizy błędów) daje deweloperom możliwość powodować błędy, aby przetestować swoje usługi. Błędy, są przeznaczone symulowanych jak [ponowne uruchomienie partycji](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), ułatwia wykonywanie typowych stanami. Jednak docelowych symulowane błędy są obciążona zgodnie z definicją i dlatego może brakować usterek przedstawiające się tylko w twardych przewidzieć, długie i złożone sekwencji stanami. Do testowania bezstronna, możesz użyć Chaos.

Chaos symuluje błędów przeplotem, okresowe (łagodne i nieprawidłowego) w całym klastrze przez dłuższy czas. Łagodne błędów zawiera zestaw wywołań interfejsu API usługi Service Fabric, na przykład ponowne uruchomienie repliki błędów jest łagodne błędów, ponieważ jest to zamknięcia, a następnie otwórz w replice. Usuń replikę, przeniesienia replikę podstawową i replikę pomocniczą przeniesienia są inne błędy łagodne wykonywane przez Chaos. Błędy nieprawidłowego są wyjścia procesu, takie jak ponownie uruchomić węzeł i uruchom ponownie pakiet kodu. 

Po skonfigurowaniu Chaos przy użyciu stawki i rodzaju błędów, można uruchomić Chaos przy użyciu języka C#, programu Powershell lub interfejsu API REST można uruchomić generowania błędy w klastrze, w usługach. Chaosu do uruchamiania w określonym przedziale czasu (na przykład jedna godzina), po których zatrzymuje Chaos można skonfigurować automatycznie lub wywołując StopChaos API (C#, programu Powershell lub REST) aby ją wyłączyć w dowolnym momencie.

> [!NOTE]
> W jego obecnej formie Chaos wywołuje tylko bezpiecznych błędów, co oznacza, że w przypadku braku błędów zewnętrznych utraciła kworum lub utraty danych nigdy nie następuje.
>

Po uruchomieniu Chaos tworzy różnych zdarzeń, które przechwytują stan wykonywania w tej chwili. Na przykład ExecutingFaultsEvent zawiera wszystkie błędy, które Chaos zdecydował się do wykonywania w tej iteracji. ValidationFailedEvent zawiera szczegóły błędu sprawdzania poprawności (problemy dotyczące kondycji lub stabilności), który został znaleziony podczas sprawdzania poprawności klastra. Możesz wywołać raporcie GetChaosReport interfejsu API (C#, programu Powershell lub REST) można pobrać raportu Chaos przebiegów. Te zdarzenia pobieranie utrwalonych w [niezawodnego słownika](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections), która zawiera zasady obcięcie przez dwie konfiguracje: **MaxStoredChaosEventCount** (wartość domyślna to 25000) i **StoredActionCleanupIntervalInSeconds** (wartość domyślna to 3600). Każdy *StoredActionCleanupIntervalInSeconds* Chaos kontroli i wszystkie, ale najbardziej aktualną *MaxStoredChaosEventCount* zdarzenia, są usuwane z niezawodnego słownika.

## <a name="faults-induced-in-chaos"></a>Błędy wywołanego w chaosu
Chaos generuje błędy dla całego klastra usługi Service Fabric i kompresuje błędy, które są widoczne w ciągu miesięcy lub lat w ciągu kilku godzin. Kombinacja przeplotem błędów przy użyciu współczynnika wysoką odporność znajduje przypadki brzegowe, które w przeciwnym razie mogą zostać pominięci. To ćwiczenie Chaos prowadzi do znacznej poprawy jakości kodu usługi.

Chaos wywołuje usterek z następujących kategorii:

* Ponowne uruchomienie węzła
* Uruchom ponownie pakiet wdrożonego kodu
* Usuwanie repliki
* Uruchom ponownie replikę
* Przenieś repliki podstawowej (z możliwością konfiguracji)
* Przenieś tylko do repliki pomocniczej (z możliwością konfiguracji)

Chaos działa w wielu iteracji. Każda iteracja składa się z błędów i sprawdzanie poprawności klastra w wybranym okresie. Możesz skonfigurować czas dla klastra to stabilizację i sprawdzanie poprawności zakończyło się sukcesem. Jeśli błąd zostanie znaleziony podczas sprawdzania poprawności klastra, Chaos generuje i będzie się powtarzał ValidationFailedEvent sygnatura czasowa UTC i szczegóły błędu. Na przykład należy wziąć pod uwagę wystąpienie elementu Chaos, skonfigurowanej do uruchomienia na godzinę z maksymalnie trzech współbieżnych błędów. Chaos wywołuje trzy błędów, a następnie sprawdza poprawność kondycji klastra. Iteruje poprzedniego kroku, dopóki nie zostanie jawnie zatrzymana za pomocą interfejsu API StopChaosAsync lub godzinna przekazuje. Jeśli klaster staje się zła w dowolnym iteracji (oznacza to, nie stabilizacji lub go nie poprawi w ramach MaxClusterStabilizationTimeout przekazanego) Chaos generuje ValidationFailedEvent. To zdarzenie wskazuje, że wystąpił problem i mogą wymagać dalszej analizy.

Można pobrać błędów, które Chaos wywołane, można użyć raporcie GetChaosReport API (powershell, C# lub REST). Interfejs API pobiera następny segment Chaos raportu na podstawie token kontynuacji w przekazanym lub przekazany w czasie zakresie. Można określić token kontynuacji, można pobrać następny segment raportu Chaos lub można określić zakres czasu za pośrednictwem StartTimeUtc i EndTimeUtc, ale nie można określić zakres czasu i token kontynuacji, które znajdują się w tym samym wywołaniu. W przypadku więcej niż 100 zdarzeń Chaos raportu Chaos jest zwracany w segmentów, których segment zawiera nie więcej niż 100 zdarzeń Chaos.

## <a name="important-configuration-options"></a>Opcje konfiguracji ważne
* **Wartość TimeToRun**: Całkowity czas, który uruchamia Chaos przed kończy się sukcesem. Zostało uruchomione przez wartość TimeToRun, za pomocą interfejsu API StopChaos, można je zatrzymać Chaos.

* **MaxClusterStabilizationTimeout**: Maksymalna ilość czasu oczekiwania na poprawi przed utworzeniem ValidationFailedEvent klastra. Ta oczekiwania jest zmniejszenie obciążenia w klastrze, gdy trwa jej odzyskiwanie. Kontrole wykonywane są następujące:
  * Jeśli kondycja klastra jest OK
  * Jeśli kondycja usługi jest OK
  * Jeśli rozmiar zestawu replik docelowej uzyskuje się partycji usługi
  * Czy istnieją nie replik w stanie InBuild
* **MaxConcurrentFaults**: Maksymalna liczba współbieżnych błędów, które są wywołane w każdej iteracji. Jest wyższa Chaos liczba, tym bardziej agresywne i trybu failover i kombinacji przejście stanu, które klaster przechodzi przez, są również bardziej złożone. 

> [!NOTE]
> Niezależnie od tego jak wysoka wartość *MaxConcurrentFaults* ma Chaos gwarantuje — w przypadku braku błędów zewnętrznych — Brak utraty danych lub utraciła kworum.
>

* **EnableMoveReplicaFaults**: Włącza lub wyłącza błędy, które powodują repliki podstawowej lub dodatkowej, aby przenieść. Te błędy są domyślnie włączone.
* **WaitTimeBetweenIterations**: Ilość czasu oczekiwania między poszczególnymi iteracjami. Oznacza to czas Chaos zatrzyma się po wykonaniu round błędów i o zakończeniu odpowiedniego sprawdzania poprawności kondycji klastra. Im wyższa wartość, niższa jest częstotliwość iniekcji błędów średni.
* **WaitTimeBetweenFaults**: Ilość czasu oczekiwania między dwa kolejne błędy w jednej iteracji. Im większa wartość, dolna wartość współbieżności to (lub nakładania się) błędów.
* **ClusterHealthPolicy**: Zasady dotyczące kondycji klastra jest używany do sprawdzania poprawności kondycji klastra między Chaos iteracji. Jeśli kondycja klastra jest w wyniku błędu lub sytuacji wystąpił nieoczekiwany wyjątek podczas wykonywania błędów Chaos będzie czekać przez 30 minut przed następnym — sprawdzenie kondycji — zapewnienie klastra recuperate trochę czasu.
* **Kontekst**: Kolekcja (string, string) wpisz pary klucz wartość. Mapy może służyć do rejestrowania informacji o przebiegu Chaos. Nie może istnieć więcej niż 100 takich pary i każdego ciągu (klucza lub wartości) mogą mieć maksymalnie 4095 znaków. Ta mapa jest ustawiany przez początkowego elementu Chaos, uruchom na przechowywanie kontekst dotyczący określonego przebiegu.
* **ChaosTargetFilter**: Ten filtr może służyć do docelowego Chaos błędów tylko do niektórych typów węzłów lub tylko do niektórych wystąpień aplikacji. Jeśli w obiekcie ChaosTargetFilter nie jest używany, Chaos błędów wszystkie jednostki klastra. Jeśli obiekcie ChaosTargetFilter Chaos błędów jednostek, które spełniają specyfikacji obiekcie ChaosTargetFilter. Obie opcje NodeTypeInclusionList i ApplicationInclusionList umożliwiają tylko semantyki Unii. Innymi słowy nie jest możliwe określenie część wspólna obie opcje NodeTypeInclusionList i ApplicationInclusionList. Na przykład nie jest możliwe określenie "błędów tej aplikacji, tylko wtedy, gdy jej dla tego typu węzła". Gdy obie opcje NodeTypeInclusionList i ApplicationInclusionList zawiera jednostki, tej jednostki nie można wykluczyć za pomocą obiekcie ChaosTargetFilter. Nawet wtedy, gdy opcja ApplicationInclusionList nie ma applicationX, w niektórych iteracji Chaos applicationX mogą być błędne ponieważ zdarza się w węźle nodeTypeY, który znajduje się w obie opcje NodeTypeInclusionList. Jeśli obie opcje NodeTypeInclusionList i ApplicationInclusionList o wartości null ani być pusta, zostanie zgłoszony ArgumentException.
    * **Obie opcje NodeTypeInclusionList**: Lista typy węzłów, które mają zostać objęte Chaos błędów. Wszystkie typy błędów (ponownie uruchomić węzeł, uruchom ponownie codepackage, usunąć replikę, uruchom ponownie repliki, Przenieś podstawowego i przenoszenie dodatkowej) są włączone dla węzłów z tych typów węzłów. Jeśli element nodetype (powiedzieć NodeTypeX) nie jest widoczna w obie opcje NodeTypeInclusionList, a następnie węzeł poziomu błędów (np. NodeRestart) nigdy nie zostanie włączona dla węzłów NodeTypeX, ale błędów pakietu i repliki kod nadal może zostać włączona dla NodeTypeX, jeśli aplikacja w Opcja ApplicationInclusionList stanie się znajdują się w węźle NodeTypeX. Co najwyżej 100 nazw typ węzła może obejmować na tej liście, aby zwiększyć tę liczbę, wymagane jest uaktualnienie konfiguracji dla konfiguracji MaxNumberOfNodeTypesInChaosTargetFilter.
    * **Opcja ApplicationInclusionList**: Lista aplikacji identyfikatorów URI Chaos błędów. Wszystkie repliki należących do usługi te aplikacje są podatna na błędy repliki (repliki ponownego uruchomienia, Usuń replikę, Przenieś podstawowego i zapasowego przenoszenia) przez Chaos. Chaos może ponownie uruchomić pakiet kodu, tylko wtedy, gdy pakiet kodu obsługuje replik tylko do tych aplikacji. Jeśli aplikacja nie ma na tej liście, jego może nadal wystąpi błąd w niektórych iteracji Chaos Jeśli aplikacja kończy się w węźle typ węzła, który znajduje się w obie opcje NodeTypeInclusionList. Jednak jeśli applicationX jest powiązany z nodeTypeY ograniczeniami dotyczącymi umieszczania i applicationX Brak ApplicationInclusionList i nodeTypeY jest nieobecny w obie opcje NodeTypeInclusionList, następnie applicationX nigdy nie wystąpi błąd. Maksymalnie 1000 nazwy aplikacji mogły zostać uwzględnione w tej liście, aby zwiększyć tę liczbę, wymagane jest uaktualnienie konfiguracji dla konfiguracji MaxNumberOfApplicationsInChaosTargetFilter.

## <a name="how-to-run-chaos"></a>Jak uruchomić chaosu

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }
        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;

            // The maximum amount of time to wait for all cluster entities to become stable and healthy. 
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster
            // entities. 
            // During validation if a cluster entity is not stable and healthy within
            // MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex
            // series of states to uncover bugs.
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be
            // used to record information about the Chaos run. There cannot be more than 100 such pairs and
            // each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the
            // lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration.
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of
            // state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };

            // All types of faults, restart node, restart code package, restart replica, move primary replica,
            // and move secondary replica will happen for nodes of type 'FrontEndType'
            var nodetypeInclusionList = new List<string> { "FrontEndType"};

            // In addition to the faults included by nodetypeInclusionList,
            // restart code package, restart replica, move primary replica, move secondary replica faults will
            // happen for 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing
            // on a node which is not of type included in nodeypeInclusionList.
            var applicationInclusionList = new List<string> { "fabric:/TestApp2" };

            // List of cluster entities to target for Chaos faults.
            var chaosTargetFilter = new ChaosTargetFilter
            {
                NodeTypeInclusionList = nodetypeInclusionList,
                ApplicationInclusionList = applicationInclusionList
            };

            var parameters = new ChaosParameters(
                maxClusterStabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun,
                startContext,
                waitTimeBetweenIterations,
                waitTimeBetweenFaults,
                clusterHealthPolicy) {ChaosTargetFilter = chaosTargetFilter};

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            string continuationToken = null;

            while (true)
            {
                ChaosReport report;
                try
                {
                    report = string.IsNullOrEmpty(continuationToken)
                        ? client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult()
                        : client.TestManager.GetChaosReportAsync(continuationToken).GetAwaiter().GetResult();
                }
                catch (Exception e)
                {
                    if (e is FabricTransientException)
                    {
                        Console.WriteLine("A transient exception happened: '{0}'", e);
                    }
                    else if(e is TimeoutException)
                    {
                        Console.WriteLine("A timeout exception happened: '{0}'", e);
                    }
                    else
                    {
                        throw;
                    }

                    Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
                    continue;
                }

                continuationToken = report.ContinuationToken;

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```

```powershell
$clusterConnectionString = "localhost:19000"
$timeToRunMinute = 60

# The maximum amount of time to wait for all cluster entities to become stable and healthy.
# Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities.
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds,
# Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration.
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of
# states to uncover bugs.
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the
# fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration.
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state
# transitions that the cluster goes through.
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record
# information about the Chaos run.
# There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

#List of cluster entities to target for Chaos faults.
$chaosTargetFilter = new-object -TypeName System.Fabric.Chaos.DataStructures.ChaosTargetFilter
$chaosTargetFilter.NodeTypeInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# All types of faults, restart node, restart code package, restart replica, move primary replica, and move
# secondary replica will happen for nodes of type 'FrontEndType'
$chaosTargetFilter.NodeTypeInclusionList.AddRange( [string[]]@("FrontEndType") )
$chaosTargetFilter.ApplicationInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# In addition to the faults included by nodetypeInclusionList, 
# restart code package, restart replica, move primary replica, move secondary replica faults will happen for
# 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is
# not of type included in nodeypeInclusionList.
$chaosTargetFilter.ApplicationInclusionList.Add("fabric:/TestApp2")

Connect-ServiceFabricCluster $clusterConnectionString

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRunMinute -MaxConcurrentFaults $maxConcurrentFaults -MaxClusterStabilizationTimeoutSec $maxClusterStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ClusterHealthPolicy $clusterHealthPolicy -ChaosTargetFilter $chaosTargetFilter

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                Write-Host $e
                # When Chaos stops, a StoppedEvent is created.
                # If a StoppedEvent is found, exit the loop.
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    return
                }
            }
        }
    }

    Start-Sleep -Seconds 1
}
```
