---
title: Uruchamianie i zatrzymywania węzłów klastra
description: Dowiedz się, jak używać iniekcji błędów do testowania aplikacji sieci szkieletowej usług przez uruchamianie i zatrzymywanie węzłów klastra.
author: LMWF
ms.topic: conceptual
ms.date: 6/12/2017
ms.author: lemai
ms.openlocfilehash: 8f2eefec94ad4763a054ee089b17232c41e642dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609795"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>Zastępowanie interfejsów API węzła start i zatrzymania interfejsami API przejścia węzła

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>Do czego działają interfejsy API węzła zatrzymania i węzła startowego?

Interfejs API węzła zatrzymania (zarządzany: [StopNodeAsync()][stopnode], PowerShell: [Stop-ServiceFabricNode][stopnodeps]) zatrzymuje węzeł sieci szkieletowej usług.  Węzeł sieci szkieletowej usług jest procesem, a nie maszyną wirtualną lub maszyną — maszyna wirtualna lub maszyna będzie nadal uruchomiona.  Dla pozostałej części dokumentu "węzeł" będzie oznaczać węzeł sieci szkieletowej usług.  Zatrzymywanie węzła umieszcza go w stanie *zatrzymania,* gdzie nie jest członkiem klastra i nie może obsługiwać usług, symulując w ten sposób *węzeł w dół.*  Jest to przydatne do wstrzykiwania błędów do systemu, aby przetestować aplikację.  Interfejs API węzła startowego (zarządzany: [StartNodeAsync()][startnode], PowerShell: [Start-ServiceFabricNode][startnodeps]]) odwraca interfejs API węzła zatrzymania, który przywraca węzeł do stanu normalnego.

## <a name="why-are-we-replacing-these"></a>Dlaczego je zastępujemy?

Jak opisano wcześniej, *zatrzymany* węzeł sieci szkieletowej usług jest węzłem celowo kierowane przy użyciu interfejsu API węzła zatrzymania.  Węzeł *w dół* jest węzłem, który jest wyłączony z dowolnego innego powodu (na przykład maszyna wirtualna lub maszyna jest wyłączona).  W interfejsie API zatrzymania węzła system nie udostępnia informacji w celu rozróżnienia między *węzłami zatrzymanymi* i *węzłami w dół.*

Ponadto niektóre błędy zwracane przez te interfejsy API nie są tak opisowe, jak mogłyby być.  Na przykład wywołanie interfejsu API zatrzymania węzła w już *zatrzymanym* węźle zwróci błąd *InvalidAddress*.  To doświadczenie można poprawić.

Ponadto czas trwania węzła jest zatrzymany dla jest "nieskończony", dopóki nie zostanie wywołany interfejs API węzła startowego.  Odkryliśmy, że może to powodować problemy i może być podatne na błędy.  Na przykład widzieliśmy problemy, w których użytkownik wywołał interfejs API zatrzymania węzła w węźle, a następnie zapomniał o tym.  Później nie było jasne, czy węzeł został *zamknięty* lub *zatrzymany*.


## <a name="introducing-the-node-transition-apis"></a>Przedstawiamy interfejsy API przejścia węzła

Rozwiązaliśmy te problemy powyżej w nowym zestawie interfejsów API.  Nowy interfejs API przejścia węzła (zarządzany: [StartNodeTransitionAsync()][snt]) może służyć do przejścia węzła sieci szkieletowej usług do stanu *zatrzymanego* lub przejścia go ze stanu *zatrzymanego* do normalnego stanu.  Należy pamiętać, że "Start" w nazwie interfejsu API nie odnosi się do uruchamiania węzła.  Odnosi się do rozpoczęcia operacji asynchroniiowej, że system będzie wykonywany do przejścia węzła do stanu *zatrzymanego* lub uruchomionego.

**Użycia**

Jeśli interfejs API przejścia węzła nie zgłasza wyjątek podczas wywoływania, system zaakceptował operację asynchronizacyjną i wykona ją.  Pomyślne wywołanie nie oznacza, że operacja została jeszcze zakończona.  Aby uzyskać informacje o bieżącym stanie operacji, należy wywołać interfejs API postępu przejścia węzła (zarządzany: [GetNodeTransitionProgressAsync()][gntp]) z identyfikatorem guid używanym podczas wywoływania interfejsu API przejścia węzła dla tej operacji.  Interfejs API postępu przejścia węzła zwraca obiekt NodeTransitionProgress.  Właściwość State tego obiektu określa bieżący stan operacji.  Jeśli stan jest "Uruchomiony", a następnie operacja jest wykonywana.  Jeśli jest zakończona, operacja została zakończona bez błędu.  Jeśli jest uszkodzony, wystąpił problem z wykonaniem operacji.  Właściwość Exception właściwości Result będzie wskazywać, co było problemem.  Zobacz https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate więcej informacji na temat State właściwości i "Przykładowe użycie" poniżej przykłady kodu.


**Rozróżnianie między zatrzymanym węzłem a węzłem w dół** Jeśli węzeł zostanie *zatrzymany* przy użyciu interfejsu API przejścia węzła, dane wyjściowe kwerendy węzła (zarządzane: [GetNodeListAsync()][nodequery], PowerShell: [Get-ServiceFabricNode][nodequeryps]) pokażą, że ten węzeł ma wartość właściwości *IsStopped* true.  Należy zauważyć, że różni się to od wartości *NodeStatus* właściwości, która powie *Down*.  Jeśli *Właściwość NodeStatus* ma wartość *W dół*, ale *IsStopped* jest false, węzeł nie został zatrzymany przy użyciu interfejsu API przejścia węzła i jest *w dół* z innego powodu.  Jeśli *Właściwość IsStopped* jest true, a *Właściwość NodeStatus* jest *w dół,* a następnie został zatrzymany przy użyciu interfejsu API przejścia węzła.

Uruchomienie *zatrzymanego* węzła przy użyciu interfejsu API przejścia węzła spowoduje ponowne przywrócenie go do działania jako zwykły członek klastra.  Dane wyjściowe interfejsu API kwerendy węzła będą wyświetlane *isstopped* jako false i *NodeStatus* jako coś, co nie jest w dół (na przykład w górę).


**Ograniczony czas trwania** Podczas korzystania z interfejsu API przejścia węzła, aby zatrzymać węzeł, jeden z wymaganych parametrów, *stopNodeDurationInSeconds*, reprezentuje czas w sekundach, aby zatrzymać *węzeł*.  Ta wartość musi znajdować się w dozwolonym zakresie, który ma co najmniej 600 i maksymalnie 14400.  Po upływie tego czasu węzeł automatycznie uruchomi się ponownie w stanie Up.  Zapoznaj się z przykładem 1 poniżej, aby uzyskać przykład użycia.

> [!WARNING]
> Należy unikać mieszania interfejsów API przejścia węzła i interfejsów API węzła zatrzymania i węzła startowego.  Zalecenie jest użycie tylko interfejsu API przejścia węzła.  > Jeśli węzeł został już zatrzymany przy użyciu interfejsu API węzła zatrzymania, należy najpierw uruchomić przy użyciu interfejsu API węzła startowego przed użyciem interfejsów API przejścia > węzła.

> [!WARNING]
> Wywołania interfejsów API przejścia wielu węzłów nie mogą być nawiązywane równolegle w tym samym węźle.  W takiej sytuacji interfejs API przejścia węzła będzie > zgłosić FabricException z errorcode wartość właściwości NodeTransitionInProgress.  Po przejściu węzła w określonym węźle > zostało uruchomione, należy poczekać, aż operacja osiągnie stan terminalu (Zakończone, Uszkodzony lub ForceCancelled) przed rozpoczęciem > nowe przejście w tym samym węźle.  Równoległe wywołania przejścia węzła w różnych węzłach są dozwolone.


#### <a name="sample-usage"></a>Przykładowe użycie


**Przykład 1** — poniższy przykład używa interfejsu API przejścia węzła, aby zatrzymać węzeł.

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

**Przykład 2** — poniższy przykład uruchamia *zatrzymany* węzeł.  Używa niektórych metod pomocnika z pierwszej próbki.

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

**Przykład 3** — poniższa próbka pokazuje nieprawidłowe użycie.  To użycie jest niepoprawne, ponieważ *stopDurationInSeconds* zapewnia jest większa niż dozwolony zakres.  Ponieważ StartNodeTransitionAsync() zakończy się niepowodzeniem z błędem oś czasu krytycznego, operacja nie została zaakceptowana i interfejs API postępu nie powinien być wywoływany.  W tym przykładzie użyto niektórych metod pomocnika z pierwszej próbki.

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

**Przykład 4** — w poniższym przykładzie przedstawiono informacje o błędzie, które zostaną zwrócone z interfejsu API postępu przejścia węzła, gdy operacja zainicjowana przez interfejs API przejścia węzła zostanie zaakceptowana, ale nie powiedzie się później podczas wykonywania.  W przypadku niepowodzenia, ponieważ interfejs API przejścia węzła próbuje uruchomić węzeł, który nie istnieje.  W tym przykładzie użyto niektórych metod pomocnika z pierwszej próbki.

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
