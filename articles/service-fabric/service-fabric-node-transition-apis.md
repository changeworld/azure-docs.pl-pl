---
title: Uruchamianie i zatrzymywanie węzłów klastra, aby przetestować Azure mikrousług | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać iniekcji błędów do testowania aplikacji usługi Service Fabric przez uruchamianie i zatrzymywanie węzłów klastra.
services: service-fabric
documentationcenter: .net
author: LMWF
manager: rsinha
editor: ''
ms.assetid: f4e70f6f-cad9-4a3e-9655-009b4db09c6d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/12/2017
ms.author: lemai
ms.openlocfilehash: 0ed18097fa18101c237b4408d26dd1bc9c5d5648
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212582"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>Zastępowanie uruchomić węzeł i zatrzymanie węzła interfejsów API przy użyciu interfejsu API Przejście węzła

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>Co węzeł zatrzymać i uruchomić interfejsów API węzła zrobić?

API zatrzymanie węzła (zarządzany: [StopNodeAsync()][stopnode], programu PowerShell: [Stop-ServiceFabricNode][stopnodeps]) zatrzymuje węzła sieci szkieletowej usług.  Węzeł sieci szkieletowej usług procesu, nie jest wirtualna lub maszyna — maszyny Wirtualnej lub maszyny będą nadal działać.  W pozostałej części dokumentu "węzła" oznacza węzła sieci szkieletowej usług.  Zatrzymanie węzła umieszcza je w *zatrzymana* stanu, gdy nie jest członkiem klastra i nie może obsługiwać usługi, w związku z tym symulując *dół* węzła.  Jest to przydatne wstrzykiwania błędów do systemu, aby przetestować aplikację.  API węzła Start (zarządzany: [StartNodeAsync()][startnode], programu PowerShell: [Start ServiceFabricNode][startnodeps]]) odwraca API węźle Zatrzymaj  Powrót do normalnego stanu, który przełącza węzeł.

## <a name="why-are-we-replacing-these"></a>Dlaczego możemy je zastąpić?

Jak opisano wcześniej, *zatrzymana* sieci szkieletowej usług węzeł jest węzłem, który został celowo docelowych przy użyciu interfejsu API węźle Zatrzymaj.  A *dół* węzeł jest węzłem, który nie działa z jakiegokolwiek powodu (np. maszyna wirtualna lub maszyna jest wyłączone).  Przy użyciu zatrzymanie węzła interfejsu API, system nie ujawnia informacji w celu rozróżnienia *zatrzymana* węzłów i *dół* węzłów.

Ponadto niektóre błędy zwrócone przez te interfejsy API nie są jako opisową stać się.  Na przykład wywoływanie interfejsu API węźle Zatrzymaj na moduł już *zatrzymana* węzła zwróci błąd *InvalidAddress*.  To środowisko można lepiej.

Czas trwania, który jest zatrzymanie węzła jest również "nieskończone", dopóki wywołaniu interfejsu API węzła Start.  Znaleźliśmy to może spowodować problemy i mogą być podatne na błędy.  Na przykład firma Microsoft w tym samouczku problemów, gdzie użytkownik wywołać interfejsu API węźle Zatrzymaj w węźle, a następnie zapomniano o.  Później, jest jasne, czy węzeł został *dół* lub *zatrzymana*.


## <a name="introducing-the-node-transition-apis"></a>Wprowadzenie do interfejsów API Przejście węzła

Uwzględniono te problemy powyżej w nowy zestaw interfejsów API.  Nowy interfejs API Przejście węzła (zarządzany: [StartNodeTransitionAsync()][snt]) może służyć do przejścia do węzła sieci szkieletowej usług *zatrzymana* stanu, lub do przejścia z *zatrzymana* stanu do normalnego stanu.  Należy pamiętać, że "Start" nazwy interfejsu API nie odwołuje się węzeł początkowy.  Odnosi się do rozpoczyna operację asynchroniczną, która systemu wykona do przejścia do każdego węzła *zatrzymana* lub został rozpoczęty stanu.

**Użycie**

Jeśli interfejs API Przejście węzła zgłosiła wyjątek przy wywołaniu, następnie system zaakceptowane przez operację asynchroniczną i wykonaj go.  Pomyślne wywołanie nie oznacza, że operacja jest jeszcze zakończona.  Aby uzyskać informacje dotyczące bieżącego stanu operacji, wywołania interfejsu API postępu Przejście węzła (zarządzany: [GetNodeTransitionProgressAsync()][gntp]) o identyfikatorze guid używane po wywołaniu interfejsu API Przejście węzła dla tej operacji.  Interfejs API postępu Przejście węzła zwraca obiekt NodeTransitionProgress.  Właściwość stanu tego obiektu określa bieżący stan operacji.  Jeśli stan "działa" jest wykonywanie operacji.  Jeśli jest zakończone, operacja zakończona bez błędów.  Jeśli jest ona uszkodzona, wystąpił problem podczas wykonywania operacji.  Właściwość wyjątku Właściwość Result będą wskazywać problem został.  Zobacz https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate Aby uzyskać więcej informacji na temat właściwości stanu i w sekcji "Przykładowe zastosowanie" poniżej przykłady kodu.


**Rozróżnianie między węzłem zatrzymana i dół węzła** Jeśli węzeł ma *zatrzymana* przy użyciu interfejsu API Przejście węzła, wyników kwerendy węzła (zarządzany: [GetNodeListAsync()] [ nodequery], Programu PowerShell: [Get-ServiceFabricNode][nodequeryps]) wyświetli ten węzeł zawiera *IsStopped* właściwości wartość true.  Należy pamiętać, jest inna niż wartość *NodeStatus* właściwość, która będzie napisane *dół*.  Jeśli *NodeStatus* właściwość ma wartość *dół*, ale *IsStopped* wynosi false, a następnie węzeł nie został zatrzymany, przy użyciu interfejsu API Przejście węzła i jest *w dół*  powodu innego powodu.  Jeśli *IsStopped* właściwość ma wartość true i *NodeStatus* właściwość jest *dół*, a następnie została zatrzymana, przy użyciu interfejsu API Przejście węzła.

Uruchamianie *zatrzymana* węzła przy użyciu interfejsu API Przejście węzła zwraca go do działania jako normalne członkiem klastra ponownie.  Zostaną wyświetlone dane wyjściowe węzeł zapytania interfejsu API *IsStopped* jako FAŁSZ i *NodeStatus* jako element, który nie jest w dół (np. w górę).


**Ograniczony czas trwania** w razie zatrzymanie węzła, jeden z wymaganych parametrów za pomocą interfejsu API Przejście węzła *stopNodeDurationInSeconds*, reprezentuje czas w sekundach, aby zachować węzeł *zatrzymana*.  Ta wartość musi być z dopuszczalnego zakresu, który ma co najmniej 600 i maksymalnie 14400.  Po upływie tego czasu, węzeł zostanie automatycznie uruchomiony ponownie się do stanu.  Zapoznaj się z 1 próbki poniżej przedstawiono przykład użycia.

> [!WARNING]
> Unikaj mieszanie interfejsów API Przejście węzła i zatrzymanie węzła i uruchomienia interfejsów API węzła.  To zalecanie służy do użycia tylko API Przejście węzła.  > Jeśli już zostało węzła zatrzymana, przy użyciu interfejsu API węźle Zatrzymaj, jego powinny być uruchamiane przy użyciu węzła Start API przed skorzystaniem z > interfejsów API Przejście węzła.

> [!WARNING]
> Nie można utworzyć wielu wywołań interfejsów API Przejście węzła w tym samym węźle równolegle.  W takiej sytuacji zostanie API Przejście węzła > throw FabricException o wartości właściwości ErrorCode NodeTransitionInProgress.  Przejście węzła w określonym węźle ma > została uruchomiona, należy poczekać aż operacja osiągnie stan terminali (ukończone, Faulted lub ForceCancelled) przed rozpoczęciem > nowe przejście w tym samym węźle.  Równoległe węzła wywołania przejścia w różnych węzłach są dozwolone.


#### <a name="sample-usage"></a>Przykładowe zastosowanie


**Przykład 1** — w poniższym przykładzie użyto API Przejście węzła zatrzymanie węzła.

```csharp
        // Helper function to get information about a node
        static Node GetNodeInfo(FabricClient fc, string node)
        {
            NodeList n = null;
            while (n == null)
            {
                n = fc.QueryManager.GetNodeListAsync(node).GetAwaiter().GetResult();
                Task.Delay(TimeSpan.FromSeconds(1)).GetAwaiter();
            };

            return n.FirstOrDefault();
        }

        static async Task WaitForStateAsync(FabricClient fc, Guid operationId, TestCommandProgressState targetState)
        {
            NodeTransitionProgress progress = null;

            do
            {
                bool exceptionObserved = false;
                try
                {
                    progress = await fc.TestManager.GetNodeTransitionProgressAsync(operationId, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                    exceptionObserved = true;
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                    exceptionObserved = true;
                }

                if (!exceptionObserved)
                {
                    Console.WriteLine("Current state of operation '{0}': {1}", operationId, progress.State);

                    if (progress.State == TestCommandProgressState.Faulted)
                    {
                        // Inspect the progress object's Result.Exception.HResult to get the error code.
                        Console.WriteLine("'{0}' failed with: {1}, HResult: {2}", operationId, progress.Result.Exception, progress.Result.Exception.HResult);

                        // ...additional logic as required
                    }

                    if (progress.State == targetState)
                    {
                        Console.WriteLine("Target state '{0}' has been reached", targetState);
                        break;
                    }
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (true);
        }

        static async Task StopNodeAsync(FabricClient fc, string nodeName, int durationInSeconds)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            // Create a Guid
            Guid guid = Guid.NewGuid();

            // Create a NodeStopDescription object, which will be used as a parameter into StartNodeTransition
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, durationInSeconds);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStopDescription from above, which will stop the target node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    // This is retryable
                }
                catch (FabricTransientException fte)
                {
                    // This is retryable
                }

                // Backoff
                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until hte desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Przykład 2** -następujące przykładowe uruchamia *zatrzymana* węzła.  Używa metody pomocnicze, niektóre z pierwszego przykładu.

```csharp
        static async Task StartNodeAsync(FabricClient fc, string nodeName)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = n.NodeInstanceId;

            // Create a NodeStartDescription object, which will be used as a parameter into StartNodeTransition
            NodeStartDescription description = new NodeStartDescription(guid, n.NodeName, nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until hte desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Przykład 3** -poniższy przykład zawiera niepoprawne użycie.  To użycie jest nieprawidłowy ponieważ *stopDurationInSeconds* zapewnia przekracza dozwolony zakres.  Ponieważ StartNodeTransitionAsync() zakończy się niepowodzeniem z powodu błędu krytycznego, operacja nie została zaakceptowana, i nie powinna być wywoływana z interfejsu API w toku.  W przykładzie użyto metody pomocnicze, niektóre z pierwszego przykładu.

```csharp
        static async Task StopNodeWithOutOfRangeDurationAsync(FabricClient fc, string nodeName)
        {
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();

            // Use an out of range value for stopDurationInSeconds to demonstrate error
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, 99999);

            try
            {
                await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
            }

            catch (FabricException e)
            {
                Console.WriteLine("Caught {0}", e);
                Console.WriteLine("ErrorCode {0}", e.ErrorCode);
                // Output:
                // Caught System.Fabric.FabricException: System.Runtime.InteropServices.COMException (-2147017629)
                // StopDurationInSeconds is out of range ---> System.Runtime.InteropServices.COMException: Exception from HRESULT: 0x80071C63
                // << Parts of exception omitted>>
                //
                // ErrorCode InvalidDuration
            }
        }
```

**Przykład 4** -poniższy przykład przedstawia informacje o błędzie, który zostanie zwrócony z interfejsu API postępu Przejście węzła podczas operacji inicjowane przez interfejs API przejścia węzeł zostanie przyjęte, ale nie powiedzie się później podczas wykonywania.  W przypadku niepowodzenia ponieważ API Przejście węzła podejmie próbę uruchomienia węzła, który nie istnieje.  W przykładzie użyto metody pomocnicze, niektóre z pierwszego przykładu.

```csharp
        static async Task StartNodeWithNonexistentNodeAsync(FabricClient fc)
        {
            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = 12345;

            // Intentionally use a nonexistent node
            NodeStartDescription description = new NodeStartDescription(guid, "NonexistentNode", nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.  In this case, it will end up in the Faulted state since the node does not exist.
            // When StartNodeTransitionProgressAsync()'s returned progress object has a State if Faulted, inspect the progress object's Result.Exception.HResult to get the error code.
            // In this case, it will be NodeNotFound.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Faulted).ConfigureAwait(false);
        }
```

[stopnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN#System_Fabric_FabricClient_FaultManagementClient_StopNodeAsync_System_String_System_Numerics_BigInteger_System_Fabric_CompletionMode_
[stopnodeps]: https://msdn.microsoft.com/library/mt125982.aspx
[startnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN#System_Fabric_FabricClient_FaultManagementClient_StartNodeAsync_System_String_System_Numerics_BigInteger_System_String_System_Int32_System_Fabric_CompletionMode_System_Threading_CancellationToken_
[startnodeps]: https://msdn.microsoft.com/library/mt163520.aspx
[nodequery]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient#System_Fabric_FabricClient_QueryClient_GetNodeListAsync_System_String_
[nodequeryps]: https://docs.microsoft.com/powershell/servicefabric/vlatest/Get-ServiceFabricNode?redirectedfrom=msdn
[snt]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_StartNodeTransitionAsync_System_Fabric_Description_NodeTransitionDescription_System_TimeSpan_System_Threading_CancellationToken_
[gntp]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_GetNodeTransitionProgressAsync_System_Guid_System_TimeSpan_System_Threading_CancellationToken_
