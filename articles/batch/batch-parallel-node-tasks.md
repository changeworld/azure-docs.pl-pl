---
title: Równoległe uruchamianie zadań w celu optymalizacji zasobów obliczeniowych — usługa Azure Batch
description: Zwiększ wydajność i obniż koszty, korzystając z mniejszej liczby węzłów obliczeniowych i uruchamiając równoczesne zadania w każdym węźle w puli usługi Azure Batch
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023637"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Uruchamianie zadań jednocześnie w celu maksymalizacji użycia węzłów obliczeniowych wsadowych 

Uruchamiając więcej niż jedno zadanie jednocześnie w każdym węźle obliczeniowym w puli usługi Azure Batch, można zmaksymalizować użycie zasobów na mniejszą liczbę węzłów w puli. W przypadku niektórych obciążeń może to spowodować skrócenie czasu pracy i niższe koszty.

Podczas gdy niektóre scenariusze korzystają z poświęcania wszystkich zasobów węzła do jednego zadania, kilka sytuacji korzyści z zezwalania na wiele zadań do udostępniania tych zasobów:

* **Minimalizowanie transferu danych,** gdy zadania są w stanie udostępniać dane. W tym scenariuszu można znacznie zmniejszyć opłaty za transfer danych, kopiując udostępnione dane do mniejszej liczby węzłów i wykonując zadania równolegle w każdym węźle. Dotyczy to w szczególności, jeśli dane, które mają być kopiowane do każdego węzła muszą być przesyłane między regionami geograficznymi.
* **Maksymalizacja użycia pamięci,** gdy zadania wymagają dużej ilości pamięci, ale tylko w krótkich okresach czasu i w zmiennych godzinach podczas wykonywania. Można zastosować mniej, ale większe węzły obliczeniowe z większą ilością pamięci, aby skutecznie obsługiwać takie skoki. Te węzły będą miały wiele zadań uruchomionych równolegle w każdym węźle, ale każde zadanie będzie korzystać z wielu węzłów pamięci w różnym czasie.
* **Ograniczanie limitów numer węzła,** gdy komunikacja między węzłami jest wymagana w puli. Obecnie pule skonfigurowane do komunikacji między węzłami są ograniczone do 50 węzłów obliczeniowych. Jeśli każdy węzeł w takiej puli jest w stanie wykonywać zadania równolegle, większa liczba zadań może być wykonywana jednocześnie.
* **Replikowanie lokalnego klastra obliczeniowego, na**przykład podczas pierwszego przenoszenia środowiska obliczeniowego na platformę Azure. Jeśli bieżące rozwiązanie lokalne wykonuje wiele zadań na węzeł obliczeniowy, można zwiększyć maksymalną liczbę zadań węzła, aby dokładniej dublować tę konfigurację.

## <a name="example-scenario"></a>Przykładowy scenariusz
Jako przykład, aby zilustrować korzyści z równoległego wykonywania zadań, załóżmy, że aplikacja zadania ma wymagania dotyczące procesora CPU i pamięci, takie, że [standardowe\_](../cloud-services/cloud-services-sizes-specs.md) węzły D1 są wystarczające. Jednak aby zakończyć zadanie w wymaganym czasie, potrzebnych jest 1000 tych węzłów.

Zamiast używać standardowych\_węzłów D1, które mają 1 rdzeń procesora, można użyć standardowych [\_węzłów D14,](../cloud-services/cloud-services-sizes-specs.md) które mają po 16 rdzeni, i włączyć równoległe wykonywanie zadań. W związku z tym *16 razy mniej węzłów* może być używany - zamiast 1000 węzłów, tylko 63 będzie wymagane. Ponadto jeśli duże pliki aplikacji lub dane referencyjne są wymagane dla każdego węzła, czas trwania zadania i wydajność są ponownie lepsze, ponieważ dane są kopiowane tylko do 63 węzłów.

## <a name="enable-parallel-task-execution"></a>Włączanie równoległego wykonywania zadań
Węzły obliczeniowe można skonfigurować do równoległego wykonywania zadań na poziomie puli. Za pomocą biblioteki Batch .NET ustaw właściwość [CloudPool.MaxTasksPerComputeNode][maxtasks_net] podczas tworzenia puli. Jeśli używasz interfejsu API REST partii, ustaw [maxTasksPerNode][rest_addpool] element w treści żądania podczas tworzenia puli.

Usługa Azure Batch umożliwia ustawienie zadań na węzeł do (4x) liczby węzłów podstawowych. Na przykład jeśli pula jest skonfigurowana z węzłami o rozmiarze `maxTasksPerNode` "Duże" (cztery rdzenie), może być ustawiona na 16. Jednak niezależnie od liczby rdzeni węzła nie można mieć więcej niż 256 zadań na węzeł. Aby uzyskać szczegółowe informacje na temat liczby rdzeni dla każdego rozmiaru węzłów, zobacz [Rozmiary usług w chmurze](../cloud-services/cloud-services-sizes-specs.md). Aby uzyskać więcej informacji na temat limitów usług, zobacz [Przydziały i limity dla usługi Azure Batch](batch-quota-limit.md).

> [!TIP]
> Pamiętaj, aby wziąć `maxTasksPerNode` pod uwagę wartość podczas konstruowania [formuły skalowania automatycznego][enable_autoscaling] dla puli. Na przykład formuła, `$RunningTasks` która ocenia może mieć dramatyczny wpływ na wzrost zadań na węzeł. Aby uzyskać więcej informacji, zobacz [Automatyczne skalowanie węzłów obliczeniowych w puli usługi Azure Batch.](batch-automatic-scaling.md)
>
>

## <a name="distribution-of-tasks"></a>Podział zadań
Gdy węzły obliczeniowe w puli można wykonywać zadania jednocześnie, ważne jest, aby określić, jak mają być dystrybuowane między węzłami w puli.

Za pomocą [CloudPool.TaskSchedulingPolicy][task_schedule] właściwości, można określić, że zadania powinny być przypisane równomiernie we wszystkich węzłach w puli ("rozprzestrzenianie"). Lub można określić, że jak najwięcej zadań, jak to możliwe, powinny być przypisane do każdego węzła przed zadania są przypisane do innego węzła w puli ("pakowanie").

Jako przykład tego, jak ta funkcja jest cenne, należy wziąć pod uwagę pulę [standardowych\_](../cloud-services/cloud-services-sizes-specs.md) węzłów D14 (w powyższym przykładzie), który jest skonfigurowany z [CloudPool.MaxTasksPerComputeNode][maxtasks_net] wartość 16. Jeśli [CloudPool.TaskSchedulingPolicy][task_schedule] jest skonfigurowany z [ComputeNodeFillType][fill_type] *pakietu*Pack , to zmaksymalizować użycie wszystkich 16 rdzeni każdego węzła i umożliwić [puli skalowania automatycznego](batch-automatic-scaling.md) przycinać nieużywane węzły z puli (węzły bez żadnych zadań przypisanych). Minimalizuje to zużycie zasobów i oszczędza pieniądze.

## <a name="batch-net-example"></a>Przykład wsadowy .NET
Ten fragment kodu interfejsu API [usługi Batch .NET][api_net] zawiera żądanie utworzenia puli zawierającej cztery węzły z maksymalnie czterema zadaniami na węzeł. Określa zasadę planowania zadań, która wypełni każdy węzeł zadaniami przed przypisaniem zadań do innego węzła w puli. Aby uzyskać więcej informacji na temat dodawania pul przy użyciu interfejsu API usługi Batch .NET, zobacz [BatchClient.PoolOperations.CreatePool][poolcreate_net].

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

## <a name="batch-rest-example"></a>Przykład REST wsadowy
Ten fragment interfejsu API [rest partii][api_rest] pokazuje żądanie utworzenia puli, która zawiera dwa duże węzły z maksymalnie czterech zadań na węzeł. Aby uzyskać więcej informacji na temat dodawania pul przy użyciu interfejsu API REST, zobacz [Dodawanie puli do konta][rest_addpool].

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
> Można ustawić `maxTasksPerNode` element i [MaxTasksPerComputeNode][maxtasks_net] właściwości tylko w czasie tworzenia puli. Nie można ich modyfikować po utworzeniu puli.
>
>

## <a name="code-sample"></a>Przykład kodu
[ParallelNodeTasks][parallel_tasks_sample] projektu na GitHub ilustruje użycie [CloudPool.MaxTasksPerComputeNode][maxtasks_net] właściwości.

Ta aplikacja konsoli języka C# używa biblioteki [Batch .NET][api_net] do utworzenia puli z co najmniej jednym węzłem obliczeniowym. Wykonuje konfigurowalną liczbę zadań w tych węzłach, aby symulować obciążenie zmiennej. Dane wyjściowe z aplikacji określa, które węzły wykonywane każde zadanie. Aplikacja zawiera również podsumowanie parametrów zadania i czasu trwania. Poniżej znajduje się część sumaryczne danych wyjściowych z dwóch różnych przebiegów przykładowej aplikacji.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

Pierwsze wykonanie przykładowej aplikacji pokazuje, że z jednego węzła w puli i domyślne ustawienie jednego zadania na węzeł, czas trwania zadania jest ponad 30 minut.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

Drugi przebieg próbki pokazuje znaczny spadek czasu trwania zadania. Dzieje się tak, ponieważ pula została skonfigurowana z czterema zadaniami na węzeł, co pozwala na równoległe wykonywanie zadań w celu wykonania zadania w prawie jednej czwartej czasu.

> [!NOTE]
> Czas trwania zadania w powyższych podsumowaniach nie obejmuje czasu tworzenia puli. Każde z powyższych zadań zostało przesłane do wcześniej utworzonych pul, których węzły obliczeniowe były w stanie *bezczynności* w czasie przesyłania.
>
>

## <a name="next-steps"></a>Następne kroki
### <a name="batch-explorer-heat-map"></a>Mapa cieplna eksploratora partii
[Wytwórcy partii][batch_labs] to bezpłatne, bogate, funkcjonalne, autonomiczne narzędzie klienta ułatwiające tworzenie, debugowanie i monitorowanie aplikacji usługi Azure Batch. Eksplorator wsadowy zawiera funkcję *Mapy cieplnej,* która umożliwia wizualizację wykonywania zadań. Podczas wykonywania [paralleltasks][parallel_tasks_sample] przykładowej aplikacji, można użyć funkcji Mapy ciepła, aby łatwo wizualizować wykonywanie równoległych zadań w każdym węźle.


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

