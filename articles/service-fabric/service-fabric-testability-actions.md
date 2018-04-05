---
title: Symulacji awarii w Azure mikrousług | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje o akcji zmianę w usługi sieć szkieletowa usług Microsoft Azure.
services: service-fabric
documentationcenter: .net
author: motanv
manager: timlt
editor: toddabel
ms.assetid: ed53ca5c-4d5e-4b48-93c9-e386f32d8b7a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: motanv;heeldin
ms.openlocfilehash: c8ddc7732999ae555323bebaef60aa34c8f2ec17
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="testability-actions"></a>Testowania czynności
Aby symulować zawodnych infrastruktury, sieć szkieletowa usług Azure udostępnia Tobie, deweloperze, ze sposobów, aby symulować różne błędy rzeczywistych i przejścia stanu. Są one widoczne jako testowania czynności. Akcje są niskiego poziomu interfejsów API, które powodują iniekcji określonych błędów, przejście stanu lub sprawdzania poprawności. Łącząc te akcje można zapisywać scenariusze kompleksowego testowania dla usług.

Sieć szkieletowa usług zawiera kilka typowych scenariuszy testu składa się z tych akcji. Zdecydowanie zaleca się korzystanie z tych wbudowanych scenariuszy, które dokładnie chcą wspólnej przejść stanu i przypadków awarii. Jednak akcje może służyć do tworzenia scenariuszy testowania niestandardowych, jeśli chcesz dodać pokrycia w scenariuszach, które nie są objęte wbudowanych scenariusze jeszcze lub które są niestandardowe dostosowane do aplikacji.

C# implementacje akcje znajdują się w zestawie System.Fabric.dll. Moduł PowerShell sieci szkieletowej systemu znajduje się w zestawie Microsoft.ServiceFabric.Powershell.dll. W ramach instalacji środowiska uruchomieniowego zainstalowano modułu ServiceFabric programu PowerShell umożliwiające łatwość użycia.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Bezpieczne, a błąd nieprawidłowego działania
Testowania czynności dzieli się na dwie główne pakiety:

* Błędy nieprawidłowego: te błędy symulacji awarii, takie jak ponowne uruchomienie komputera i awarii procesów. W takich przypadkach niepowodzenia kontekstu wykonywania procesu zatrzymuje nagle. Oznacza to, że nie oczyszczania stanu można uruchomić, zanim uruchamiania aplikacji ponownie.
* Błędy bezpiecznie: te błędy symulować łagodne akcje jak przenosi repliki i porzucania wyzwalane przez równoważenia obciążenia. W takiej sytuacji usługa pobiera powiadomienie po zamknięciu i wyczyścić stanu przed zakończeniem.

Podczas wywołania różnych błędów bezpieczne i nieprawidłowego lepszą jakość sprawdzania poprawności, uruchamiać obciążenie usługi i biznesowych. Błędy nieprawidłowego wykonuje scenariuszy, w którym proces usługi nagle kończy środku niektórych przepływu pracy. Testy ścieżka odzyskiwania, po przywróceniu repliki usługi przez sieć szkieletowa usług. To może pomóc w testowania spójności danych i określa, czy stan usługi jest prawidłowo utrzymywany po awarii. Zestaw błędów (awarii bezpieczne) przetestować, czy usługa poprawnie reaguje na replik przenoszenie przez sieć szkieletowa usług. Obsługa anulowania to testów w metodzie RunAsync. Usługa musi Sprawdź, czy token anulowania jest ustawiona, poprawnie zapisać jej stan, a następnie zamknij metodzie RunAsync.

## <a name="testability-actions-list"></a>Pola listy Akcje
| Akcja | Opis | Zarządzanego interfejsu API | Polecenia cmdlet programu PowerShell | Bezpieczne/nieprawidłowego błędów |
| --- | --- | --- | --- | --- |
| CleanTestState |Usuwa wszystkie stanu testu z klastra w przypadku zły wyłączania sterownika testu. |CleanTestStateAsync |Remove-ServiceFabricTestState |Nie dotyczy |
| InvokeDataLoss |Powoduje utratę danych na partycji usługi. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Bezpieczne |
| InvokeQuorumLoss |Umieszcza partycji danej usługi stanowej, w wyniku utraty kworum. |InvokeQuorumLossAsync |Invoke-ServiceFabricQuorumLoss |Bezpieczne |
| Przenoszenie podstawowej |Przenosi określony repliką podstawową usługi stanowej określony węzeł klastra. |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |Bezpieczne |
| Przenieś pomocniczej |Przenosi bieżący pomocniczej replice usługi stanowej na inny węzeł klastra. |MoveSecondaryAsync |Move-ServiceFabricSecondaryReplica |Bezpieczne |
| RemoveReplica |Symuluje awarii repliki poprzez usunięcie repliki z klastra. To spowoduje zamknięcie repliki i przenieść ją do roli "None", usunięcie wszystkich jego stanu z klastra. |RemoveReplicaAsync |Remove-ServiceFabricReplica |Bezpieczne |
| RestartDeployedCodePackage |Symuluje awarii procesu pakietu kodu przez ponowne uruchomienie pakiet kodu wdrożonych na węzłach w klastrze. Przerywa ten proces pakietu kodu, który zostanie uruchomiony ponownie wszystkie użytkownika repliki usługi hostowanej w tym procesie. |RestartDeployedCodePackageAsync |Restart-ServiceFabricDeployedCodePackage |Nieprawidłowego |
| RestartNode |Symuluje awarii węzła klastra sieci szkieletowej usług przez ponowne uruchomienie węzła. |RestartNodeAsync |Restart-ServiceFabricNode |Nieprawidłowego |
| RestartPartition |Symuluje scenariusza niedostępności klastra lub niedostępności centrum danych przez ponowne uruchomienie niektórych lub wszystkich replik partycji. |RestartPartitionAsync |Restart-ServiceFabricPartition |Bezpieczne |
| RestartReplica |Symuluje awarii repliki ponownego uruchamiania utrwalonych repliki w klastrze, zamykając repliki i otworzyć go ponownie. |RestartReplicaAsync |Restart-ServiceFabricReplica |Bezpieczne |
| Parametr StartNode |Rozpoczyna się węzeł w klastrze, który jest już zatrzymana. |StartNodeAsync |Start-ServiceFabricNode |Nie dotyczy |
| StopNode |Symuluje awarii węzła przez zatrzymanie węzła w klastrze. Węzeł pozostanie w dół do momentu parametr StartNode jest wywoływana. |StopNodeAsync |Stop-ServiceFabricNode |Nieprawidłowego |
| ValidateApplication |Sprawdza dostępność i kondycję wszystkich usług sieci szkieletowej usług aplikacji, zwykle po wywołania niektórych błędów w systemie. |ValidateApplicationAsync |Test-ServiceFabricApplication |Nie dotyczy |
| ValidateService |Sprawdza dostępność i kondycji usługi Service Fabric, zwykle po wywołania niektórych błędów w systemie. |ValidateServiceAsync |Test-ServiceFabricService |Nie dotyczy |

## <a name="running-a-testability-action-using-powershell"></a>Uruchomienie akcji testowania przy użyciu programu PowerShell
Ten samouczek przedstawia sposób uruchamiania działania testowania przy użyciu programu PowerShell. Dowiesz się sposób uruchamiania działania kontroli względem klastra lokalnego (jeden pole) lub klastrze platformy Azure. Microsoft.Fabric.Powershell.dll — moduł programu PowerShell usługi Service Fabric — jest instalowana automatycznie podczas instalowania MSI sieci szkieletowej usług firmy Microsoft. Moduł jest ładowane automatycznie podczas otwierania wiersza programu PowerShell.

Samouczek segmentów:

* [Uruchom akcję w odniesieniu do klastra z jednym polu](#run-an-action-against-a-one-box-cluster)
* [Uruchom akcję względem klastra platformy Azure](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Uruchom akcję w odniesieniu do klastra z jednym polu
W celu uruchomienia działania kontroli klastra lokalnego, połącz się z klastrem i otwórz wiersz polecenia programu PowerShell w trybie administratora. Daj nam przyjrzeć się **ServiceFabricNode ponowne uruchomienie** akcji.

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Tutaj akcji **ServiceFabricNode ponowne uruchomienie** jest uruchamiana w węźle o nazwie "Node1". Tryb uzupełniania Określa, że także powinien sprawdza, czy ponowne uruchomienie węzła akcji faktycznie zakończyło się pomyślnie. Określanie tryb uzupełniania jako "Weryfikuj" spowoduje, że należy sprawdzić, czy akcja ponownego uruchomienia faktycznie zakończyło się pomyślnie. Zamiast bezpośrednio określać węzła przy użyciu nazwy, możesz je określić za pomocą klucza partycji i rodzaj repliki, w następujący sposób:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Ponowne uruchomienie ServiceFabricNode** należy ponownie uruchomić węzeł sieci szkieletowej usług w klastrze. Spowoduje to zatrzymanie proces Fabric.exe, który zostanie uruchomiony ponownie wszystkie system usługi i użytkowników usługi repliki obsługiwane w tym węźle. Przy użyciu tego interfejsu API, aby przetestować usługi pomaga odkrywanie usterki wzdłuż ścieżki odzyskiwania trybu failover. Pomaga symulacji awarii węzła w klastrze.

Poniższy zrzut ekranu przedstawia **ServiceFabricNode ponowne uruchomienie** polecenia kontroli w akcji.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

Dane wyjściowe pierwszego **Get-ServiceFabricNode** (polecenia cmdlet z modułu programu PowerShell usługi Service Fabric) pokazuje, że klaster lokalny zawiera pięć węzłów: Node.1 do Node.5. Po testowania czynności (polecenia cmdlet) **ServiceFabricNode ponowne uruchomienie** jest wykonywana w węźle o nazwie Node.4, widzimy zresetowano węzła przestojów.

### <a name="run-an-action-against-an-azure-cluster"></a>Uruchom akcję względem klastra platformy Azure
Uruchomienie akcji pola (przy użyciu programu PowerShell) względem klastra platformy Azure jest podobny do uruchamiania działania klastra lokalnego. Jedyna różnica polega na tym, że przed uruchomieniem tej akcji, zamiast nawiązywania połączenia z lokalnym klastrem, należy połączyć się z klastrem Azure najpierw.

## <a name="running-a-testability-action-using-c35"></a>Uruchomienie akcji testowania za pomocą C&#35;
Aby uruchomić akcję testowania przy użyciu języka C#, musisz najpierw Połącz się z klastrem przy użyciu klienta fabricclient z rolą. Następnie Uzyskaj parametrów wymaganych do uruchomienia akcji. Można użyć różnych parametrów do uruchomienia tego samego działania.
Jednym ze sposobów uruchamiania patrzeć akcji RestartServiceFabricNode, jest przy użyciu informacji węzła (nazwy węzła i identyfikator wystąpienia węzła) w klastrze.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Objaśnienie parametrów:

* **CompleteMode** Określa, czy tryb nie należy sprawdzić, czy akcja ponownego uruchomienia faktycznie zakończyło się pomyślnie. Określanie tryb uzupełniania jako "Weryfikuj" spowoduje, że należy sprawdzić, czy akcja ponownego uruchomienia faktycznie zakończyło się pomyślnie.  
* **OperationTimeout** ustawia czas operacji przed jest zgłaszany wyjątek TimeoutException.
* **CancellationToken** umożliwia oczekujące wywołanie do anulowania.

Zamiast bezpośrednio określać węzła przy użyciu nazwy, możesz je określić za pomocą klucza partycji i rodzaj repliki.

Aby uzyskać więcej informacji, zobacz [elementu PartitionSelector i elementu ReplicaSelector](#partition_replica_selector).

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>Elementu PartitionSelector i elementu ReplicaSelector
### <a name="partitionselector"></a>Elementu PartitionSelector
Elementu PartitionSelector jest pomocnika ujawnione podczas testowania i jest używany do wybierania określonej partycji, na którym należy wykonać czynności kontroli. Można ją wybrać określonej partycji, jeśli identyfikator partycji jest znany wcześniej. Lub, możesz podać klucz partycji i operacji wewnętrznie rozwiąże Identyfikatora partycji. Masz również wybrać losowe partycji.

Aby użyć tego pomocnika, Utwórz obiekt elementu PartitionSelector i wybierz partycję za pomocą jednej z metod Select *. Następnie przekaż obiekt elementu PartitionSelector do interfejsu API, która go wymaga. Jeśli wybrano opcję nie, domyślnie losowe partycji.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>Elementu ReplicaSelector
Elementu ReplicaSelector jest pomocnika ujawnione podczas testowania i służą do wybierz replikę, na którym należy wykonać czynności kontroli. Można ją wybrać określonych repliki, jeśli identyfikator repliki jest znany wcześniej. Ponadto istnieje możliwość wybrania repliki podstawowej lub pomocniczej losowe. Elementu ReplicaSelector pochodzi z elementu PartitionSelector, dlatego należy wybrać zarówno repliki i partycji, na którym chcesz wykonać operacji kontroli.

Aby użyć tego pomocnika, Utwórz obiekt elementu ReplicaSelector i skonfigurowane w sposób, aby wybrać repliki i partycji. Można następnie przekazać do interfejsu API, która go wymaga. Jeśli wybrano opcję nie, domyślnie losowe repliki i losowe partycji.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>Kolejne kroki
* [Scenariusze testowania](service-fabric-testability-scenarios.md)
* Jak przetestować usługi
  * [Symulacji awarii podczas obciążeń usługi](service-fabric-testability-workload-tests.md)
  * [Błędy usługi do komunikacji](service-fabric-testability-scenarios-service-communication.md)

