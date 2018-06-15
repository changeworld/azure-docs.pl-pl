---
title: Wywołać Chaos w klastrach usługi sieć szkieletowa | Dokumentacja firmy Microsoft
description: Zarządzanie za pomocą iniekcji błędów i interfejsów API usługi analizy klastra Chaos w klastrze.
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
ms.openlocfilehash: 26a954412b8755cd112bf2931ed9bdda291fd727
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204842"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Wywołać kontrolowane Chaos w klastrach sieci szkieletowej usług
Dużych systemów rozproszonych jak infrastruktury chmury jest z założenia gwarantowane. Sieć szkieletowa usług Azure umożliwia deweloperom zapisu niezawodnej usługi rozproszone zawodnych infrastrukturze. Można zapisać niezawodne usługi rozproszone zawodnych infrastrukturze, deweloperzy muszą być możliwe przetestowanie stabilność swoich usług, podczas gdy podstawowej infrastruktury zawodnych przechodzi przez przejść skomplikowane stanu z powodu błędów.

[Iniekcji błędów i usługi klastrowania analizy](https://docs.microsoft.com/azure/service-fabric/service-fabric-testability-overview) (znaną również jako usługa analiza błędów) umożliwia deweloperom powodować błędy, aby przetestować swoich usług. Tak samo, jak te docelowe symulowane błędów, [ponowne uruchomienie partycji](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), ułatwiają wykonywanie typowych przejścia stanu. Jednak docelowe symulowane błędów są ukierunkowane zgodnie z definicją i w związku z tym może nie usterek przedstawiające się tylko w twardych prognozowania, długich i skomplikowane sekwencji przejścia stanu. Nieobciążonej testowania, możesz użyć Chaos.

Chaos symuluje błędów okresowych, przeplotem (bezpieczne i nieprawidłowego) w całym klastrze przez dłuższy czas. Bezpieczne błędów zawiera zestaw wywołań interfejsu API usługi sieci szkieletowej, na przykład błędów repliki ponowne uruchomienie jest bezpieczne błędów, ponieważ jest zamknięty, a następnie otwórz w replice. Usuń replikę, Przenieś repliki podstawowej i repliki pomocniczej przenoszenia są inne błędy bezpiecznie wykonywane przez Chaos. Nieprawidłowego błędów są wyjścia procesu, takie jak ponowne uruchomienie węzła i restrat pacakge kodu. 

Po skonfigurowaniu Chaos częstotliwość i rodzaju błędów, można uruchomić Chaos za pomocą języka C#, programu Powershell lub interfejsu API REST do rozpoczęcia generowania błędów w klastrze i w usługach. Chaos do uruchomienia w określonym przedziale czasu (na przykład na godzinę), po którym zatrzymuje Chaos można skonfigurować automatycznie lub można wywołać interfejsu API StopChaos, (C#, programu Powershell lub REST) można zatrzymać w dowolnym momencie.

> [!NOTE]
> W postaci bieżącego Chaos wywołuje tylko bezpiecznych błędy, co oznacza, że w przypadku braku błędów zewnętrznych utrata kworum, lub utraty danych nigdy nie występuje.
>

Po uruchomieniu Chaos tworzy różnych zdarzeń, których przechwycony stan w momencie uruchomienia. Na przykład ExecutingFaultsEvent zawiera wszystkie błędy, które Chaos podjęto decyzję o wykonanie w tym iteracji. ValidationFailedEvent zawiera szczegóły niepowodzenia weryfikacji (problemów kondycji lub stabilności) został znaleziony podczas sprawdzania poprawności klastra. Można wywołać GetChaosReport interfejsu API (C#, programu Powershell lub REST) w celu uzyskania raportu Chaos działa. Te zdarzenia uzyskać utrwalone w [niezawodnej słownika](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections), której obowiązują zasady obcięcie ustawieniem dwie konfiguracje: **MaxStoredChaosEventCount** (wartość domyślna to 25000) i **StoredActionCleanupIntervalInSeconds** (wartość domyślna to 3600). Co *StoredActionCleanupIntervalInSeconds* Chaos kontroli i wszystkie, ale najnowszej *MaxStoredChaosEventCount* zdarzenia, zostaną usunięte ze słownika wiarygodne.

## <a name="faults-induced-in-chaos"></a>Błędy powstaniu w Chaos
Chaos generuje błędy w ramach całego klastra sieci szkieletowej usług i kompresuje błędów, które są widoczne w miesięcy lub lat do kilku godzin. Kombinacja przeplotem błędów ze wskaźnikiem wysoką odporność znajduje sytuacjach wyjątkowych, które w przeciwnym razie mogą zostać pominięci. Tego ćwiczenia Chaos prowadzi do znacznej poprawy jakości kodu usługi.

Chaos wywołuje usterek z następujących kategorii:

* Uruchom ponownie węzeł
* Ponowne uruchomienie wdrożonego pakietu kodu
* Usuwanie repliki
* Uruchom ponownie repliki
* Przenieś repliki podstawowej (można konfigurować)
* Przenieś repliki pomocniczej (można konfigurować)

Chaos działa w przejść przez wiele iteracji. Każdej iteracji składa się z usterek i weryfikacji klastra w określonym okresie. Można skonfigurować czas dla klastra do utrwalania i Weryfikacja powiodła się. Jeśli błąd zostanie znaleziony w weryfikacji klastra, Chaos generuje i będzie się powtarzał ValidationFailedEvent sygnatury czasowej UTC oraz szczegóły dotyczące błędu. Rozważmy na przykład wystąpienie Chaos, który jest ustawiony na uruchomienie przez godzinę z maksymalnie trzech równoczesnych błędów. Chaos wywołuje trzy błędów, a następnie sprawdza stan klastra. Jego iterację poprzedniego kroku, dopóki nie zostanie jawnie zatrzymana za pośrednictwem interfejsu API StopChaosAsync lub jednogodzinnym przekazuje. Jeśli klaster staje się zła w dowolnym iteracji (oznacza to, że nie ustabilizowania lub go nie stała się dobra przed MaxClusterStabilizationTimeout przekazany w), Chaos generuje ValidationFailedEvent. To zdarzenie wskazuje, że coś niepowodzenia i może być konieczne dalsze badania.

Można pobrać błędów, które powstaniu Chaos, można użyć API GetChaosReport (środowiska powershell, C# lub REST). Interfejs API pobiera następny segment Chaos raportu na podstawie token kontynuacji przekazany lub przekazany w czasie zakresie. Można określić ContinuationToken uzyskać następny segment raportu Chaos lub można określić zakres czasu za pośrednictwem StartTimeUtc i EndTimeUtc, ale nie można określić zarówno ContinuationToken, jak i zakres czasu, w tym samym wywołaniu. W przypadku więcej niż 100 zdarzeń Chaos raportu Chaos jest zwracany w segmentów gdy segment zawiera nie więcej niż 100 Chaos zdarzeń.

## <a name="important-configuration-options"></a>Opcje konfiguracji ważne
* **TimeToRun**: suma czasu uruchomionym programem Chaos przed zakończeniem pomyślnie. Można zatrzymać Chaos ma uruchomić w okresie TimeToRun za pośrednictwem interfejsu API StopChaos.

* **MaxClusterStabilizationTimeout**: maksymalną ilość czasu oczekiwania na klaster, aby stała się dobra przed utworzeniem ValidationFailedEvent. Jest to oczekiwania Aby zmniejszyć obciążenie klastra, gdy jest on odzyskiwanie. Testy wykonywane przez to:
  * Jeśli kondycja klastra jest OK
  * Jeśli kondycja usługi jest OK
  * Jeśli rozmiar zestawu replik docelowej uzyskuje się partycji usługi
  * Czy istnieją nie replik w stanie InBuild
* **MaxConcurrentFaults**: Maksymalna liczba jednoczesnych usterek, które są wywołane w każdej iteracji. Jest wyższy Chaos liczba, tym bardziej agresywną i tryb failover i kombinacji przejścia stanu, które przechodzą klastra są również bardziej złożone. 

> [!NOTE]
> Niezależnie od tego jak wysoka wartość *MaxConcurrentFaults* ma Chaos gwarantuje — w przypadku braku błędów zewnętrznych — nie utraty kworum lub utraty danych.
>

* **EnableMoveReplicaFaults**: Włącza lub wyłącza błędów, które powodują repliki podstawowej lub pomocniczej, aby przenieść. Te błędy są domyślnie włączone.
* **WaitTimeBetweenIterations**: ilość czasu między iteracji. Oznacza to, że czas Chaos zatrzyma się po wykonaniu round usterek i o zakończeniu odpowiednich sprawdzania poprawności kondycji klastra. Im wyższa wartość dolnej jest wskaźnikiem iniekcji średnią usterek.
* **WaitTimeBetweenFaults**: ilość czasu między dwa kolejne błędy w jednym iteracji. Im większa wartość, tym mniejszy współbieżności (lub nakładanie się między) błędów.
* **ClusterHealthPolicy**: zasad dotyczących kondycji klastra jest używany do sprawdzania poprawności kondycji klastra Between Chaos iteracji. Jeśli stan klastra jest wynikiem błędu lub sytuacji wystąpił nieoczekiwany wyjątek podczas wykonywania błąd Chaos będzie czekać przez 30 minut przed dalej — sprawdzenie kondycji - zapewnienie klastra recuperate trochę czasu.
* **Kontekst**: pary klucz wartość typu kolekcji (ciąg). Mapy może służyć do rejestrowania informacji o przebiegu Chaos. Nie może być więcej niż 100 takie pary, a każdy ciąg (klucz lub wartość) może mieć maksymalnie 4095 znaków. Ta mapa jest ustawiana przez starter milczenia Uruchom na przechowywanie w kontekście o określonym przebiegu.
* **ChaosTargetFilter**: ten filtr może służyć do docelowego Chaos błędów tylko dla określonych typów węzła lub tylko do niektórych wystąpień aplikacji. Jeśli ChaosTargetFilter nie jest używany, Chaos błędów wszystkich jednostek klastra. Użycie ChaosTargetFilter Chaos błędów jednostek spełniających specyfikację ChaosTargetFilter. NodeTypeInclusionList i ApplicationInclusionList umożliwiają tylko Unii semantyki. Innymi słowy nie jest możliwe określenie przecięcia NodeTypeInclusionList i ApplicationInclusionList. Na przykład nie jest możliwe określenie "fault tej aplikacji, tylko wtedy, gdy jest tego typu węzła". Gdy podmiot jest uwzględniony w NodeTypeInclusionList lub ApplicationInclusionList, tej jednostki nie można wykluczyć przy użyciu ChaosTargetFilter. Nawet wtedy, gdy nie ma applicationX ApplicationInclusionList, w niektórych iteracji Chaos applicationX może być uszkodzona ponieważ zdarza się w węźle nodeTypeY, który znajduje się w NodeTypeInclusionList. Jeśli zarówno NodeTypeInclusionList i ApplicationInclusionList jest zerowa lub pusta, zostanie zgłoszony ArgumentException.
    * **NodeTypeInclusionList**: listy typów węzłów do uwzględnienia w Chaos błędów. Wszystkie typy błędów (ponownie uruchomić węzeł, uruchom ponownie elementu codepackage usunąć replikę, uruchom ponownie repliki, Przenieś podstawowego i Przenieś dodatkowej) są włączone dla węzłów typu węzła. Jeśli nodetype (powiedzieć NodeTypeX) nie ma NodeTypeInclusionList, a następnie błędów poziomu węzła (na przykład NodeRestart) nigdy nie zostanie włączona dla węzłów NodeTypeX, ale błędów kodu pakietu i repliki nadal mogą być włączone dla NodeTypeX, jeśli aplikacja programu ApplicationInclusionList odbywa się w węźle NodeTypeX. Co najwyżej 100 nazwy typu węzła można dołączyć do tej listy, aby zwiększyć ten numer, konfiguracja MaxNumberOfNodeTypesInChaosTargetFilter wymagany jest uaktualnienie konfiguracji.
    * **ApplicationInclusionList**: wykaz aplikacji identyfikatorów URI do uwzględnienia w Chaos błędów. Wszystkie repliki należące do usługi te aplikacje są nadającymi się do repliki usterek (repliki ponownego uruchomienia, Usuń replikę, Przenieś podstawowe i pomocnicze przenoszenia) przez Chaos. Chaos może ponownie uruchomić pakietu kodu tylko wtedy, gdy pakiet kodu hostem repliki tylko te aplikacje. Jeśli aplikacja nie ma na liście, go może nadal być umieszczone w niektórych iteracji Chaos Jeśli kończenia aplikacji w węźle typu węzła, który jest incuded w NodeTypeInclusionList. Jednak jeśli applicationX jest związany z nodeTypeY za pośrednictwem ograniczenia umieszczania i applicationX jest nieobecny z ApplicationInclusionList i nodeTypeY jest nieobecny z NodeTypeInclusionList, następnie applicationX nigdy nie wystąpi błąd. Maksymalnie 1000 nazwy aplikacji można dołączyć do tej listy, aby zwiększyć ten numer, uaktualnienia konfiguracji jest wymagany dla konfiguracji MaxNumberOfApplicationsInChaosTargetFilter.

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
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities. 
            // During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase. 
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of states to uncover bugs. 
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record information about
            // the Chaos run. There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration. 
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };

            // All types of faults, restart node, restart code package, restart replica, move primary replica, and move secondary replica will happen
            // for nodes of type 'FrontEndType'
            var nodetypeInclusionList = new List<string> { "FrontEndType"};

            // In addition to the faults included by nodetypeInclusionList, 
            // restart code package, restart replica, move primary replica, move secondary replica faults will happen for 'fabric:/TestApp2'
            // even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is not of type included in nodeypeInclusionList.
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
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase. 
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of states to uncover bugs. 
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration. 
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state transitions that the cluster goes through. 
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record information about
# the Chaos run. There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

#List of cluster entities to target for Chaos faults.
$chaosTargetFilter = new-object -TypeName System.Fabric.Chaos.DataStructures.ChaosTargetFilter
$chaosTargetFilter.NodeTypeInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# All types of faults, restart node, restart code package, restart replica, move primary replica, and move secondary replica will happen
# for nodes of type 'FrontEndType'
$chaosTargetFilter.NodeTypeInclusionList.AddRange( [string[]]@("FrontEndType") )
$chaosTargetFilter.ApplicationInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# In addition to the faults included by nodetypeInclusionList, 
# restart code package, restart replica, move primary replica, move secondary replica faults will happen for 'fabric:/TestApp2'
# even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is not of type included in nodeypeInclusionList.
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
