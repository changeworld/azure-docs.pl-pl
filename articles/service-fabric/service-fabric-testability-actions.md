---
title: Symulowanie błędów w mikrousługach platformy Azure
description: W tym artykule o mówi o akcjach testowania znalezionych w sieci szkieletowej usług Microsoft Azure.
author: motanv
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: 4bdb00eec38addc0c9f88eba8b73185ec5721277
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282044"
---
# <a name="testability-actions"></a>Działania związane z testowaniem
Aby symulować za zawodne infrastruktury, usługa Azure Service Fabric zapewnia, deweloper, sposoby symulowania różnych rzeczywistych błędów i przejściach stanu. Są one widoczne jako akcje testowania. Akcje są interfejsami API niskiego poziomu, które powodują iniekcji określonych błędów, przejścia stanu lub sprawdzania poprawności. Łącząc te akcje, można napisać kompleksowe scenariusze testów dla usług.

Sieci szkieletowej usług zawiera kilka typowych scenariuszy testów składających się z tych akcji. Zdecydowanie zaleca się, aby korzystać z tych wbudowanych scenariuszy, które są starannie wybrane do testowania typowych przejść stanu i przypadków awarii. Jednak akcje mogą służyć do tworzenia scenariuszy testów niestandardowych, gdy chcesz dodać pokrycie dla scenariuszy, które nie są objęte wbudowanymi scenariuszami jeszcze lub które są niestandardowe dostosowane do aplikacji.

Implementacje c# akcji znajdują się w zestawie System.Fabric.dll. Moduł Sieci szkieletowej systemu PowerShell znajduje się w zestawie Microsoft.ServiceFabric.Powershell.dll. W ramach instalacji środowiska wykonawczego moduł ServiceFabric PowerShell jest zainstalowany w celu ułatwienia użytkowania.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Wdzięczne a niewdzięczne akcje błędów
Działania testowalności są podzielone na dwa główne rzesze:

* Błędy bezgrawowe: te błędy symulują awarie, takie jak ponowne uruchomienie komputera i awarie procesu. W takich przypadkach awarii kontekst wykonywania procesu zatrzymuje się nagle. Oznacza to, że żadne oczyszczanie stanu można uruchomić przed ponownym uruchomieniem aplikacji.
* Błędy wdzięku: Te błędy symulują wdzięczne akcje, takie jak ruchy repliki i upadki wyzwalane przez równoważenie obciążenia. W takich przypadkach usługa otrzymuje powiadomienie o zamknięciu i może oczyścić stan przed zamknięciem.

Aby uzyskać lepszą jakość sprawdzania poprawności, uruchom obciążenie usług i przedsiębiorstw, jednocześnie wywołując różne wdzięczne i niewdzięczne błędy. Ungraceful błędów wykonywania scenariuszy, w których proces usługi nagle kończy pracę w środku niektórych przepływu pracy. Spowoduje to testy ścieżki odzyskiwania po przywróceniu repliki usługi przez sieci szkieletowej usług. Pomoże to przetestować spójność danych i czy stan usługi jest obsługiwany poprawnie po awarii. Inny zestaw błędów (błędy pełne wdzięku) test, że usługa poprawnie reaguje na repliki są przenoszone przez sieci szkieletowej usług. To testuje obsługę anulowania w RunAsync metody. Usługa musi sprawdzić, czy token anulowania jest ustawiony, poprawnie zapisać jego stan i zakończyć RunAsync metody.

## <a name="testability-actions-list"></a>Lista akcji testowania
| Akcja | Opis | Zarządzany interfejs API | Polecenie cmdlet programu PowerShell | Wdzięku / niewdzięczne błędy |
| --- | --- | --- | --- | --- |
| CleanTestState (CleanTestState) |Usuwa cały stan testu z klastra w przypadku nieprawidłowego zamknięcia sterownika testowego. |CleanTestStateAsync |Remove-ServiceFabricTestState |Nie dotyczy |
| Przywoływanych danych |Powoduje utratę danych do partycji usługi. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Wdzięku |
| PrzywołująQuorumLoss |Umieszcza danej partycji usługi stanowej do utraty kworum. |InvokeQuorumLossAsync |Invoke-ServiceFabricQuorumLoss |Wdzięku |
| MovePrimary (Niem. |Przenosi określoną replikę podstawową usługi stanowej do określonego węzła klastra. |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |Wdzięku |
| MoveSecondary ( MoveSecondary ) |Przenosi bieżącą replikę pomocniczą usługi stanowej do innego węzła klastra. |MoveSecondaryAsync ( MoveSecondaryAsync ) |Move-ServiceFabricSecondaryReplica |Wdzięku |
| UsuńReplica |Symuluje błąd repliki, usuwając replikę z klastra. Spowoduje to zamknięcie repliki i przejście jej do roli "Brak", usuwając cały jej stan z klastra. |UsuńReplicaAsync |Usuń-ServiceFabricReplica |Wdzięku |
| Pakiet Kodu Restartu |Symuluje błąd procesu pakietu kodu, uruchamiając ponownie pakiet kodu wdrożony w węźle w klastrze. Spowoduje to przerwanie procesu pakietu kodu, który spowoduje ponowne uruchomienie wszystkich replik usługi użytkownika hostowanych w tym procesie. |RestartDeployedCodePackageSync |Ponowne uruchomienie usługiFabricDeployedCodePackage |Bezbożny |
| Ponowne uruchamianie |Symuluje błąd węzła klastra sieci szkieletowej usług, uruchamiając ponownie węzeł. |RestartNodeAsync |Ponowne uruchomienie usługiFabricNode |Bezbożny |
| Uruchom ponowniepartition |Symuluje zaciemnienie centrum danych lub scenariusz zaciemnienia klastra przez ponowne uruchomienie niektórych lub wszystkich replik partycji. |Ponowne uruchamianieasync |Restart-ServiceFabricPartition |Wdzięku |
| Ponowne uruchomienie |Symuluje błąd repliki, uruchamiając ponownie utrwalone repliki w klastrze, zamykając replikę, a następnie ponownie otwierając ją. |Ponowne odnawianie |Ponowne uruchomienie usługiFabricReplica |Wdzięku |
| StartNode (Początek) |Uruchamia węzeł w klastrze, który jest już zatrzymany. |StartNodeAsync |Start-ServiceFabricNode |Nie dotyczy |
| StopNode (Przystanek) |Symuluje błąd węzła, zatrzymując węzeł w klastrze. Węzeł pozostanie w dół, dopóki nie zostanie wywołana startnode. |StopNodeAsync |Stop-ServiceFabricNode |Bezbożny |
| Sprawdzanie poprawności aplikacji |Sprawdza poprawność dostępności i kondycji wszystkich usług sieci szkieletowej usług w aplikacji, zwykle po wysunieniu niektórych błędów do systemu. |Sprawdzanie poprawności aplikacjiSync |Test-ServiceFabricAplikacja |Nie dotyczy |
| Sprawdzanie poprawności usługi |Sprawdza poprawność dostępności i kondycji usługi sieci szkieletowej usług, zwykle po wysunieniu niektórych błędów do systemu. |ValidateServiceAsync |Test-ServiceFabricService |Nie dotyczy |

## <a name="running-a-testability-action-using-powershell"></a>Uruchamianie akcji testowalności przy użyciu programu PowerShell
W tym samouczku pokazano, jak uruchomić akcję testowania przy użyciu programu PowerShell. Dowiesz się, jak uruchomić akcję testowania dla lokalnego klastra (jedno pole) lub klastra platformy Azure. Microsoft.Fabric.Powershell.dll - moduł PowerShell sieci szkieletowej usług — jest instalowany automatycznie podczas instalowania msi sieci szkieletowej usług firmy Microsoft. Moduł jest ładowany automatycznie po otwarciu monitu programu PowerShell.

Segmenty samouczka:

* [Uruchamianie akcji względem klastra jednokołowego](#run-an-action-against-a-one-box-cluster)
* [Uruchamianie akcji względem klastra platformy Azure](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Uruchamianie akcji względem klastra jednokołowego
Aby uruchomić akcję testowania klastra lokalnego, najpierw połącz się z klastrem i otwórz monit programu PowerShell w trybie administratora. Przyjrzyjmy się akcji **Restart-ServiceFabricNode.**

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

W tym miejscu akcja **Uruchom ponownie-ServiceFabricNode** jest uruchamiany w węźle o nazwie "Węzeł1". Tryb uzupełniania określa, że nie należy sprawdzać, czy akcja ponownego uruchamiania węzła faktycznie powiodła się. Określenie trybu ukończenia jako "Sprawdź" spowoduje, że weryfikuje, czy akcja ponownego uruchomienia faktycznie powiodła się. Zamiast bezpośrednio określać węzeł według jego nazwy, można określić go za pomocą klucza partycji i rodzaju repliki, w następujący sposób:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Restart ServiceFabricNode** powinny być używane do ponownego uruchomienia węzła sieci szkieletowej usług w klastrze. Spowoduje to zatrzymanie procesu Fabric.exe, który uruchomi ponownie wszystkie repliki usługi systemowej i usługi użytkownika hostowane w tym węźle. Za pomocą tego interfejsu API do testowania usługi pomaga odkryć błędy wzdłuż ścieżek odzyskiwania trybu failover. Pomaga symulować błędy węzłów w klastrze.

Poniższy zrzut ekranu przedstawia polecenie **Uruchom ponownie ServiceFabricNode** testability w akcji.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

Dane wyjściowe pierwszego **pliku Get-ServiceFabricNode** (polecenia cmdlet z modułu PowerShell sieci szkieletowej usług) pokazują, że klaster lokalny ma pięć węzłów: Node.1 do Node.5. Po akcji testowalności (polecenie cmdlet) **Restart-ServiceFabricNode** jest wykonywany w węźle o nazwie Node.4, widzimy, że węzeł czas pracy został zresetowany.

### <a name="run-an-action-against-an-azure-cluster"></a>Uruchamianie akcji względem klastra platformy Azure
Uruchamianie akcji testowalności (przy użyciu programu PowerShell) względem klastra platformy Azure jest podobne do uruchamiania akcji względem klastra lokalnego. Jedyną różnicą jest to, że przed uruchomieniem akcji, zamiast łączenia się z klastrem lokalnym, należy najpierw połączyć się z klastrem platformy Azure.

## <a name="running-a-testability-action-using-c35"></a>Uruchamianie akcji testowalności przy użyciu&#35; C
Aby uruchomić akcję testowalności przy użyciu języka C#, najpierw należy połączyć się z klastrem przy użyciu FabricClient. Następnie uzyskać parametry potrzebne do uruchomienia akcji. Różne parametry mogą być używane do uruchamiania tej samej akcji.
Patrząc na akcję RestartServiceFabricNode, jednym ze sposobów jej uruchomienia jest użycie informacji o węźle (nazwa węzła i identyfikator wystąpienia węzła) w klastrze.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Wyjaśnienie parametrów:

* **CompleteMode** określa, że tryb nie powinien sprawdzać, czy akcja ponownego uruchomienia faktycznie powiodła się. Określenie trybu ukończenia jako "Sprawdź" spowoduje, że weryfikuje, czy akcja ponownego uruchomienia faktycznie powiodła się.  
* **OperationTimeout** ustawia czas zakończenia operacji przed zadekuszczeniem timeoutexception.
* **CancellationToken** umożliwia anulowanie oczekującego połączenia.

Zamiast bezpośrednio określać węzeł według jego nazwy, można określić go za pomocą klucza partycji i rodzaju repliki.

Aby uzyskać więcej informacji, zobacz PartitionSelector i ReplicaSelector.

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
PartitionSelector jest pomocnikiem narażone w testowalność i jest używany do wyboru określonej partycji, na której można wykonać dowolną z akcji testowalności. Może służyć do wybierania określonej partycji, jeśli identyfikator partycji jest znany wcześniej. Lub można podać klucz partycji, a operacja rozwiąże identyfikator partycji wewnętrznie. Istnieje również możliwość wybrania losowej partycji.

Aby użyć tego pomocnika, utwórz partitionSelector obiektu i wybierz partycję przy użyciu jednej z Wybierz * metody. Następnie przekazać w PartitionSelector obiektu do interfejsu API, który tego wymaga. Jeśli nie jest zaznaczona żadna opcja, domyślnie jest to losowa partycja.

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
ReplicaSelector jest pomocnikiem narażone w testowalności i służy do wybierania repliki, na którym można wykonać dowolną z akcji testowalności. Może służyć do wybierania określonej repliki, jeśli identyfikator repliki jest znany wcześniej. Ponadto można wybrać replikę podstawową lub losową pomocniczą. ReplicaSelector pochodzi z PartitionSelector, więc należy wybrać zarówno replikę, jak i partycję, na której chcesz wykonać operację testowalności.

Aby użyć tego pomocnika, utwórz obiekt ReplicaSelector i ustaw sposób wybierania repliki i partycji. Następnie można przekazać go do interfejsu API, który tego wymaga. Jeśli nie jest zaznaczona żadna opcja, domyślnie jest to losowa replika i losowa partycja.

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

## <a name="next-steps"></a>Następne kroki
* [Scenariusze testowania](service-fabric-testability-scenarios.md)
* Jak przetestować usługę
  * [Symulowanie awarii podczas obciążeń serwisowych](service-fabric-testability-workload-tests.md)
  * [Awarie komunikacji między usługami](service-fabric-testability-scenarios-service-communication.md)

