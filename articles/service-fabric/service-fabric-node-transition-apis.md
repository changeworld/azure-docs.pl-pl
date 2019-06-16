---
title: Uruchamianie i zatrzymywanie węzłów klastra, aby testować aplikacje usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i do testowania aplikacji usługi Service Fabric przez uruchamianie i zatrzymywanie węzłów klastra, użyj iniekcji błędów.
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
ms.openlocfilehash: df0e53736c08fd2c26c467def7328e85f2989f26
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60718142"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>Zastępowanie uruchomić węzeł i węzeł Stop interfejsów API za pomocą interfejsu API przechodzenia węzłów

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>Co Start API węzła i zatrzymanie węzła zrobić?

Interfejs API zatrzymać węzła (zarządzane: [StopNodeAsync()][stopnode], PowerShell: [Stop-ServiceFabricNode][stopnodeps]) zatrzymuje węzła usługi Service Fabric.  Węzeł usługi Service Fabric to proces, nie maszyny Wirtualnej lub komputera — maszyna wirtualna lub maszyna będzie w dalszym ciągu działać.  W pozostałej części dokumentu "węzeł" będzie oznaczać węzła usługi Service Fabric.  Zatrzymanie węzła umieszcza go w *zatrzymana* stanu, w którym nie jest elementem członkowskim klastra i nie może obsługiwać usługi, w związku z tym symulowania *dół* węzła.  Jest to przydatne wstrzykiwania błędów do systemu, aby przetestować aplikację.  Interfejs API Node Start (zarządzane: [StartNodeAsync()][startnode], PowerShell: [Start-ServiceFabricNode][startnodeps]]) odwraca zatrzymanie węzła interfejsu API, który przełącza węzeł z powrotem do normalnego stanu.

## <a name="why-are-we-replacing-these"></a>Dlaczego firma Microsoft je zastąpić?

Jak opisano wcześniej, *zatrzymana* węzła usługi Service Fabric jest węzłem celowo docelowe przy użyciu interfejsu API zatrzymać węzła.  A *dół* węzeł jest węzłem, który nie działa z innego powodu (na przykład maszyna wirtualna lub maszyna jest wyłączone).  Za pomocą interfejsu API zatrzymać węzła, system nie ujawnia informacji do rozróżnienia między *zatrzymana* węzłów i *dół* węzłów.

Ponadto niektóre błędy zwrócone przez te interfejsy API nie są jak opisowy stać się.  Na przykład wywołanie interfejsu API zatrzymać węzła na wiadomość już *zatrzymana* węzła zwróci błąd *InvalidAddress*.  To środowisko można lepiej.

Ponadto czas, przez jaki jest zatrzymanie węzła jest "nieskończonej", dopóki nie Start Node API jest wywoływana.  Odkryliśmy, to może być przyczyną problemów i mogą być podatne na błędy.  Na przykład widzieliśmy problemów, gdy użytkownik wywoływane interfejsu API zatrzymać węzeł w węźle, a następnie nie pamiętam o nim.  Później, było wiadomo, jeśli węzeł został *dół* lub *zatrzymana*.


## <a name="introducing-the-node-transition-apis"></a>Wprowadzenie do interfejsów API przechodzenia węzłów

Uwzględniono tych problemów powyżej utworzy nowy zestaw interfejsów API.  Nowy interfejs API przechodzenia węzłów (zarządzane: [StartNodeTransitionAsync()][snt]) może służyć do przejścia do węzła usługi Service Fabric *zatrzymana* stanu lub przejścia z *zatrzymana* do stanu normalnego stanu.  Należy pamiętać, że "Start" nazwę interfejsu API nie odwołuje się węzeł początkowy.  Odwołuje się on od operację asynchroniczną, która systemu będą wykonywane na przeniesienie węzła do jednej *zatrzymana* lub stanu.

**Użycie**

Jeśli interfejs API Przejście węzła zgłasza wyjątek przy wywołaniu, następnie systemu zaakceptował operacji asynchronicznej i uruchomić go.  Pomyślne wywołanie nie oznacza, że operacja jest jeszcze zakończona.  Aby uzyskać informacje dotyczące bieżącego stanu operacji, wywoływania interfejsu API programu Node przejścia postępu (zarządzane: [GetNodeTransitionProgressAsync()][gntp]) o identyfikatorze guid używany podczas wywoływania interfejsu API przechodzenia węzłów do wykonania tej operacji.  Węzeł postępu przejście z interfejsu API zwraca obiekt NodeTransitionProgress.  Właściwość stanu tego obiektu określa stan bieżącej operacji.  Jeśli stan ma wartość "działa", operacja jest wykonywana.  Jeśli jest ukończona, operacja zostanie zakończona bez błędów.  Jeśli jest wystąpił błąd, wystąpił problem podczas wykonywania operacji.  Właściwość wyjątku właściwości wyniku będzie wskazywać problem został.  Zobacz https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate Aby uzyskać więcej informacji na temat właściwości stanu i w sekcji "Przykładowe zastosowanie" poniżej przykłady kodu.


**Rozróżnianie między węzłem zatrzymania i języka node dół** Jeśli węzeł jest *zatrzymana* przy użyciu interfejsu API Przejście węzła, wyników kwerendy węzła (zarządzane: [GetNodeListAsync()][nodequery], programu PowerShell: [Get-ServiceFabricNode][nodequeryps]) będą wyświetlane, że ten węzeł zawiera *IsStopped* właściwości wartość true.  Należy pamiętać, to różni się od wartości *NodeStatus* właściwość, która będzie wyświetlany tekst *dół*.  Jeśli *NodeStatus* właściwość ma wartość *dół*, ale *IsStopped* ma wartość FAŁSZ, a następnie węzeł nie został zatrzymany, przy użyciu interfejsu API przechodzenia węzłów i jest *w dół*  z powodu innej przyczyny.  Jeśli *IsStopped* właściwość ma wartość true i *NodeStatus* właściwość jest *dół*, a następnie została zatrzymana, przy użyciu interfejsu API przechodzenia węzłów.

Uruchamianie *zatrzymana* węzła przy użyciu interfejsu API przechodzenia węzłów zwróci ona działa jak normalne członka tego klastra, ponownie.  Wyświetli wyniki kwerendy węzła API *IsStopped* jako wartość false, a *NodeStatus* jako coś, co nie jest w dół (na przykład w górę).


**Czas trwania** zatrzymanie węzła, jeden z wymaganych parametrów za pomocą interfejsu API przechodzenia węzłów *stopNodeDurationInSeconds*, reprezentuje czas w sekundach, aby zachować węzeł *zatrzymana*.  Ta wartość musi być z dopuszczalnego zakresu, który ma co najmniej 600 i maksymalnie 14400.  Po upływie tego czasu, węzeł zostanie automatycznie uruchomiony ponownie się w stanie Up.  Zapoznaj się z 1 przykład poniżej przedstawiono przykład użycia.

> [!WARNING]
> Unikaj łączenia operacji węzła przejścia interfejsów API i zatrzymanie węzła i uruchomić węzeł.  Zaleca się używać tylko API Przejście węzła.  > Jeśli węzeł został już została zatrzymana, korzystanie z interfejsu API zatrzymać węzła, jej powinna być uruchamiana przy użyciu Start węzła interfejsu API przed skorzystaniem z > interfejsów API przechodzenia węzłów.

> [!WARNING]
> Nie można wprowadzać wiele wywołań interfejsów API przechodzenia węzłów na tym samym węźle równolegle.  W takiej sytuacji będzie interfejsu API przechodzenia węzłów > throw FabricException o wartości właściwości ErrorCode NodeTransitionInProgress.  Przejście węzła w określonym węźle ma > została uruchomiona, powinien poczekaj, aż operacja osiągnie stan końcowy (ukończone, Faulted lub ForceCancelled) przed rozpoczęciem > nowe przejście na tym samym węźle.  Równoległe węzła wywołania przejścia na różnych węzłach są dozwolone.


#### <a name="sample-usage"></a>Przykładowe zastosowanie


**Przykład 1** — w poniższym przykładzie użyto interfejsu API przechodzenia węzłów zatrzymanie węzła.

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

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Przykład 2** — następujące przykładowe rozpoczyna się *zatrzymana* węzła.  Używa metody pomocnika, niektóre z pierwszego przykładu.

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

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Przykład 3** -poniższy przykład pokazuje nieprawidłowe użycie.  To obciążenie jest nieprawidłowy ponieważ *stopDurationInSeconds* zapewnia, jest większy niż dozwolony zakres.  Ponieważ StartNodeTransitionAsync() zakończy się niepowodzeniem z powodu błędu krytycznego, operacja nie została zaakceptowana, i nie należy wywoływać interfejs API postępu.  W przykładzie użyto metody pomocnika, niektóre z pierwszego przykładu.

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

**Przykład 4** -poniższy przykład pokazuje informacje o błędzie, który zostanie zwrócony z interfejsu API węzła przejścia postępu podczas operacji zainicjowanych przez interfejs API Przejście węzła jest akceptowana, ale nie powiedzie się później podczas wykonywania.  W przypadku niepowodzenia ponieważ API Przejście węzła podejmie próbę uruchomienia węzła, który nie istnieje.  W przykładzie użyto metody pomocnika, niektóre z pierwszego przykładu.

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

[stopnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN
[stopnodeps]: https://msdn.microsoft.com/library/mt125982.aspx
[startnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN
[startnodeps]: https://msdn.microsoft.com/library/mt163520.aspx
[nodequery]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient
[nodequeryps]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode
[snt]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient
[gntp]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient
