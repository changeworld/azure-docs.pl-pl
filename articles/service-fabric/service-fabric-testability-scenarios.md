---
title: Tworzenie testów chaosu i pracy awaryjnej dla sieci szkieletowej usług Azure
description: Za pomocą testu chaosu sieci szkieletowej usług i scenariuszy testów pracy awaryjnej do wywoływania błędów i weryfikowania niezawodności usług.
author: motanv
ms.topic: conceptual
ms.date: 10/1/2019
ms.author: motanv
ms.openlocfilehash: 206b02024ad052a12e87cfdf1773815027e8aec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465539"
---
# <a name="testability-scenarios"></a>Scenariusze testowania
Duże systemy rozproszone, takie jak infrastruktura chmury, są z natury zawodne. Usługa Azure Service Fabric daje deweloperom możliwość pisania usług do uruchamiania na wierzchu za zawodne infrastruktury. Aby napisać wysokiej jakości usługi, deweloperzy muszą być w stanie wywołać tak zawodne infrastruktury, aby przetestować stabilność swoich usług.

Usługa analizy błędów daje deweloperom możliwość wywoływania akcji błędów do testowania usług w obecności błędów. Jednak ukierunkowane symulowane błędy dostaną Cię tylko do tej pory. Aby przejść do dalszych testów, można użyć scenariuszy testów w sieci szkieletowej usług: test chaosu i test pracy awaryjnej. Te scenariusze symulują ciągłe błędy przeplatane, zarówno wdzięku i niegraceny, w całym klastrze przez dłuższy czas. Gdy test jest skonfigurowany z szybkością i rodzaju błędów, można uruchomić za pośrednictwem interfejsów API języka C# lub programu PowerShell, aby wygenerować błędy w klastrze i usługi.

> [!WARNING]
> ChaosTestScenario jest zastępowany przez bardziej odporne, oparte na usługach Chaos. Więcej informacji można znaleźć w nowym artykule [Controlled Chaos.](service-fabric-controlled-chaos.md)
> 
> 

## <a name="chaos-test"></a>Test chaosu
Scenariusz chaosu generuje błędy w całym klastrze sieci szkieletowej usług. Scenariusz kompresuje błędy zwykle widoczne w miesiącach lub latach do kilku godzin. Kombinacja błędów przeplatanych z wysoką szybkością błędów znajduje przypadki narożników, które w przeciwnym razie zostały pominięte. Prowadzi to do znacznej poprawy jakości kodu usługi.

### <a name="faults-simulated-in-the-chaos-test"></a>Usterki symulowane w teście chaosu
* Ponowne uruchamianie węzła
* Ponowne uruchamianie wdrożonego pakietu kodu
* Usuwanie repliki
* Ponowne uruchamianie repliki
* Przenoszenie repliki podstawowej (opcjonalnie)
* Przenoszenie repliki pomocniczej (opcjonalnie)

Test chaosu uruchamia wiele iteracji błędów i sprawdzania poprawności klastra przez określony czas. Można również skonfigurować czas spędzony na ustabilizowanie klastra i pomyślne sprawdzanie poprawności. Scenariusz kończy się niepowodzeniem po naciśnięciu pojedynczego błędu w weryfikacji klastra.

Na przykład należy wziąć pod uwagę zestaw testów do uruchomienia przez jedną godzinę z maksymalnie trzech równoczesnych błędów. Test wywoła trzy błędy, a następnie sprawdź poprawność kondycji klastra. Test będzie iterować za pośrednictwem poprzedniego kroku, aż klaster staje się w złej kondycji lub jedna godzina przechodzi. Jeśli klaster staje się w złej kondycji w dowolnej iteracji, to znaczy nie stabilizuje się w skonfigurowanym czasie, test zakończy się niepowodzeniem z wyjątkiem. Ten wyjątek wskazuje, że coś poszło nie tak i wymaga dalszego zbadania.

W obecnej formie silnik generowania usterek w teście chaosu wywołuje tylko bezpieczne usterki. Oznacza to, że w przypadku braku błędów zewnętrznych kworum lub utraty danych nigdy nie nastąpi.

### <a name="important-configuration-options"></a>Ważne opcje konfiguracji
* **TimeToRun**: Całkowity czas, że test zostanie uruchomiony przed zakończeniem z powodzeniem. Test można zakończyć wcześniej zamiast niepowodzenia sprawdzania poprawności.
* **MaxClusterStabilizationTimeout**: Maksymalna ilość czasu oczekiwania na klaster, aby stać się w dobrej kondycji przed niepowodzeniem testu. Wykonywane kontrole są, czy kondycja klastra jest OK, kondycja usługi jest OK, rozmiar zestawu replik docelowych jest osiągnięty dla partycji usługi i nie istnieje repliki InBuild.
* **MaxConcurrentFaults**: Maksymalna liczba równoczesnych błędów wywołanych w każdej iteracji. Im wyższa liczba, tym bardziej agresywny test, co powoduje bardziej złożone kombinacje pracy awaryjnej i przejścia. Test gwarantuje, że w przypadku braku błędów zewnętrznych nie będzie kworum lub utraty danych, niezależnie od tego, jak wysoka jest ta konfiguracja.
* **EnableMoveReplicaFaults**: Włącza lub wyłącza błędy, które powodują przeniesienie repliki podstawowej lub pomocniczej. Te błędy są domyślnie wyłączone.
* **WaitTimeBetweenIterations**: Ilość czasu oczekiwania między iteracjami, czyli po rundzie błędów i odpowiedniej weryfikacji.

### <a name="how-to-run-the-chaos-test"></a>Jak uruchomić test chaosu
Przykład w języku C#

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```

PowerShell

Moduł Powershell sieci szkieletowej usługi zawiera dwa sposoby, aby rozpocząć scenariusz chaosu. `Invoke-ServiceFabricChaosTestScenario`jest oparty na kliencie, a jeśli komputer kliencki jest zamknięty w połowie testu, nie zostaną wprowadzone żadne dalsze błędy. Alternatywnie istnieje zestaw poleceń przeznaczonych do utrzymania testu działa w przypadku zamknięcia komputera. `Start-ServiceFabricChaos`używa stanowej i niezawodnej usługi systemowej o nazwie FaultAnalysisService, zapewniając, że błędy pozostaną wprowadzone do czasu, gdy nastąpi timetorun. `Stop-ServiceFabricChaos`można użyć do ręcznego zatrzymania `Get-ServiceFabricChaosReport` scenariusza i uzyska raport. Aby uzyskać więcej informacji, zobacz [odwołanie do sieci szkieletowej usługi Azure Powershell](https://docs.microsoft.com/powershell/module/servicefabric/?view=azureservicefabricps) i [wywoływanie kontrolowanego chaosu w klastrach sieci szkieletowej usług.](service-fabric-controlled-chaos.md)

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## <a name="failover-test"></a>Test pracy awaryjnej
Scenariusz testu trybu failover jest wersją scenariusza testu chaosu, który jest przeznaczony dla określonej partycji usługi. Testuje wpływ pracy awaryjnej na partycji określonej usługi, pozostawiając inne usługi bez zmian. Po skonfigurowaniu z informacjami o partycji docelowej i innymi parametrami działa jako narzędzie po stronie klienta, które używa interfejsów API języka C# lub programu PowerShell do generowania błędów dla partycji usługi. Scenariusz iteruje za pomocą sekwencji symulowanych błędów i sprawdzania poprawności usługi, podczas gdy logika biznesowa działa po stronie, aby zapewnić obciążenie. Błąd sprawdzania poprawności usługi wskazuje problem, który wymaga dalszego badania.

### <a name="faults-simulated-in-the-failover-test"></a>Usterki symulowane w teście pracy awaryjnej
* Uruchom ponownie wdrożony pakiet kodu, w którym znajduje się partycja
* Usuwanie repliki podstawowej/pomocniczej lub wystąpienia bezstanowego
* Uruchom ponownie podstawową replikę pomocniczą (jeśli utrwalona usługa)
* Przenoszenie repliki podstawowej
* Przenoszenie repliki pomocniczej
* Uruchom ponownie partycję

Test pracy awaryjnej wywołuje wybrany błąd, a następnie uruchamia sprawdzanie poprawności w usłudze, aby zapewnić jej stabilność. Test pracy awaryjnej wywołuje tylko jeden błąd naraz, w przeciwieństwie do możliwych wielu błędów w teście chaosu. Jeśli partycja usługi nie ustabilizuje się w ramach skonfigurowany limit czasu po każdej awarii, test zakończy się niepowodzeniem. Test wywołuje tylko bezpieczne usterki. Oznacza to, że w przypadku braku błędów zewnętrznych nie nastąpi kworum lub utrata danych.

### <a name="important-configuration-options"></a>Ważne opcje konfiguracji
* **PartitionSelector**: Selektor obiektu, który określa partycję, która musi być kierowana.
* **TimeToRun**: Całkowity czas, przez który test zostanie uruchomiony przed zakończeniem.
* **MaxServiceStabilizationTimeout:** Maksymalna ilość czasu oczekiwania na klaster, aby stać się w dobrej kondycji przed niepowodzeniem testu. Wykonywane kontrole są czy kondycja usługi jest OK, rozmiar zestawu replik docelowych jest osiągnięty dla wszystkich partycji i nie istnieje repliki InBuild.
* **WaitTimeBetweenFaults:** Ilość czasu oczekiwania między każdym błędem a cyklem sprawdzania poprawności.

### <a name="how-to-run-the-failover-test"></a>Jak uruchomić test trybu failover
**C #**

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario failoverScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await failoverScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


**Powershell**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```
