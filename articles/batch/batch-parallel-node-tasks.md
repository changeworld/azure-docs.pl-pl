---
title: Uruchom zadania równolegle efektywnie - korzystać z zasobów obliczeniowych partii zadań Azure | Dokumentacja firmy Microsoft
description: Zwiększenie wydajności i zredukowania kosztów przy użyciu mniejszej liczby węzłów obliczeniowych i uruchamianie równoczesnych zadań w każdym węźle w puli partii zadań Azure
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5106bbbb073908af7e7e8f045fa6fb60e8a306f4
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Węzły obliczeniowe uruchamiania zadań jednocześnie, aby zapewnić maksymalne użycie usługi partia zadań 

Uruchamiając więcej niż jedno zadanie równocześnie w każdym węźle obliczeń w puli partii zadań Azure, można zmaksymalizować użycia zasobów na mniejszą liczbę węzłów w puli. W przypadku niektórych obciążeń może to spowodować krótszych czasów zadań i tańsze.

Gdy niektóre scenariusze korzystania z dedykowanym wszystkie zasoby węzła do danego zadania, kilku sytuacjach korzystać z wielu zadań udostępnić te zasoby umożliwiające:

* **Minimalizowanie transferu danych** podczas zadania będą mogli udostępniać dane. W tym scenariuszu można zdecydowanie zmniejszyć koszty transferu danych przez skopiowanie danych udostępnionych do mniejszej liczby węzłów i wykonywanie zadań równolegle na każdym węźle. Dotyczy to zwłaszcza, jeśli dane do skopiowania na każdym węźle muszą być przekazywane między regionów geograficznych.
* **Maksymalizacja użycie pamięci** podczas zadania wymagają dużej ilości pamięci, ale tylko w krótkich okresach czasu i w czasie zmiennej podczas wykonywania. Można wdrożyć węzły obliczeniowe mniej, ale są większe, więcej pamięci do efektywnej obsługi tych wartości szczytowe. Te węzły byłyby wielu zadań uruchomione równolegle w każdym węźle, ale każde zadanie będzie korzystać z tych węzłów dużej ilości pamięci w różnym czasie.
* **Zmniejszenia limity numer węzła** podczas komunikacji między węzłami jest wymagana w puli. Obecnie skonfigurowane do komunikacji między węzłami pule są ograniczone do 50 węzłów obliczeniowych. Jeśli każdy węzeł w takich puli jest w stanie wykonywać zadania równolegle, większa liczba zadań mogą być wykonywane jednocześnie.
* **Replikowanie lokalnych klastra obliczeniowego**, takie jak kiedy należy najpierw przenieść środowiska obliczeniowe na platformie Azure. Jeśli lokalne rozwiązanie wykonuje wiele zadań na węzeł obliczeń, można zwiększyć maksymalną liczbę zadań węzła dokładniejsze dublowanego tej konfiguracji.

## <a name="example-scenario"></a>Przykładowy scenariusz
Na przykład ilustrujący zalet wykonywanie zadań równoległych Załóżmy, że czy aplikacji zadanie ma wymagania dotyczące procesora CPU i pamięci tak, aby [standardowe\_D1](../cloud-services/cloud-services-sizes-specs.md) węzły są wystarczające. Jednak, aby zakończyć zadanie w wymaganym czasie, 1000 te węzły są potrzebne.

Zamiast standardowego\_D1 węzły, które mają 1 rdzeń procesora CPU, można użyć [standardowe\_D14](../cloud-services/cloud-services-sizes-specs.md) węzłów, które ma 16 rdzeni i włącz wykonywanie zadań równoległych. W związku z tym *16 razy mniej węzłów* można użyć — zamiast 1000 węzłów tylko 63 będą wymagane. Ponadto jeśli pliki dużych aplikacji lub dane referencyjne są wymagane dla każdego węzła, czas trwania zadania i wydajności ponownie zwiększona, ponieważ dane są kopiowane do 63 węzłów.

## <a name="enable-parallel-task-execution"></a>Włącz wykonywanie zadań równoległych
Węzłami obliczeniowymi w celu wykonania zadań równoległych można skonfigurować na poziomie puli. Z biblioteki partiami platformy .NET, należy ustawić [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] właściwość podczas tworzenia puli. Jeśli korzystasz z interfejsu API REST partii, ustaw [maxTasksPerNode] [ rest_addpool] elementu w treści żądania podczas tworzenia puli.

Partia zadań Azure umożliwia określenie maksymalnej zadań na węzeł maksymalnie cztery razy (4 x) liczba rdzeni węzła. Na przykład, jeśli pula jest skonfigurowana z węzłami z rozmiar "Duże" (4 rdzenie), następnie `maxTasksPerNode` ustawioną 16. Szczegółowe informacje dotyczące liczby rdzeni dla każdego węzła rozmiarów, zobacz [rozmiary dla usług w chmurze](../cloud-services/cloud-services-sizes-specs.md). Aby uzyskać więcej informacji na ograniczenia usługi, zobacz [przydziały i limity dla usługi partia zadań Azure](batch-quota-limit.md).

> [!TIP]
> Należy wziąć pod uwagę `maxTasksPerNode` wartość utworzenia [formułą automatycznego skalowania] [ enable_autoscaling] dla użytkownika. Na przykład formuła, której wynikiem `$RunningTasks` może mieć znaczący wpływ wzrost zadań na węzeł. Zobacz [automatycznie skali obliczeniowe węzłów w puli partii zadań Azure](batch-automatic-scaling.md) Aby uzyskać więcej informacji.
>
>

## <a name="distribution-of-tasks"></a>Podział zadań
Węzły obliczeń w puli mogą jednocześnie wykonywać zadania, należy określić sposób zadań, które mają być dystrybuowane między węzłami w puli.

Za pomocą [CloudPool.TaskSchedulingPolicy] [ task_schedule] właściwości, można określić czy zadania powinien być przypisany równomiernie we wszystkich węzłach w puli ("rozpraszania"). Lub możesz określić, że jako liczbę zadań powinny zostać przypisane do każdego węzła przed zadania są przypisane do innego węzła w puli ("pakowania").

Jako przykład sposobu ta funkcja jest przydatna, należy wziąć pod uwagę puli [standardowe\_D14](../cloud-services/cloud-services-sizes-specs.md) (w powyższym przykładzie) węzły, które są skonfigurowane przy użyciu [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] wartość 16. Jeśli [CloudPool.TaskSchedulingPolicy] [ task_schedule] skonfigurowano [ComputeNodeFillType] [ fill_type] z *pakietu*, czy zmaksymalizować użycie wszystkich rdzeni 16 każdego węzła i umożliwia [puli Skalowanie automatyczne](batch-automatic-scaling.md) Aby oczyścić nieużywane węzłów z puli (węzłów bez żadnych zadań przypisane). Ten sposób można zmniejszyć obciążenie zasobów i zapisuje pieniędzy.

## <a name="batch-net-example"></a>Przykład .NET partii
To [partiami platformy .NET] [ api_net] interfejsu API fragment kodu przedstawia żądanie, aby utworzyć pulę zawierającą czterech węzłów dużych maksymalnie cztery zadania w każdym węźle. Określa zadanie planowania zasad, który zostanie wypełniony każdego węzła zadania przed przypisywanie zadań do innego węzła w puli. Aby uzyskać więcej informacji o dodawaniu pule przy użyciu interfejsu API programu .NET partii, zobacz [BatchClient.PoolOperations.CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "large",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Przykład REST partii
To [REST partii] [ api_rest] interfejsu API fragment kodu przedstawia żądanie, aby utworzyć pulę, która zawiera dwa węzły dużych maksymalnie cztery zadania w każdym węźle. Aby uzyskać więcej informacji o dodawaniu pule przy użyciu interfejsu API REST, zobacz [Dodaj pulę, aby konto][rest_addpool].

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
> Można ustawić `maxTasksPerNode` elementu i [MaxTasksPerComputeNode] [ maxtasks_net] właściwość tylko w czasie tworzenia puli. Nie można zmodyfikować po puli został już utworzony.
>
>

## <a name="code-sample"></a>Przykład kodu
[ParallelNodeTasks] [ parallel_tasks_sample] projektu w witrynie GitHub ilustruje użycie [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] właściwości.

Korzysta z tej aplikacji konsolowej C# [partiami platformy .NET] [ api_net] biblioteki, aby utworzyć pulę z co najmniej jednym węźle obliczeń. Można skonfigurować wiele zadań jest wykonywana na tych węzłach, aby symulować zmiennej obciążenia. Dane wyjściowe aplikacji Określa węzły, które wykonywane każdego zadania. Aplikacja jest także podsumowanie parametrów zadania i czas trwania. Podsumowanie część dane wyjściowe z dwóch różnych uruchomień przykładowej aplikacji pojawia się poniżej.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

Pierwszy wykonywanie przykładowej aplikacji wskazuje, że z jednego węzła w puli i ustawieniem domyślnym jednego zadania w każdym węźle, czas trwania zadania jest ponad 30 minut.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

Drugi uruchomienie przedstawiono przykładowe znaczny spadek czas trwania zadania. Jest to spowodowane puli został skonfigurowany z czterech zadań na węzeł, który pozwala na ukończenie zadania w niemal kwartału czasu wykonywania zadań równoległych.

> [!NOTE]
> Czas trwania zadania w powyższym podsumowania nie dołączaj czas utworzenia puli. Każdy z powyższych zadania zostało przesłane do utworzonej wcześniej pule, którego węzły obliczeniowe były w *bezczynny* stanu w czasie przesyłania.
>
>

## <a name="next-steps"></a>Kolejne kroki
### <a name="batchlabs-heat-map"></a>Mapa cieplna BatchLabs
[BatchLabs][batch_labs] to bezpłatne, bogate w funkcje, autonomiczne narzędzie klienta pomagające tworzyć, debugować i monitorować aplikacje usługi Azure Batch. Zawiera BatchLabs *Mapa cieplna* funkcja, która umożliwia wizualizację wykonywania zadania. Jeśli w przypadku wykonywania [ParallelTasks] [ parallel_tasks_sample] przykładowej aplikacji, można użyć funkcji Mapa cieplna można łatwo przedstawić wizualnie wykonywanie zadań równoległych w każdym węźle.


[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchLabs/
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

