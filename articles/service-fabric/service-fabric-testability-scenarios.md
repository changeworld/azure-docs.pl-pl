---
title: Utwórz testy chaotyczne i pracy awaryjnej dla usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Za pomocą usługi Service Fabric chaos testu i trybu failover przetestować scenariusze wywoływać błędy i weryfikację niezawodność usług.
services: service-fabric
documentationcenter: .net
author: motanv
manager: rsinha
editor: toddabel
ms.assetid: 8eee7e89-404a-4605-8f00-7e4d4fb17553
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: d12c5097d4ba5e0ccfe0e2b2cbc8ccd758c32d98
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60865026"
---
# <a name="testability-scenarios"></a>Scenariuszy testowalności
Dużych systemach rozproszonych jak infrastruktur chmury są założenia wiarygodne. Usługa Azure Service Fabric umożliwia deweloperom pisanie usług do uruchomienia na szczycie infrastruktury zawodne. Pisanie wysokiej jakości usług, w celu deweloperzy muszą być w stanie wywoływać takie zawodnych infrastruktury do testowania stabilność usług.

Usługa analizy błędów daje deweloperom możliwość wywołują akcje błędów do testowania usług w razie awarii. Jednak docelowych błędów symulowane uzyskasz tylko do tej pory. Scenariusze testowania podejmowanie testowania, można użyć w usłudze Service Fabric: chaos test i test pracy awaryjnej. Te scenariusze symulowanie ciągłe przeplotem błędów, zarówno bezpieczne, jak i nieprawidłowego, w całym klastrze przez dłuższy czas. Po skonfigurowaniu testu z szybkości i rodzaju błędów, może być uruchamiany za pośrednictwem interfejsów API języka C# lub programu PowerShell w celu generowania błędy w klastrze i usługi.

> [!WARNING]
> ChaosTestScenario jest zastępowany przez Chaos bardziej odporne na błędy, opartych na usługach. Można znaleźć w artykule nowe [kontrolowane Chaos](service-fabric-controlled-chaos.md) Aby uzyskać więcej informacji.
> 
> 

## <a name="chaos-test"></a>Test chaosu
Scenariusz chaos generuje błędy dla całego klastra usługi Service Fabric. Scenariusz kompresuje błędy zazwyczaj występuje w ciągu miesięcy lub lat do kilku godzin. Kombinacja przeplotem błędów przy użyciu współczynnika wysoką odporność umożliwia znalezienie przypadki brzegowe, które w przeciwnym razie zostaną pominięte. Prowadzi to do znacznej poprawy jakości kodu usługi.

### <a name="faults-simulated-in-the-chaos-test"></a>Błędy symulowane w teście chaosu
* Ponowne uruchomienie węzła
* Uruchom ponownie pakiet wdrożonego kodu
* Usuwanie repliki
* Uruchom ponownie replikę
* Przenieś repliki podstawowej (opcjonalnie)
* Przenieś tylko do repliki pomocniczej (opcjonalnie)

Chaos test działa wiele iteracji, błędów i sprawdzanie poprawności klastra w określonym okresie czasu. Konfiguruje się czas dla klastra to stabilizację i sprawdzanie poprawności zakończyło się sukcesem. Scenariusz kończy się niepowodzeniem, gdy naciśniesz pojedynczego uszkodzenia podczas sprawdzania poprawności klastra.

Rozważmy na przykład test do uruchamiania na jedną godzinę z maksymalnie trzech współbieżnych błędów. Test occurs trzy błędów, a następnie sprawdzania poprawności kondycji klastra. Test iteracji przez poprzedniego kroku do momentu złej kondycji klastra lub przekazuje jedną godzinę. Jeśli klaster staje się zła w dowolną iterację, czyli nie stabilizacji w skonfigurowanym czasie, test zakończy się niepowodzeniem z powodu wyjątku. Ten wyjątek wskazuje, że wystąpił problem i musi bliższe zbadanie tej sprawy.

W jego obecnej formie aparatu generowania błędów w teście chaos wywołuje tylko bezpiecznych błędów. Oznacza to, że w przypadku braku błędów zewnętrznych kworum lub utraty danych nigdy nie wystąpi.

### <a name="important-configuration-options"></a>Opcje konfiguracji ważne
* **Wartość TimeToRun**: Łączny czas testu przed zakończeniem sukcesem. Test może zakończyć wcześniej audytów niepowodzenia weryfikacji.
* **MaxClusterStabilizationTimeout**: Maksymalna ilość czasu oczekiwania klaster poprawi przed niepowodzeniem testu. Kontrole wykonywane są, czy kondycja klastra jest OK, usługa kondycji jest dobry, uzyskuje się rozmiaru docelowego zestawu replik partycji usługi i replik w stanie InBuild nie istnieje.
* **MaxConcurrentFaults**: Maksymalna liczba współbieżnych błędów wywołanego w każdej iteracji. Wyższa wartość, bardziej aktywnego testu, dlatego skutkuje bardziej złożonych operacji Failover i kombinacji przejścia. Test gwarantuje, że w przypadku braku błędów zewnętrznych nie będzie kworum lub utraty danych, niezależnie od tego, jak wysoka jest w tej konfiguracji.
* **EnableMoveReplicaFaults**: Włącza lub wyłącza błędy, które powodują przeniesienie repliki podstawowej lub dodatkowej. Te błędy są domyślnie wyłączone.
* **WaitTimeBetweenIterations**: Ilość czasu oczekiwania między poszczególnymi iteracjami po round błędów i odpowiedni sprawdzania poprawności.

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
Scenariusz testu w trybie failover jest wersją chaos scenariusza testu, który jest przeznaczony dla partycji określonej usługi. Sprawdza efekt przejścia w tryb failover na partycji określonej usługi, pozostawiając inne usługi nie ma wpływu. Po skonfigurowaniu przy użyciu informacji o partycji docelowej oraz innych parametrów, jest uruchamiana jako narzędzie po stronie klienta, które używa interfejsów API języka C# lub programu PowerShell do wygenerowania błędów na potrzeby partycji usługi. Scenariusz iterację sekwencja symulowane błędów i weryfikacji usługi podczas logiki biznesowej po stronie, zapewnienie obciążenia. Wystąpił błąd podczas weryfikacji usługi wskazuje problem, który wymaga dalszego badania.

### <a name="faults-simulated-in-the-failover-test"></a>Błędy w test pracy awaryjnej
* Uruchom ponownie pakiet wdrożonego kodu, w którym jest hostowana partycja
* Usuwanie repliki podstawowe i pomocnicze lub bezstanowych wystąpienia
* Uruchom ponownie podstawowej repliki pomocniczej (Jeśli usługa utrwalona)
* Przenieś replika podstawowa
* Przenieś replika pomocnicza
* Uruchom ponownie partycję

Test pracy awaryjnej wywołuje wybranej usterki, a następnie uruchamia sprawdzania poprawności w usłudze, aby zapewnić jego stabilność. Test pracy awaryjnej wywołuje tylko jeden błąd w czasie, w przeciwieństwie do to możliwe, wiele błędów w teście chaos. Partycji Usługa docelowa nie stabilizacji przed upływem skonfigurowanego limitu czasu po każdym błędów, test kończy się niepowodzeniem. Test wywołuje tylko bezpiecznych błędów. Oznacza to, że w przypadku braku błędów zewnętrznych, utraty danych lub kworum nie nastąpi.

### <a name="important-configuration-options"></a>Opcje konfiguracji ważne
* **PartitionSelector**: Obiekt selektor, który określa partycję, która musi być obiektem.
* **Wartość TimeToRun**: Łączny czas testu przed zakończeniem.
* **MaxServiceStabilizationTimeout**: Maksymalna ilość czasu oczekiwania klaster poprawi przed niepowodzeniem testu. Kontrole wykonywane są, czy usługa kondycji jest OK, rozmiaru docelowego zestawu replik odbywa się dla wszystkich partycji i replik w stanie InBuild nie istnieje.
* **WaitTimeBetweenFaults**: Ilość czasu oczekiwania między każdym cyklu błędów i sprawdzanie poprawności.

### <a name="how-to-run-the-failover-test"></a>Jak przeprowadzić test pracy awaryjnej
**C#**

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


**Program PowerShell**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```
