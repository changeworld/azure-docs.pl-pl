---
title: Równoległe Uruchamianie zadań w celu wydajnego używania zasobów obliczeniowych — Azure Batch | Microsoft Docs
description: Zwiększenie wydajności i obniżenie kosztów dzięki użyciu mniejszej liczby węzłów obliczeniowych i uruchamiania współbieżnych zadań w każdym węźle w puli Azure Batch
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/17/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cc6a607da2227ecf9acd6209e31b7aa0ef1c62d8
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323362"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Uruchom zadania współbieżnie, aby zmaksymalizować użycie węzłów obliczeniowych wsadowych 

Uruchamiając więcej niż jedno zadanie jednocześnie w każdym węźle obliczeniowym w puli Azure Batch, można zmaksymalizować użycie zasobów na mniejszej liczbie węzłów w puli. W przypadku niektórych obciążeń może to spowodować skrócenie czasu zadania i obniżenie kosztów.

Chociaż niektóre scenariusze korzystają z przydzielenia wszystkich zasobów węzła do jednego zadania, niektóre sytuacje mogą korzystać z umożliwienia wielu zadań współdzielenia tych zasobów:

* **Minimalizowanie transferu danych** , gdy zadania mogą udostępniać dane. W tym scenariuszu można znacznie zmniejszyć opłaty za transfer danych przez skopiowanie danych udostępnionych na mniejszą liczbę węzłów i wykonywanie zadań równolegle na każdym węźle. W szczególności ma to zastosowanie, jeśli dane, które mają być skopiowane do każdego węzła, muszą być transferowane między regionami geograficznymi.
* **Maksymalizowanie użycia pamięci** , gdy zadania wymagają dużej ilości pamięci, ale tylko w krótkich okresach czasu i w zmiennym czasie wykonywania. Możesz użyć mniej, ale większych węzłów obliczeniowych z większą ilością pamięci, aby efektywnie obsłużyć takie skoki. Te węzły mają równolegle uruchomione wiele zadań w każdym węźle, ale każde zadanie będzie korzystać z plentiful pamięci węzłów w różnych godzinach.
* **Ograniczenie liczby węzłów** w przypadku, gdy komunikacja między węzłami jest wymagana w ramach puli. Obecnie pule skonfigurowane pod kątem komunikacji między węzłami są ograniczone do 50 węzłów obliczeniowych. Jeśli każdy węzeł w takiej puli ma możliwość równoległego wykonywania zadań, można jednocześnie wykonać większą liczbę zadań.
* **Replikacja lokalnego klastra obliczeniowego**, na przykład podczas pierwszego przenoszenia środowiska obliczeniowego na platformę Azure. Jeśli bieżące rozwiązanie lokalne wykonuje wiele zadań w każdym węźle obliczeniowym, można zwiększyć maksymalną liczbę zadań węzłów, aby dokładniej zdublować tę konfigurację.

## <a name="example-scenario"></a>Przykładowy scenariusz
Przykładowo, aby zilustrować zalety równoległego wykonywania zadań, Załóżmy, że aplikacja zadania ma wymagania dotyczące [\_](../cloud-services/cloud-services-sizes-specs.md) procesora CPU i pamięci, co jest wystarczające. Jednak w celu zakończenia zadania w wymaganym czasie 1 000 z tych węzłów są potrzebne.

Zamiast używać standardowych\_węzłów D1, które mają 1 rdzeń procesora CPU, można użyć [standardowych\_węzłów D14](../cloud-services/cloud-services-sizes-specs.md) o 16 rdzeniach i włączyć równoległe wykonywanie zadań. W związku z tym, można użyć *16 razy mniej węzłów* — zamiast węzłów 1 000, wymagana jest tylko 63. Ponadto jeśli w każdym węźle są wymagane duże pliki aplikacji lub dane referencyjne, czas trwania zadania i wydajność są ponownie udoskonalane, ponieważ dane są kopiowane tylko do 63 węzłów.

## <a name="enable-parallel-task-execution"></a>Włącz równoległe wykonywanie zadań
Węzły obliczeniowe można skonfigurować do równoległego wykonywania zadań na poziomie puli. Za pomocą biblioteki Batch .NET Ustaw element [CloudPool. MaxTasksPerComputeNode][maxtasks_net] property when you create a pool. If you are using the Batch REST API, set the [maxTasksPerNode][rest_addpool] w treści żądania podczas tworzenia puli.

Azure Batch pozwala ustawiać zadania na węzeł do (4x) liczbę węzłów podstawowych. Na przykład, jeśli Pula jest skonfigurowana z węzłami o rozmiarze "duże" (cztery rdzenie), `maxTasksPerNode` wówczas może być ustawiona na 16. Jednak niezależnie od tego, ile rdzeni ma węzeł, nie można mieć więcej niż 256 zadań na węzeł. Aby uzyskać szczegółowe informacje o liczbie rdzeni dla każdego rozmiaru węzła, zobacz [rozmiary dla Cloud Services](../cloud-services/cloud-services-sizes-specs.md). Aby uzyskać więcej informacji na temat limitów usługi, zobacz limity [przydziału i limity dla usługi Azure Batch](batch-quota-limit.md).

> [!TIP]
> Pamiętaj, `maxTasksPerNode` aby wziąć pod uwagę wartość podczas konstruowania [formuły skalowania automatycznego][enable_autoscaling] dla puli. Na przykład `$RunningTasks` można znacznie wpływać na liczbę zadań na węzeł. Aby uzyskać więcej informacji [, zobacz Automatyczne skalowanie węzłów obliczeniowych w puli Azure Batch](batch-automatic-scaling.md) .
>
>

## <a name="distribution-of-tasks"></a>Dystrybucja zadań
Gdy węzły obliczeniowe w puli mogą wykonywać zadania współbieżnie, ważne jest, aby określić, w jaki sposób zadania mają być dystrybuowane między węzłami w puli.

Za pomocą właściwości [CloudPool. TaskSchedulingPolicy][task_schedule] można określić, że zadania mają być przypisywane równomiernie we wszystkich węzłach w puli ("rozpraszanie"). Można też określić, że możliwie jak najwięcej zadań należy przypisać do każdego węzła przed przypisaniem zadań do innego węzła w puli ("pakowanie").

Przykładowo, jak ta funkcja jest cenna, weź pod uwagę pulę [standardowych\_węzłów D14](../cloud-services/cloud-services-sizes-specs.md) (w powyższym przykładzie), która jest skonfigurowana za pomocą [CloudPool. MaxTasksPerComputeNode][maxtasks_net] value of 16. If the [CloudPool.TaskSchedulingPolicy][task_schedule] jest skonfigurowana z [ ComputeNodeFillType][fill_type] *Pack*, maksymalizuje użycie wszystkich 16 rdzeni każdego węzła i zezwoli [puli skalowania](batch-automatic-scaling.md) automatycznego na oczyszczanie nieużywanych węzłów z puli (węzły bez przypisanych zadań). Pozwala to zminimalizować użycie zasobów i zaoszczędzić pieniądze.

## <a name="batch-net-example"></a>Przykład platformy .NET w usłudze Batch
Ten [Partia programu .NET][api_net] API code snippet shows a request to create a pool that contains four nodes with a maximum of four tasks per node. It specifies a task scheduling policy that will fill each node with tasks prior to assigning tasks to another node in the pool. For more information on adding pools by using the Batch .NET API, see [BatchClient.PoolOperations.CreatePool][poolcreate_net].

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
Ta [Partia zadań][api_rest] API snippet shows a request to create a pool that contains two large nodes with a maximum of four tasks per node. For more information on adding pools by using the REST API, see [Add a pool to an account][rest_addpool].

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
> Właściwość `maxTasksPerNode` element i [MaxTasksPerComputeNode][maxtasks_net] można ustawić tylko w czasie tworzenia puli. Nie można ich modyfikować po utworzeniu puli.
>
>

## <a name="code-sample"></a>Przykład kodu
Właściwość [ParallelNodeTasks][parallel_tasks_sample] project on GitHub illustrates the use of the [CloudPool.MaxTasksPerComputeNode][maxtasks_net] .

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
> Czasy trwania zadań w powyższych podsumowaniu nie obejmują czasu utworzenia puli. Każde z powyższych zadań zostało przesłane do wcześniej utworzonych pul, których węzły obliczeniowe były w  stanie bezczynności w czasie przesyłania.
>
>

## <a name="next-steps"></a>Kolejne kroki
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

