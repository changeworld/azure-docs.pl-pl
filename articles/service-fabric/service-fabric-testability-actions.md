---
title: Symulowanie błędów w mikrousług platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje o akcji testowalności znaleziony w usłudze Microsoft Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: motanv
manager: chackdan
editor: heeldin
ms.assetid: ed53ca5c-4d5e-4b48-93c9-e386f32d8b7a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: 37a794387f3a2f02124805705d380ad9f1fc1270
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662860"
---
# <a name="testability-actions"></a>Akcje testowalności
Aby symulować zawodnych infrastruktury, usługi Azure Service Fabric zapewnia, developer, przy użyciu metody symulowanie różnych rzeczywistych błędów i stanów przejść. Są one widoczne jako akcji testowalności. Interfejsy API niskiego poziomu, powodującą, że technika wstrzykiwania błędów określonych, zmiany stanu lub sprawdzania poprawności są następujące akcje. Łącząc te akcje, można napisać scenariuszy testowych w kompleksowe dla usług.

Usługa Service Fabric udostępnia kilka typowych scenariuszy testowych, który składa się z tych akcji. Firma Microsoft zdecydowanie zaleca się korzystanie z tych wbudowanych scenariusze, które są uważnie dobrać do testowania wspólnej stanami i przypadki awarii. Akcje można jednak utworzyć niestandardowe scenariusze testowania, gdy chcesz dodać pokrycia dla scenariuszy, które nie są objęte wbudowanych scenariuszy jeszcze lub które są niestandardowe dostosowane do Twojej aplikacji.

C#implementacje akcje znajdują się w zestawie System.Fabric.dll. Moduł programu PowerShell Service Fabric w systemie znajduje się w zestawie Microsoft.ServiceFabric.Powershell.dll. W ramach instalacji środowiska uruchomieniowego moduł ServiceFabric programu PowerShell jest instalowany umożliwiają łatwość użycia.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Łagodne a błędów nieprawidłowego działania
Akcje testowalności dzieli się na dwóch głównych przedziałów:

* Nieprawidłowego błędów: Te błędy symulacji awarii, takie jak ponowne uruchomienie maszyny i przetwarzanie awarie. W takich przypadkach niepowodzenia kontekstu wykonania procesu zatrzymuje nagle. Oznacza to, że żadne Oczyszczanie stanu można uruchomić, zanim aplikacja uruchamia ponownie.
* Łagodne błędów: Te błędy symulować łagodne akcji, takich jak przenosi repliki i spadnie wyzwolone przez równoważenie obciążenia sieciowego. W takich przypadkach usługa pobiera powiadomienia po zamknięciu i wyczyścić stanu przed zamknięciem.

Dla lepszej jakości sprawdzania poprawności należy uruchomić obciążenie usługi i biznesowe podczas wywołania różnych błędów łagodne i nieprawidłowego. Błędy nieprawidłowego korzystają ze scenariuszy, w którym proces usługi nagle kończy działanie w trakcie wykonywania niektórych przepływu pracy. Sprawdza ścieżkę odzyskiwania, po przywróceniu repliki usługi przez usługę Service Fabric. Ułatwi to przetestować wyjaśnienie pojęcia spójności danych i tego, czy stan usługi jest utrzymywany prawidłowo po awarii. Zestaw błędów (błędy łagodne) testowanie, czy usługa jest prawidłowo reaguje na repliki przenoszenie przez usługę Service Fabric. Testuje obsługi anulowania w metodzie RunAsync. Usługa musi sprawdzić, czy token anulowania jest ustawiona, poprawnie zapisać swój stan i wyjście z metody RunAsync.

## <a name="testability-actions-list"></a>Lista akcji testowalności
| Akcja | Opis | Zarządzany interfejs API | Polecenia cmdlet programu PowerShell | Łagodne/nieprawidłowego błędów |
| --- | --- | --- | --- | --- |
| CleanTestState |Usuwa wszystkie stan testu z klastra, w przypadku nieprawidłowe zamknięcie sterownik testu. |CleanTestStateAsync |Remove-ServiceFabricTestState |Nie dotyczy |
| InvokeDataLoss |Powoduje utratę danych na partycji usługi. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Łagodne |
| InvokeQuorumLoss |Umieszcza partycji danej usługi stanowe z utraciła kworum. |InvokeQuorumLossAsync |Invoke-ServiceFabricQuorumLoss |Łagodne |
| MovePrimary |Przenosi określony replice podstawowej usługi stanowej określony węzeł klastra. |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |Łagodne |
| MoveSecondary |Przenosi bieżący repliki pomocniczej usługi stanowej na inny węzeł klastra. |MoveSecondaryAsync |Move-ServiceFabricSecondaryReplica |Łagodne |
| RemoveReplica |Symuluje awarię repliki, usuwając replikę z klastra. To spowoduje zamknięcie repliki i przenieść ją do roli "None", usuwając wszystkie jego stan z klastra. |RemoveReplicaAsync |Remove-ServiceFabricReplica |Łagodne |
| RestartDeployedCodePackage |Symuluje awarię procesu pakietu kodu przez ponowne uruchomienie pakietu kodu wdrożonych na węzłach w klastrze. Przerywa ten proces pakietu kodu, który spowoduje ponowne uruchomienie wszystkich użytkowników usługi replik hostowanych w tym procesie. |RestartDeployedCodePackageAsync |Restart-ServiceFabricDeployedCodePackage |Nieprawidłowego |
| RestartNode |Symuluje awarii węzła klastra usługi Service Fabric przez ponowne uruchomienie węzła. |RestartNodeAsync |Restart-ServiceFabricNode |Nieprawidłowego |
| RestartPartition |Symuluje scenariusz niedostępności centrum danych, jak czas niedostępności lub klastra przez ponowne uruchomienie niektórych lub wszystkich replik partycji. |RestartPartitionAsync |Restart-ServiceFabricPartition |Łagodne |
| RestartReplica |Symuluje awarię repliki ponowne uruchomienie utrwalonych repliki w klastrze, zamykając repliki i otworzyć go ponownie. |RestartReplicaAsync |Restart-ServiceFabricReplica |Łagodne |
| Węzeł_początkowy |Rozpoczyna się węzeł w klastrze, na którym jest już zatrzymana. |StartNodeAsync |Start-ServiceFabricNode |Nie dotyczy |
| StopNode |Symuluje awarii węzła przez zatrzymanie węzła w klastrze. Węzeł pozostanie w dół do momentu Węzeł_początkowy jest wywoływana. |StopNodeAsync |Stop-ServiceFabricNode |Nieprawidłowego |
| ValidateApplication |Sprawdza dostępność i kondycję wszystkich usług usługi Service Fabric w aplikacji, zwykle po wykonuje kilka błędów w systemie. |ValidateApplicationAsync |Test-ServiceFabricApplication |Nie dotyczy |
| ValidateService |Sprawdza dostępność i kondycji usługi Service Fabric, zwykle po wykonuje kilka błędów w systemie. |ValidateServiceAsync |Test-ServiceFabricService |Nie dotyczy |

## <a name="running-a-testability-action-using-powershell"></a>Uruchamianie akcji testowalności, przy użyciu programu PowerShell
Ten samouczek pokazuje, jak uruchamianie akcji testowalności przy użyciu programu PowerShell. Dowiesz się jak uruchamianie akcji testowalności względem klastra lokalnego (jednopunktowe) lub w klastrze platformy Azure. Microsoft.Fabric.Powershell.dll — moduł Service Fabric programu PowerShell — jest instalowana automatycznie podczas instalacji pliku MSI Microsoft usługi Service Fabric. Moduł jest załadowany automatycznie po otwarciu wiersz polecenia programu PowerShell.

Samouczek segmenty:

* [Uruchamianie akcji względem klastra jednopunktowe](#run-an-action-against-a-one-box-cluster)
* [Uruchom akcję względem klastrze platformy Azure](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Uruchamianie akcji względem klastra jednopunktowe
Uruchamianie akcji testowalności względem klastra lokalnego, połącz się z klastrem i otwórz wiersz polecenia programu PowerShell w trybie administratora. Przyjrzyjmy się **ServiceFabricNode ponowne uruchomienie** akcji.

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Tutaj akcji **ServiceFabricNode ponowne uruchomienie** jest uruchamiana w węźle, nazwane "Node1". Tryb uzupełniania Określa, że jej nie sprawdzić, czy akcja ponownego uruchomienia węzła faktycznie zakończyło się pomyślnie. Określanie trybu uzupełniania, jako "Weryfikuj" spowoduje, że należy sprawdzić, czy akcja ponownego faktycznie zakończyło się pomyślnie. Zamiast bezpośrednio określać węzła po jego nazwie, możesz je określić za pomocą klucza partycji i rodzaj repliki bazy danych, w następujący sposób:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Ponowne uruchomienie ServiceFabricNode** powinny być używane do ponownego uruchomienia węzła usługi Service Fabric w klastrze. Spowoduje to zatrzymanie procesu Fabric.exe, w którym zostanie uruchomiony ponownie wszystkie systemu usługi i użytkownik usługi repliki hostowane w tym węźle. Przy użyciu tego interfejsu API, aby przetestować usługę pomaga wykrycia błędów, wzdłuż ścieżek odzyskiwania trybu failover. Umożliwia symulowanie awarii węzła w klastrze.

Poniższy zrzut ekranu przedstawia **ServiceFabricNode ponowne uruchomienie** polecenia testowania w działaniu.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

Dane wyjściowe pierwszego **Get-ServiceFabricNode** (polecenia cmdlet z modułu Service Fabric programu PowerShell) pokazuje, czy klaster lokalny zawiera pięć węzłów: Node.1 do Node.5. Po wykonaniu akcji testowalności (polecenia cmdlet) **ServiceFabricNode ponowne uruchomienie** jest wykonywane w węźle, nazwane Node.4, widzimy zresetowano czas działania podrzędnego.

### <a name="run-an-action-against-an-azure-cluster"></a>Uruchom akcję względem klastrze platformy Azure
Uruchamianie akcji testowalności (przy użyciu programu PowerShell) w klastrze platformy Azure jest podobny do uruchamiania akcji względem klastra lokalnego. Jedyna różnica polega na tym, zanim będzie można uruchomić działania, zamiast łączenia się z lokalnym klastrem, należy najpierw połączyć w klastrze platformy Azure.

## <a name="running-a-testability-action-using-c35"></a>Uruchamianie akcji testowalności, przy użyciu języka C&#35;
Uruchamianie akcji testowalności C#, najpierw musisz nawiązać połączenie z klastrem przy użyciu FabricClient. Następnie Uzyskaj parametrów wymaganych do uruchomienia działania. Można użyć różnych parametrów do uruchomienia tego samego działania.
Patrząc akcji RestartServiceFabricNode, jednym ze sposobów jego działania jest, korzystając z informacji węzła (nazwa węzła i identyfikator wystąpienia węzła) w klastrze.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Objaśnienie parametrów:

* **CompleteMode** Określa, czy tryb nie należy sprawdzić, czy akcja ponownego faktycznie zakończyło się pomyślnie. Określanie trybu uzupełniania, jako "Weryfikuj" spowoduje, że należy sprawdzić, czy akcja ponownego faktycznie zakończyło się pomyślnie.  
* **OperationTimeout** ustawia ilość czasu na zakończenie przed jest zgłaszany wyjątek TimeoutException operacji.
* **Token anulowania** umożliwia wywołanie oczekujące zostaną anulowane.

Zamiast bezpośrednio określać węzła po jego nazwie, możesz je określić za pomocą klucza partycji i rodzaju repliki.

Aby uzyskać więcej informacji zobacz PartitionSelector i ReplicaSelector.

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

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector i ReplicaSelector
### <a name="partitionselector"></a>PartitionSelector
PartitionSelector jest pomocnika ujawnione podczas testowania i jest używany do wybierania określonej partycji, na którym należy wykonać jedną z akcji testowalności. Może służyć do wybierz określoną partycję, jeśli identyfikator partycji jest znana wcześniej. Lub, możesz dostarczyć klucza partycji, a operacja zostanie rozwiązany identyfikator partycji: wewnętrznie. Istnieje również możliwość wyboru losowe partycji.

Aby użyć tego pomocnika, Utwórz obiekt PartitionSelector i wybierz partycję, używając jednej z metod Select *. Następnie przekaż obiekt PartitionSelector do interfejsu API, który go wymaga. Jeśli opcja nie jest zaznaczone, domyślnie losowy partycji.

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

### <a name="replicaselector"></a>ReplicaSelector
ReplicaSelector jest pomocnika ujawnione podczas testowania i ułatwiają Wybierz replikę, na którym należy wykonać jedną z akcji testowalności. Może służyć do wybierz określonych repliki, jeśli identyfikator repliki jest znana wcześniej. Ponadto masz możliwość wyboru repliki podstawowej lub dodatkowej losowych. ReplicaSelector pochodną PartitionSelector, więc musisz wybrać replik i partycji, na którym chcesz wykonać operację testowania.

Aby użyć tego pomocnika, Utwórz obiekt ReplicaSelector i ustawić w taki sposób, aby wybrać replik i partycji. Można następnie przekazać go do interfejsu API, który go wymaga. Jeśli opcja nie jest zaznaczone, domyślnie losowy replik i partycji losowe.

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
* [Scenariuszy testowalności](service-fabric-testability-scenarios.md)
* Testowanie usługi
  * [Symulowanie błędów w trakcie obciążenia usługi](service-fabric-testability-workload-tests.md)
  * [Problemy z komunikacją Service to service](service-fabric-testability-scenarios-service-communication.md)

