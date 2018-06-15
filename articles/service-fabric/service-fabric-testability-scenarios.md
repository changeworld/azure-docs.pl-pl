---
title: Tworzenie testów chaos i pracy awaryjnej dla usługi Azure mikrousług | Dokumentacja firmy Microsoft
description: Przy użyciu usługi Service Fabric chaos testu i pracy awaryjnej przetestować scenariusze wywołać błędów i sprawdź niezawodności usług.
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
ms.openlocfilehash: d9c05ba2b98af5ef26ef5b5a7ae0995512df4e75
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208757"
---
# <a name="testability-scenarios"></a>Scenariusze testowania
Dużych systemach rozproszonych jak infrastruktury chmury jest z założenia gwarantowane. Sieć szkieletowa usług Azure zapewnia deweloperom możliwość zapisu do uruchamiania na szczycie infrastruktury niepewna. Aby można było zapisać wysokiej jakości usług, deweloperzy muszą móc wywoływać takie zawodnych infrastruktury do testowania stabilność swoich usług.

Usługi analizy błędów zapewnia deweloperom możliwość wywołać błędów działań do przetestowania usługi w razie awarii. Jednak docelowe błędów symulowane programy tylko do tej pory. Podejmowanie testowania, możesz skorzystać ze scenariuszy testu w sieci szkieletowej usług: chaos test i test pracy awaryjnej. Te scenariusze symulować ciągłego przeplotem błędów, zarówno bezpieczne i nieprawidłowego w całym klastrze przez dłuższy czas. Po skonfigurowaniu testu z szybkość i rodzaju błędów można było go uruchomić za pomocą interfejsów API języka C# lub programu PowerShell w celu generowania błędów w klastrze i usługi.

> [!WARNING]
> ChaosTestScenario jest zastępowany przez Chaos bardziej elastyczne, oparta na usłudze. Zapoznaj się z nowego artykułu [kontrolowane Chaos](service-fabric-controlled-chaos.md) więcej szczegółów.
> 
> 

## <a name="chaos-test"></a>Chaos testu
Scenariusz chaos generuje błędy w ramach całego klastra sieci szkieletowej usług. Scenariusz kompresuje błędów zwykle widoczny w miesięcy lub lat do kilku godzin. Kombinacja przeplotem błędów ze wskaźnikiem wysoką odporność znajduje sytuacjach wyjątkowych, które w przeciwnym razie zostaną pominięte. Prowadzi to do znacznej poprawy jakości kodu usługi.

### <a name="faults-simulated-in-the-chaos-test"></a>Błędy w teście chaos
* Uruchom ponownie węzeł
* Ponowne uruchomienie wdrożonego pakietu kodu
* Usuwanie repliki
* Uruchom ponownie repliki
* Przenieś repliki podstawowej (opcjonalnie)
* Przenieś repliki pomocniczej (opcjonalnie)

Chaos test uruchamia przejść przez wiele iteracji usterek i sprawdzanie poprawności klastra w określonym okresie czasu. Konfiguruje się czas dla klastra do utrwalania i Weryfikacja powiodła się. Scenariusz nie powiedzie się, gdy naciśniesz pojedynczego uszkodzenia w weryfikacji klastra.

Rozważmy na przykład uruchamiana przez godzinę z maksymalnie trzech równoczesnych błędów testu. Testu wywoływać błędy trzy, a następnie sprawdź stan klastra. Test zostanie iterację poprzedniego kroku, dopóki klastra staje się nieprawidłowy, lub przekazuje jedną godzinę. Jeśli klaster staje się nieprawidłowy, w dowolnym iteracji, tj. nie ustabilizowania w skonfigurowanym czasie, test zakończy się niepowodzeniem z powodu wyjątku. Ten wyjątek wskazuje, że coś niepowodzenia i wymaga dalszego badania.

W postaci bieżącego aparatu generowania błędów w teście chaos wywołuje tylko bezpiecznych błędów. Oznacza to, że w przypadku braku błędów zewnętrznych utraty kworum lub dane nigdy nie wystąpi.

### <a name="important-configuration-options"></a>Opcje konfiguracji ważne
* **TimeToRun**: całkowity czas uruchomienia testu przed zakończeniem pomyślnie. Test zakończenia wcześniej zamiast niepowodzenia weryfikacji.
* **MaxClusterStabilizationTimeout**: maksymalna ilość czasu oczekiwania na klaster, aby stała się dobra przed niepowodzeniem testu. Testy wykonywane są czy klastra kondycja jest dobra, usługa kondycji jest OK uzyskuje się rozmiar docelowego zestawu replik partycji usługi i replik w stanie InBuild nie istnieje.
* **MaxConcurrentFaults**: Maksymalna liczba jednoczesnych błędów powstaniu w każdej iteracji. Wyższa wartość, bardziej aktywnego testu, dlatego co zapewnia bardziej złożonych przechodzenia w tryb failover i kombinacje przejścia. Test gwarantuje, że w przypadku braku błędów zewnętrznych nie będzie kworum lub utraty danych, niezależnie od tego, jak wysoka jest w tej konfiguracji.
* **EnableMoveReplicaFaults**: Włącza lub wyłącza usterek, które powodują przeniesienie repliki podstawowej lub dodatkowej. Te błędy są domyślnie wyłączone.
* **WaitTimeBetweenIterations**: czas oczekiwania pomiędzy iteracjami po round usterek i odpowiednie sprawdzania poprawności.

### <a name="how-to-run-the-chaos-test"></a>Jak uruchomić chaos test
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
Scenariusz test pracy awaryjnej jest wersja chaos scenariusza testowego przeznaczonego dla partycji określonej usługi. Sprawdzenie wpływ trybu failover na partycji określonej usługi, pozostawiając nie dotyczy innych usług. Po skonfigurowaniu o innych parametrach i informacji o partycji docelowej jest uruchamiany jako narzędzie po stronie klienta, które korzysta z interfejsów API języka C# lub programu PowerShell do generowania błędów dla partycji usługi. Scenariusz iterację sekwencji symulowane usterek i weryfikacji usługi podczas wykonywania logiki biznesowej po stronie, zapewnienie obciążenia. Błąd podczas weryfikowania usługi wskazuje problem, który wymaga dalszego badania.

### <a name="faults-simulated-in-the-failover-test"></a>Błędy w test pracy awaryjnej
* Ponowne uruchomienie wdrożonego pakietu kodu gdzie jest hostowana partycji
* Usuń podstawowe i pomocnicze repliki lub bezstanowych wystąpienia
* Uruchom ponownie podstawowej repliki pomocniczej (Jeśli usługa utrwalonego)
* Przenieś repliki podstawowej
* Przenieś replikę pomocniczą
* Uruchom ponownie partycji

Test pracy awaryjnej powoduje odporność wybrany, a następnie uruchomi sprawdzanie poprawności w usłudze, aby zapewnić jego stabilność. Test pracy awaryjnej wywołuje tylko jeden błąd w czasie, w przeciwieństwie do możliwości wiele błędów w teście chaos. Partycji usługi nie stabilizacji w ciągu skonfigurowanego limitu czasu po awarii każdego testu nie powiedzie się. Test wywołuje tylko bezpiecznych błędów. Oznacza to, że w przypadku braku błędów zewnętrznych, utrata danych lub kworum nie nastąpi.

### <a name="important-configuration-options"></a>Opcje konfiguracji ważne
* **Elementu PartitionSelector**: selektora obiekt, który określa partycji, który ma być celem.
* **TimeToRun**: całkowity czas uruchomienia testu przed zakończeniem.
* **MaxServiceStabilizationTimeout**: maksymalna ilość czasu oczekiwania na klaster, aby stała się dobra przed niepowodzeniem testu. Testy wykonywane są Określa, czy usługa kondycji jest OK rozmiar docelowego zestawu replik odbywa się dla wszystkich partycji i replik w stanie InBuild nie istnieje.
* **WaitTimeBetweenFaults**: ilość czasu między każdym cyklu usterek i sprawdzania poprawności.

### <a name="how-to-run-the-failover-test"></a>Jak uruchomić test trybu failover
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
