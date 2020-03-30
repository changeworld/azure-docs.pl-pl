---
title: Wywoływanie chaosu w klastrach sieci szkieletowej usług
description: Używanie interfejsów API usługi iniekcji i analizy klastrów do zarządzania chaosem w klastrze.
author: motanv
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: motanv
ms.openlocfilehash: 37b451abd0a519dff17aba9b2d6c42b4762f30cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463170"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Wywoływanie kontrolowanego chaosu w klastrach sieci szkieletowej usług
Wielkoskalowe systemy rozproszone, takie jak infrastruktura chmury, są z natury zawodne. Usługa Azure Service Fabric umożliwia deweloperom pisanie niezawodnych usług rozproszonych na podstawie zawodnej infrastruktury. Aby napisać niezawodne usługi rozproszone na szczycie za zawodne infrastruktury, deweloperzy muszą być w stanie przetestować stabilność swoich usług, podczas gdy podstawowej infrastruktury zawodne przechodzi skomplikowanych przejść stanu z powodu błędów.

Usługa [iniekcji błędów i analizy klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-testability-overview) (znana również jako usługa analizy błędów) daje deweloperom możliwość wywoływania błędów w celu przetestowania ich usług. Te ukierunkowane symulowane błędy, takie jak [ponowne uruchomienie partycji,](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps)mogą pomóc w wykonywaniu najczęstszych przejść stanu. Jednak ukierunkowane symulowane błędy są tendencyjne z definicji i w związku z tym mogą przegapić błędy, które pojawiają się tylko w trudnej do przewidzenia, długiej i skomplikowanej sekwencji przejść stanu. Aby uzyskać bezstronne testy, możesz użyć Chaosu.

Chaos symuluje okresowe, przeplatane błędy (zarówno wdzięczne, jak i niewdzięczne) w całym klastrze przez dłuższy czas. Błąd pełen wdzięku składa się z zestawu wywołań interfejsu API sieci szkieletowej usług, na przykład błąd ponownego uruchomienia repliki jest błędem wdzięku, ponieważ jest to zamknięcie, po którym następuje otwarcie w replice. Usuń replikę, przenieś replikę podstawową i przenieś replikę pomocniczą to inne błędy pełne wdzięku wykonywane przez chaos. Błędy nieprzetworzone są wyjścia procesu, takie jak węzeł ponownego uruchamiania i pakiet kodu ponownego uruchamiania. 

Po skonfigurowaniu chaosu z szybkością i rodzajem błędów można uruchomić chaos za pośrednictwem interfejsu API Języka C#, Programu Powershell lub REST, aby rozpocząć generowanie błędów w klastrze i w usługach. Chaos można skonfigurować do uruchamiania przez określony czas (na przykład przez jedną godzinę), po czym chaos zatrzymuje się automatycznie lub można wywołać Interfejs API StopChaos (C#, Powershell lub REST), aby zatrzymać go w dowolnym momencie.

> [!NOTE]
> W obecnej formie Chaos wywołuje tylko bezpieczne błędy, co oznacza, że w przypadku braku błędów zewnętrznych nigdy nie występuje utrata kworum lub utrata danych.
>

Podczas chaosu jest uruchomiony, produkuje różne zdarzenia, które przechwytują stan uruchomienia w tej chwili. Na przykład ExecutingFaultsEvent zawiera wszystkie błędy, które Chaos postanowił wykonać w tej iteracji. A ValidationFailedEvent zawiera szczegóły błędu sprawdzania poprawności (problemy ze zdrowiem lub stabilności), który został znaleziony podczas sprawdzania poprawności klastra. Można wywołać GetChaosReport interfejsu API (C#, Powershell lub REST), aby uzyskać raport chaos uruchamia. Te zdarzenia są zachowywane w [słowniku niezawodne](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections), który ma zasady obcinania podyktowane przez dwie konfiguracje: **MaxStoredChaosEventCount** (wartość domyślna to 25000) i **StoredActionCleanupIntervalInSeconds** (wartość domyślna to 3600). Każdy *StoredActionCleanupIntervalInSeconds* Chaos sprawdza i wszystkie, ale najnowsze zdarzenia *MaxStoredChaosEventCount,* są czyszczone z niezawodnego słownika.

## <a name="faults-induced-in-chaos"></a>Błędy wywołane w Chaosie
Chaos generuje błędy w całym klastrze sieci szkieletowej usług i kompresuje błędy, które są widoczne w miesiącach lub latach w ciągu kilku godzin. Kombinacja błędów przeplatanych z wysoką szybkością błędów znajduje przypadki narożników, które w przeciwnym razie mogą zostać pominięte. To ćwiczenie Chaos prowadzi do znacznej poprawy jakości kodu usługi.

Chaos wywołuje błędy z następujących kategorii:

* Ponowne uruchamianie węzła
* Ponowne uruchamianie wdrożonego pakietu kodu
* Usuwanie repliki
* Ponowne uruchamianie repliki
* Przenoszenie repliki podstawowej (konfigurowalnej)
* Przenoszenie repliki pomocniczej (konfigurowalnej)

Chaos działa w wielu iteracjach. Każda iteracja składa się z błędów i sprawdzania poprawności klastra dla określonego okresu. Można skonfigurować czas spędzony na ustabilizowanie klastra i pomyślne sprawdzenie poprawności. Jeśli błąd zostanie znaleziony w sprawdzania poprawności klastra, Chaos generuje i utrzymuje ValidationFailedEvent z sygnaturą czasową UTC i szczegóły błędu. Rozważmy na przykład wystąpienie Chaos, który jest ustawiony do uruchomienia przez godzinę z maksymalnie trzech równoczesnych błędów. Chaos wywołuje trzy błędy, a następnie sprawdza poprawność kondycji klastra. Iteruje za pośrednictwem poprzedniego kroku, dopóki nie zostanie jawnie zatrzymany za pośrednictwem interfejsu API StopChaosAsync lub jednogodzinnych przebiegów. Jeśli klaster staje się w złej kondycji w dowolnej iteracji (oznacza to, że nie stabilizuje się lub nie staje się w dobrej kondycji w ramach przekazanego MaxClusterStabilizationTimeout), Chaos generuje ValidationFailedEvent. To zdarzenie wskazuje, że coś poszło nie tak i może wymagać dalszego badania.

Aby uzyskać błędy wywołane chaosem, można użyć interfejsu API GetChaosReport (powershell, C#, lub REST). Interfejs API pobiera następny segment chaosu raportu na podstawie tokenu kontynuacji przekazanych lub przekazany w zakresie czasu. Można określić ContinuationToken, aby uzyskać następny segment raportu Chaos lub można określić zakres czasu za pośrednictwem StartTimeUtc i EndTimeUtc, ale nie można określić zarówno ContinuationToken i zakres czasu w tym samym wywołaniu. Gdy istnieje więcej niż 100 zdarzeń Chaos, raport Chaos jest zwracany w segmentach, gdzie segment zawiera nie więcej niż 100 zdarzeń Chaos.

## <a name="important-configuration-options"></a>Ważne opcje konfiguracji
* **TimeToRun:** Całkowity czas, że Chaos działa przed zakończeniem z powodzeniem. Można zatrzymać Chaos, zanim został uruchomiony dla timetorun okres za pośrednictwem interfejsu API StopChaos.

* **MaxClusterStabilizationTimeout:** Maksymalna ilość czasu oczekiwania na klaster w dobrej kondycji przed wyprodukowaniem ValidationFailedEvent. To oczekiwanie jest zmniejszenie obciążenia klastra podczas odzyskiwania. Przeprowadzone kontrole są następujące:
  * Jeśli kondycja klastra jest w porządku
  * Jeśli kondycja usługi jest OK
  * Jeśli rozmiar zestawu replik docelowych zostanie osiągnięty dla partycji usługi
  * Że nie istnieją repliki InBuild
* **MaxConcurrentFaults**: Maksymalna liczba równoczesnych błędów, które są wywoływane w każdej iteracji. Im wyższa liczba, tym bardziej agresywny chaos jest i pracy awaryjnej i kombinacji przejścia stanu, które klastra przechodzi są również bardziej złożone. 

> [!NOTE]
> Niezależnie od tego, jak wysoka wartość *MaxConcurrentFaults* ma Chaos gwarantuje - w przypadku braku błędów zewnętrznych — nie ma utraty kworum lub utraty danych.
>

* **EnableMoveReplicaFaults**: Włącza lub wyłącza błędy, które powodują przenoszenie replik podstawowych lub pomocniczych. Te błędy są domyślnie włączone.
* **WaitTimeBetweenIterations**: Czas oczekiwania między iteracjami. Oznacza to, że czas Chaos zostanie wstrzymany po wykonaniu rundy błędów i po zakończeniu odpowiedniego sprawdzania poprawności kondycji klastra. Im wyższa wartość, tym niższa jest średnia szybkość wtrysku usterek.
* **WaitTimeBetweenFaults:** Czas oczekiwania między dwoma kolejnymi błędami w jednej iteracji. Im wyższa wartość, tym niższa współbieżność (lub nakładanie się między) błędów.
* **ClusterHealthPolicy:** Zasady kondycji klastra służy do sprawdzania poprawności kondycji klastra między iteracji Chaos. Jeśli kondycja klastra jest w błędzie lub jeśli nieoczekiwany wyjątek wystąpi podczas wykonywania błędów, Chaos będzie czekać przez 30 minut przed następnym sprawdzanie kondycji — aby zapewnić klastra z pewnym czasem na regenerację.
* **Kontekst:** Kolekcja (ciąg, ciąg) typ klucza wartości par. Mapa może służyć do rejestrowania informacji o przebiegu chaosu. Nie może być więcej niż 100 takich par i każdy ciąg (klucz lub wartość) może mieć co najwyżej 4095 znaków. Ta mapa jest ustawiana przez rozrusznik chaos uruchomić opcjonalnie przechowywać kontekst o określonym przebiegu.
* **ChaosTargetFilter:** Ten filtr może służyć do kierowania błędów Chaos tylko do niektórych typów węzłów lub tylko do niektórych wystąpień aplikacji. Jeśli ChaosTargetFilter nie jest używany, Chaos błędów wszystkich jednostek klastra. Jeśli ChaosTargetFilter jest używany, Chaos błędów tylko jednostki, które spełniają specyfikacji ChaosTargetFilter. NodeTypeInclusionList i ApplicationInclusionList zezwalają tylko na semantykę unii. Innymi słowy nie jest możliwe określenie przecięcia NodeTypeInclusionList i ApplicationInclusionList. Na przykład nie jest możliwe określenie "błąd tej aplikacji tylko wtedy, gdy jest w tym typie węzła." Gdy jednostka jest uwzględniona w NodeTypeInclusionList lub ApplicationInclusionList, tej jednostki nie można wykluczyć za pomocą ChaosTargetFilter. Nawet jeśli applicationX nie pojawia się w ApplicationInclusionList, w niektórych Chaos iteracji applicationX może być uszkodzony, ponieważ dzieje się to w węźle nodeTypeY, który znajduje się w NodeTypeInclusionList. Jeśli zarówno NodeTypeInclusionList i ApplicationInclusionList są null lub puste, ArgumentException jest generowany.
    * **NodeTypeInclusionList**: Lista typów węzłów do uwzględnienia w błędach Chaos. Wszystkie typy usterek (węzeł ponownego uruchomienia, ponowne uruchomienie pakietu kodowego, usunięcie repliki, ponowne uruchomienie repliki, przeniesienie podstawowego i przeniesienie pomocnicze) są włączone dla węzłów tych typów węzłów. Jeśli typ węzła (powiedzmy NodeTypeX) nie pojawi się na liście NodeTypeInclusionList, błędy poziomu węzła (takie jak NodeRestart) nigdy nie będą włączone dla węzłów NodeTypeX, ale pakiet kodu i błędy replik mogą być nadal włączone dla NodeTypeX, jeśli aplikacja w ApplicationInclusionList znajduje się w węźle NodeTypeX. Co najwyżej 100 nazw typów węzłów mogą być zawarte na tej liście, aby zwiększyć tę liczbę, uaktualnienie konfiguracji jest wymagane dla konfiguracji MaxNumberOfNodeTypesInChaosTargetFilter.
    * **ApplicationInclusionList**: Lista identyfikatorów URI aplikacji do uwzględnienia w błędach Chaos. Wszystkie repliki należące do usług tych aplikacji są podatne na błędy repliki (ponowne uruchomienie repliki, usunięcie repliki, przeniesienie podstawowego i przeniesienie pomocniczego) przez chaos. Chaos może ponownie uruchomić pakiet kodu tylko wtedy, gdy pakiet kodu obsługuje repliki tylko tych aplikacji. Jeśli aplikacja nie pojawia się na tej liście, nadal może być uszkodzony w niektórych iteracji Chaos, jeśli aplikacja kończy się w węźle typu węzła, który znajduje się w NodeTypeInclusionList. Jednak jeśli applicationX jest powiązany z nodeTypeY poprzez ograniczenia umieszczania i applicationX jest nieobecny w ApplicationInclusionList i nodeTypeY jest nieobecny z NodeTypeInclusionList, a następnie applicationX nigdy nie będzie uszkodzony. Co najwyżej 1000 nazw aplikacji mogą być zawarte na tej liście, aby zwiększyć tę liczbę, uaktualnienie konfiguracji jest wymagane dla konfiguracji MaxNumberOfApplicationsInChaosTargetFilter.

## <a name="how-to-run-chaos"></a>Jak uruchomić Chaos

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
