---
title: Symulowanie błędów w aplikacjach usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Jak zabezpieczyć usługi przed awariami łagodne i nieprawidłowego.
services: service-fabric
documentationcenter: .net
author: anmolah
manager: timlt
editor: ''
ms.assetid: 44af01f0-ed73-4c31-8ac0-d9d65b4ad2d6
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: 3c075ac9642c7d050fc45ce6164071c9c733326e
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44051918"
---
# <a name="simulate-failures-during-service-workloads"></a>Symulowanie błędów w trakcie obciążenia usługi
Scenariusze testowania w usłudze Azure Service Fabric umożliwia deweloperom nie martw się o obsłudze poszczególne błędy. Istnieją scenariusze, jednak gdy jawne z przeplotem obciążenia klienta i błędy mogą być wymagane. Z przeplotem obciążenia klienta i błędy gwarantuje, że usługa wykonuje rzeczywistą operację niektóre akcje, gdy awaria. Określony poziom formant, który udostępnia możliwości testowania, mogą to być momentach dokładne wykonywania obciążenia. Ten indukowana usterek w różnych stanach w aplikacji można znaleźć błędy i poprawić jakość.

## <a name="sample-custom-scenario"></a>Przykładowy scenariusz niestandardowe
Ten test przedstawiono scenariusz, w którym przeplatają obciążeń biznesowych za pomocą [błędy łagodne i nieprawidłowego](service-fabric-testability-actions.md#graceful-vs-ungraceful-fault-actions). Błędy powinny zostać wywołane w środku operacji usługi lub obliczeniowych w celu uzyskania najlepszych wyników.

Przejdźmy przykładowej usługi, który udostępnia cztery obciążeń: A, B, C i D. Każda zestawowi przepływów pracy i może być obliczeń, magazynu lub mieszane. Dla uproszczenia będzie abstrakcji obciążeń w naszym przykładzie. Są różne błędy, wykonywane na w tym przykładzie:

* RestartNode: Błąd nieprawidłowego Aby zasymulować ponowne uruchomienie komputera.
* RestartDeployedCodePackage: Nieprawidłowego błędów, aby zasymulować proces hosta usługi kończy się niepowodzeniem.
* RemoveReplica: Łagodne błędów, aby zasymulować usuwania repliki.
* MovePrimary: Łagodne błędów, aby zasymulować repliki przenosi wyzwalane przez moduł równoważenia obciążenia usługi Service Fabric.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        // Replace these strings with the actual version for your cluster and application.
        string clusterConnection = "localhost:19000";
        Uri applicationName = new Uri("fabric:/samples/PersistentToDoListApp");
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Workload Test...");
        try
        {
            RunTestAsync(clusterConnection, applicationName, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Workload Test failed: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Workload Test completed successfully.");
        return 0;
    }

    public enum ServiceWorkloads
    {
        A,
        B,
        C,
        D
    }

    public enum ServiceFabricFaults
    {
        RestartNode,
        RestartCodePackage,
        RemoveReplica,
        MovePrimary,
    }

    public static async Task RunTestAsync(string clusterConnection, Uri applicationName, Uri serviceName)
    {
        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);
        // Maximum time to wait for a service to stabilize.
        TimeSpan maxServiceStabilizationTime = TimeSpan.FromSeconds(120);

        // How many loops of faults you want to execute.
        uint testLoopCount = 20;
        Random random = new Random();

        for (var i = 0; i < testLoopCount; ++i)
        {
            var workload = SelectRandomValue<ServiceWorkloads>(random);
            // Start the workload.
            var workloadTask = RunWorkloadAsync(workload);

            // While the task is running, induce faults into the service. They can be ungraceful faults like
            // RestartNode and RestartDeployedCodePackage or graceful faults like RemoveReplica or MovePrimary.
            var fault = SelectRandomValue<ServiceFabricFaults>(random);

            // Create a replica selector, which will select a primary replica from the given service to test.
            var replicaSelector = ReplicaSelector.PrimaryOf(PartitionSelector.RandomOf(serviceName));
            // Run the selected random fault.
            await RunFaultAsync(applicationName, fault, replicaSelector, fabricClient);
            // Validate the health and stability of the service.
            await fabricClient.ServiceManager.ValidateServiceAsync(serviceName, maxServiceStabilizationTime);

            // Wait for the workload to finish successfully.
            await workloadTask;
        }
    }

    private static async Task RunFaultAsync(Uri applicationName, ServiceFabricFaults fault, ReplicaSelector selector, FabricClient client)
    {
        switch (fault)
        {
            case ServiceFabricFaults.RestartNode:
                await client.ClusterManager.RestartNodeAsync(selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RestartCodePackage:
                await client.ApplicationManager.RestartDeployedCodePackageAsync(applicationName, selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RemoveReplica:
                await client.ServiceManager.RemoveReplicaAsync(selector, CompletionMode.Verify, false);
                break;
            case ServiceFabricFaults.MovePrimary:
                await client.ServiceManager.MovePrimaryAsync(selector.PartitionSelector);
                break;
        }
    }

    private static Task RunWorkloadAsync(ServiceWorkloads workload)
    {
        throw new NotImplementedException();
        // This is where you trigger and complete your service workload.
        // Note that the faults induced while your service workload is running will
        // fault the primary service. Hence, you will need to reconnect to complete or check
        // the status of the workload.
    }

    private static T SelectRandomValue<T>(Random random)
    {
        Array values = Enum.GetValues(typeof(T));
        T workload = (T)values.GetValue(random.Next(values.Length));
        return workload;
    }
}
```
