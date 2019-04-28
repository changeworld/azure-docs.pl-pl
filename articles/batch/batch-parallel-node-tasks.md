---
title: Uruchamianie zadań w sposób równoległy efektywnie - używania zasobów obliczeniowych usługi Azure Batch | Dokumentacja firmy Microsoft
description: Zwiększ wydajność i Obniż koszty przy użyciu mniejszej liczby węzłów obliczeniowych i uruchomieniu współbieżnych zadań w każdym węźle w puli usługi Azure Batch
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
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
ms.openlocfilehash: 79b45bd423ed6715cdb7cc7c0e079c150eefede5
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763698"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Węzły obliczeniowe uruchamiania zadań jednocześnie w celu zmaksymalizowania użycia usługi Batch 

Uruchamiając więcej niż jednego zadania jednocześnie w każdym węźle obliczeniowym w puli Azure Batch, można zmaksymalizować wykorzystanie zasobów w mniejszej liczby węzłów w puli. Dla niektórych obciążeń może to spowodować krótsze czasy zadań i obniżenie kosztów.

Podczas gdy niektóre scenariusze korzystać z dedykowanym wszystkie zasoby z węzła do jednego zadania, kilka sytuacji, w korzyści dzięki czemu wiele zadań udostępnić te zasoby:

* **Minimalizacja transfer danych** gdy zadania są w stanie udostępniać dane. W tym scenariuszu może znacznie zmniejszyć opłaty za transfer danych przez skopiowanie danych udostępnionych do mniejszej liczby węzłów i wykonywanie zadań równolegle na każdym węźle. Dotyczy to zwłaszcza, jeśli dane do skopiowania na każdym węźle muszą być przekazywane między regionów geograficznych.
* **Maksymalizacja użycia pamięci** podczas zadania wymagają dużej ilości pamięci, ale tylko w krótkim czasie i w czasie zmiennych podczas wykonywania. Można stosować węzłów obliczeniowych mniej, ale większe, więcej pamięci, aby efektywnie obsłużyć takie skoki. Te węzły będzie mieć wiele zadań uruchamianych równolegle na każdym węźle, ale każde zadanie podrzędne może korzystać z węzłów dużej ilości pamięci w różnym czasie.
* **Łagodzenia limity numer węzła** podczas komunikacji między węzłami jest wymagany w puli. Obecnie skonfigurowany do komunikacji między węzłami pule są ograniczone do węzłów obliczeniowych, 50. Jeśli każdy węzeł w takich puli jest możliwość wykonywania zadań w sposób równoległy, większą liczbę zadań mogą być wykonywane jednocześnie.
* **Replikowanie lokalnych klastra obliczeniowego**, np. gdy użytkownik najpierw przenieść środowisko obliczeniowe na platformie Azure. Jeśli z bieżącego rozwiązania w środowisku lokalnym wykonuje wiele zadań w każdym węźle obliczeń, możesz zwiększyć maksymalną liczbę zadań podrzędnych węzła dokładniej duplikatów tej konfiguracji.

## <a name="example-scenario"></a>Przykładowy scenariusz
Przykład ilustrujący zalety równoległe wykonywanie zadań podrzędnych, załóżmy, że Twoja aplikacja zadanie ma wymagania dotyczące procesora CPU i pamięci, [standardowa\_D1](../cloud-services/cloud-services-sizes-specs.md) węzły są wystarczające. Jednak aby można było zakończyć zadanie w wymaganym czasie, 1000 te węzły są potrzebne.

Zamiast przy użyciu standardu\_węzły D1, które mają 1 rdzenia Procesora, można użyć [standardowa\_D14](../cloud-services/cloud-services-sizes-specs.md) węzły, które ma 16 rdzeni i umożliwiają równoległe wykonywanie zadań podrzędnych. W związku z tym *16 razy mniej węzłów* może służyć — zamiast 1000 węzłów tylko 63 będą wymagane. Ponadto jeśli pliki dużych aplikacji lub danych referencyjnych są wymagane dla każdego węzła, czas trwania zadania i wydajności ponownie lepsza, ponieważ dane są kopiowane do 63 węzłów.

## <a name="enable-parallel-task-execution"></a>Włącz równoległe wykonywanie zadań podrzędnych
Węzły obliczeniowe, aby równoległe wykonywanie zadań podrzędnych można konfigurować na poziomie puli. Za pomocą biblioteki .NET usługi Batch, należy ustawić [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] właściwość podczas tworzenia puli. Jeśli używasz interfejsu API REST usługi Batch, ustaw [maxTasksPerNode] [ rest_addpool] elementu w treści żądania podczas tworzenia puli.

Usługa Azure Batch umożliwia ustawienie zadań na węzeł (4 x) — maksymalna liczba węzłów core. Na przykład, jeśli pula jest skonfigurowana z węzłami rozmiaru "Duże" (4 rdzenie) `maxTasksPerNode` może być ustawiona na 16. Jednak niezależnie od tego, liczba rdzeni na węzeł zawiera, nie może mieć więcej niż 256 zadań na węzeł. Szczegółowe informacje na temat liczby rdzeni dla każdego rozmiary węzłów, [rozmiary usług Cloud Services](../cloud-services/cloud-services-sizes-specs.md). Aby uzyskać więcej informacji o limitach usługi, zobacz [przydziały i limity dla usługi Azure Batch](batch-quota-limit.md).

> [!TIP]
> Pamiętaj wziąć pod uwagę `maxTasksPerNode` wartości podczas konstruowania [formułę skalowania automatycznego] [ enable_autoscaling] dla puli. Na przykład formuła, której wynikiem `$RunningTasks` może znacząco wpływać wzrost zadań na węzeł. Zobacz [automatyczne skalowanie węzłów obliczeniowych w puli usługi Azure Batch](batch-automatic-scaling.md) Aby uzyskać więcej informacji.
>
>

## <a name="distribution-of-tasks"></a>Podział zadań
W węzłach obliczeniowych w puli mogą jednocześnie wykonywanie zadań, jest ważne, aby określić sposób zadań, które mają być dystrybuowane między węzłami w puli.

Za pomocą [CloudPool.TaskSchedulingPolicy] [ task_schedule] właściwości, można określić czy zadania powinny być przypisane równomiernie we wszystkich węzłach w puli ("rozmieszczenie"). Lub można określić, że dowolną liczbę zadań, jak to możliwe powinny być przypisane do każdego węzła, przed przypisaniem zadań do innego węzła w puli ("pakowanie").

Jako przykład jak ta funkcja jest przydatna, należy wziąć pod uwagę puli [standardowa\_D14](../cloud-services/cloud-services-sizes-specs.md) węzłów (w powyższym przykładzie), które jest skonfigurowane z użyciem [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] wartość 16. Jeśli [CloudPool.TaskSchedulingPolicy] [ task_schedule] skonfigurowano [ComputeNodeFillType] [ fill_type] z *pakiet*, zostaną zmaksymalizować użycie wszystkich 16 rdzeni w każdym węźle, a umożliwić [automatyczne skalowanie puli](batch-automatic-scaling.md) Aby oczyścić nieużywane węzły z puli (węzły bez żadnych zadań przypisanych). Minimalizuje użycie zasobów i pozwala oszczędzać pieniądze.

## <a name="batch-net-example"></a>Przykład dla środowiska .NET usługi Batch
To [platformy .NET usługi Batch] [ api_net] interfejsu API, fragment kodu przedstawia żądania, aby utworzyć pulę, która zawiera cztery węzły z maksymalnie cztery zadania podrzędne w każdym węźle. Określa zadania harmonogramu zasad, które spowoduje wypełnienie każdego węzła za pomocą zadań przed przypisaniem zadań do innego węzła w puli. Aby uzyskać więcej informacji na temat dodawania pule przy użyciu interfejsu API .NET usługi Batch, zobacz [BatchClient.PoolOperations.CreatePool][poolcreate_net].

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

## <a name="batch-rest-example"></a>Przykład REST usługi Batch
To [interfejs REST usługi Batch] [ api_rest] API fragment kodu przedstawia żądanie, aby utworzyć pulę, która zawiera dwa węzły dużych maksymalnie cztery zadania podrzędne w każdym węźle. Aby uzyskać więcej informacji na temat dodawania pule przy użyciu interfejsu API REST, zobacz [Dodawanie puli do konta][rest_addpool].

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
> Możesz ustawić `maxTasksPerNode` elementu i [MaxTasksPerComputeNode] [ maxtasks_net] właściwość tylko w czasie tworzenia puli. Nie można zmodyfikować po puli został już utworzony.
>
>

## <a name="code-sample"></a>Przykład kodu
[ParallelNodeTasks] [ parallel_tasks_sample] projektu w usłudze GitHub ilustruje użycie [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] właściwości.

Ta aplikacja konsoli C# używa [platformy .NET usługi Batch] [ api_net] biblioteki, aby utworzyć pulę przy użyciu jednego lub więcej węzłów obliczeniowych. Można skonfigurować wiele zadań jest wykonywana w ramach tych węzłów do symulacji obciążenia. Dane wyjściowe aplikacji Określa węzły, które są wykonywane każdego zadania. Aplikacja udostępnia również podsumowanie parametrów zadania i czas trwania. Podsumowanie części danych wyjściowych z dwóch różnych tras w przykładowej aplikacji znajduje się poniżej.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

Pierwsze wykonanie Przykładowa aplikacja pokazuje, że z jednego węzła w puli i domyślne ustawienie jedno zadanie podrzędne w każdym węźle, czas trwania zadania to ponad 30 minut.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

Drugi uruchomienia przedstawia przykładowe znaczny spadek czas trwania zadania. Jest to spowodowane puli zostało skonfigurowane przy użyciu czterech zadań na węzeł, która umożliwia równoległe wykonywanie zadań podrzędnych do ukończenia zadania w prawie kwartale czasu.

> [!NOTE]
> Czas trwania zadania w powyższym podsumowania nie dołączaj godzina utworzenia puli. Każde z zadań powyżej zostało przesłane do wcześniej utworzonych pul, którego węzły obliczeniowe były w *bezczynne* stanu w chwili przesyłania.
>
>

## <a name="next-steps"></a>Kolejne kroki
### <a name="batch-explorer-heat-map"></a>Mapa cieplna Eksplorator usługi Batch
[Batch Explorer] [ batch_labs] to bezpłatne, bogate w funkcje, autonomiczne narzędzie klienta pomagające tworzyć, debugować i monitorować aplikacje usługi Azure Batch. Narzędzie Batch Explorer zawiera *Mapa cieplna* funkcja, która zapewnia wizualizacji wykonywania zadania. Kiedy wykonujesz [ParallelTasks] [ parallel_tasks_sample] przykładowej aplikacji, można użyć funkcji Mapa cieplna można łatwo przedstawić wizualnie wykonywania równoległych zadań podrzędnych w każdym węźle.


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

