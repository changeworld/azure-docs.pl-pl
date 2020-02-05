---
title: Run tasks in parallel to optimize compute resources - Azure Batch
description: Increase efficiency and lower costs by using fewer compute nodes and running concurrent tasks on each node in an Azure Batch pool
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/17/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5465771cb97ef9d8d5c451a6bafc61c4621d3c4b
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023637"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Run tasks concurrently to maximize usage of Batch compute nodes 

By running more than one task simultaneously on each compute node in your Azure Batch pool, you can maximize resource usage on a smaller number of nodes in the pool. For some workloads, this can result in shorter job times and lower cost.

While some scenarios benefit from dedicating all of a node's resources to a single task, several situations benefit from allowing multiple tasks to share those resources:

* **Minimizing data transfer** when tasks are able to share data. In this scenario, you can dramatically reduce data transfer charges by copying shared data to a smaller number of nodes and executing tasks in parallel on each node. This especially applies if the data to be copied to each node must be transferred between geographic regions.
* **Maksymalizowanie użycia pamięci** , gdy zadania wymagają dużej ilości pamięci, ale tylko w krótkich okresach czasu i w zmiennym czasie wykonywania. Możesz użyć mniej, ale większych węzłów obliczeniowych z większą ilością pamięci, aby efektywnie obsłużyć takie skoki. Te węzły mają równolegle uruchomione wiele zadań w każdym węźle, ale każde zadanie będzie korzystać z plentiful pamięci węzłów w różnych godzinach.
* **Ograniczenie liczby węzłów** w przypadku, gdy komunikacja między węzłami jest wymagana w ramach puli. Obecnie pule skonfigurowane pod kątem komunikacji między węzłami są ograniczone do 50 węzłów obliczeniowych. Jeśli każdy węzeł w takiej puli ma możliwość równoległego wykonywania zadań, można jednocześnie wykonać większą liczbę zadań.
* **Replikacja lokalnego klastra obliczeniowego**, na przykład podczas pierwszego przenoszenia środowiska obliczeniowego na platformę Azure. Jeśli bieżące rozwiązanie lokalne wykonuje wiele zadań w każdym węźle obliczeniowym, można zwiększyć maksymalną liczbę zadań węzłów, aby dokładniej zdublować tę konfigurację.

## <a name="example-scenario"></a>Przykładowy scenariusz
Przykładowo, aby zilustrować zalety równoległego wykonywania zadań, Załóżmy, że aplikacja zadania ma wymagania dotyczące procesora CPU i pamięci, które są wystarczające dla węzłów [standardowych\_D1](../cloud-services/cloud-services-sizes-specs.md) . Jednak w celu zakończenia zadania w wymaganym czasie 1 000 z tych węzłów są potrzebne.

Zamiast używać standardowego\_D1 węzłów, które mają 1 rdzeń procesora CPU, można użyć [standardowych\_węzłów D14](../cloud-services/cloud-services-sizes-specs.md) z 16 rdzeniami, a następnie włączyć równoległe wykonywanie zadań. W związku z tym, można użyć *16 razy mniej węzłów* — zamiast węzłów 1 000, wymagana jest tylko 63. Ponadto jeśli w każdym węźle są wymagane duże pliki aplikacji lub dane referencyjne, czas trwania zadania i wydajność są ponownie udoskonalane, ponieważ dane są kopiowane tylko do 63 węzłów.

## <a name="enable-parallel-task-execution"></a>Włącz równoległe wykonywanie zadań
Węzły obliczeniowe można skonfigurować do równoległego wykonywania zadań na poziomie puli. Za pomocą biblioteki Batch .NET ustaw właściwość [CloudPool. MaxTasksPerComputeNode][maxtasks_net] podczas tworzenia puli. Jeśli używasz interfejsu API REST usługi Batch, Ustaw element [maxTasksPerNode][rest_addpool] w treści żądania podczas tworzenia puli.

Azure Batch pozwala ustawiać zadania na węzeł do (4x) liczbę węzłów podstawowych. Na przykład jeśli Pula jest skonfigurowana z węzłami o rozmiarze "duże" (cztery rdzenie), `maxTasksPerNode` może być ustawiona na 16. Jednak niezależnie od tego, ile rdzeni ma węzeł, nie można mieć więcej niż 256 zadań na węzeł. Aby uzyskać szczegółowe informacje o liczbie rdzeni dla każdego rozmiaru węzła, zobacz [rozmiary dla Cloud Services](../cloud-services/cloud-services-sizes-specs.md). Aby uzyskać więcej informacji na temat limitów usługi, zobacz limity [przydziału i limity dla usługi Azure Batch](batch-quota-limit.md).

> [!TIP]
> Pamiętaj, aby wziąć pod uwagę wartość `maxTasksPerNode` podczas konstruowania [formuły automatycznego skalowania][enable_autoscaling] dla puli. Na przykład formuła, która oblicza `$RunningTasks` może mieć znacznie wpływ na wzrost liczby zadań na węzeł. Aby uzyskać więcej informacji [, zobacz Automatyczne skalowanie węzłów obliczeniowych w puli Azure Batch](batch-automatic-scaling.md) .
>
>

## <a name="distribution-of-tasks"></a>Dystrybucja zadań
Gdy węzły obliczeniowe w puli mogą wykonywać zadania współbieżnie, ważne jest, aby określić, w jaki sposób zadania mają być dystrybuowane między węzłami w puli.

Za pomocą właściwości [CloudPool. TaskSchedulingPolicy][task_schedule] można określić, że zadania mają być przypisywane równomiernie we wszystkich węzłach w puli ("rozpraszanie"). Można też określić, że możliwie jak najwięcej zadań należy przypisać do każdego węzła przed przypisaniem zadań do innego węzła w puli ("pakowanie").

Przykładem tego, jak ta funkcja jest cenna, należy wziąć pod uwagę pulę [standardowych\_węzłów D14](../cloud-services/cloud-services-sizes-specs.md) (w powyższym przykładzie), która jest skonfigurowana z wartością [CloudPool. MaxTasksPerComputeNode][maxtasks_net] równą 16. Jeśli [CloudPool. TaskSchedulingPolicy][task_schedule] jest skonfigurowany przy użyciu [ComputeNodeFillType][fill_type] *pakietu*, maksymalizuje użycie wszystkich 16 rdzeni każdego węzła i zezwoli [puli skalowania](batch-automatic-scaling.md) automatycznego na oczyszczanie nieużywanych węzłów z puli (węzły bez przypisanych zadań). Pozwala to zminimalizować użycie zasobów i zaoszczędzić pieniądze.

## <a name="batch-net-example"></a>Przykład platformy .NET w usłudze Batch
Ten fragment kodu interfejsu API usługi [Batch .NET][api_net] przedstawia żądanie utworzenia puli zawierającej cztery węzły z maksymalnie czterema zadaniami na węzeł. Określa zasady planowania zadań, które będą wypełnić każdy węzeł zadaniami przed przypisaniem zadań do innego węzła w puli. Aby uzyskać więcej informacji na temat dodawania pul przy użyciu interfejsu API usługi Batch platformy .NET, zobacz [BatchClient. PoolOperations. ispool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Przykład REST w usłudze Batch
Ten fragment kodu API [REST usługi Batch][api_rest] przedstawia żądanie utworzenia puli zawierającej dwa duże węzły z maksymalnie czterema zadaniami na węzeł. Aby uzyskać więcej informacji na temat dodawania pul przy użyciu interfejsu API REST, zobacz [Dodawanie puli do konta][rest_addpool].

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  }
  "targetDedicatedComputeNodes":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [!NOTE]
> Dla elementu `maxTasksPerNode` i właściwości [MaxTasksPerComputeNode][maxtasks_net] można ustawić tylko w czasie tworzenia puli. Nie można ich modyfikować po utworzeniu puli.
>
>

## <a name="code-sample"></a>Przykład kodu
Projekt [ParallelNodeTasks][parallel_tasks_sample] w usłudze GitHub ilustruje użycie właściwości [CloudPool. MaxTasksPerComputeNode][maxtasks_net] .

Ta C# Aplikacja konsolowa używa biblioteki [programu .NET Batch][api_net] do utworzenia puli z co najmniej jednym węzłem obliczeniowym. Wykonuje konfigurowalną liczbę zadań w tych węzłach, aby symulować obciążenie zmienne. Dane wyjściowe aplikacji określają, które węzły wykonali każde zadanie. Aplikacja zawiera również podsumowanie parametrów zadania i czasu trwania. Poniżej zostanie wyświetlona część podsumowania danych wyjściowych z dwóch różnych uruchomień przykładowej aplikacji.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

Pierwsze wykonanie przykładowej aplikacji pokazuje, że z pojedynczym węzłem w puli i domyślnym ustawieniem jednego zadania na węzeł, czas trwania zadania wynosi ponad 30 minut.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

Drugie uruchomienie przykładu pokazuje znaczny spadek czasu trwania zadania. Wynika to z faktu, że pula została skonfigurowana z czterema zadaniami na węzeł, co umożliwia równoległe wykonywanie zadań, aby zakończyć zadanie w prawie czwartym czasie.

> [!NOTE]
> Czasy trwania zadań w powyższych podsumowaniu nie obejmują czasu utworzenia puli. Każde z powyższych zadań zostało przesłane do wcześniej utworzonych pul, których węzły obliczeniowe były w stanie *bezczynności* w czasie przesyłania.
>
>

## <a name="next-steps"></a>Następne kroki
### <a name="batch-explorer-heat-map"></a>Mapa cieplna Batch Explorer
[Batch Explorer][batch_labs] to bezpłatne, bogate w funkcje, autonomiczne narzędzie klienta pomagające tworzyć, debugować i monitorować aplikacje Azure Batch. Batch Explorer zawiera funkcję *mapy cieplnej* , która zapewnia wizualizację wykonywania zadań. Podczas wykonywania przykładowej aplikacji [ParallelTasks][parallel_tasks_sample] można użyć funkcji mapy cieplnej, aby łatwo wizualizować wykonywanie równoległych zadań w każdym węźle.


[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

