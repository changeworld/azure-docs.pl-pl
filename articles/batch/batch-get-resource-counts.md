---
title: Liczba stanów zadań i węzłów — Azure Batch | Dokumentacja firmy Microsoft
description: Liczba stanu zadania usługi Azure Batch i obliczeń węzły, które ułatwiają zarządzanie i monitorowanie rozwiązań usługi Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 09/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 574cdea61a474dda5d20254bfae9ff2f06044cca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60775376"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Monitorowanie rozwiązań usługi Batch przez liczenie zadania i węzły według stanu

Aby monitorować i zarządzać nimi na dużą skalę rozwiązania usługi Azure Batch, należy dokładne liczby zasobów w różnych stanach. Usługa Azure Batch umożliwia wydajne operacje można pobrać tych liczb dla usługi Batch *zadania* i *węzłów obliczeniowych*. Użyj tych operacji, zamiast zapytań listy potencjalnie czasochłonne, które zwracają szczegółowe informacje o dużych kolekcjach zadań lub węzłów.

* [Pobieranie liczby zadań] [ rest_get_task_counts] pobiera zagregowana liczba aktywnych, uruchamianie i zakończonych zadań w ramach zadania i zadania, które zakończonych powodzeniem lub niepowodzeniem. 

  Przez zliczanie zadań z poszczególnymi stanami, można łatwiej wyświetlić postęp zadania dla użytkownika lub wykrywania nieoczekiwane opóźnienia lub błędy, które mogą wpłynąć na zadania. Liczba zadań Get jest dostępna od interfejsu API usługi Batch w wersji 2017-06-01.5.1 i powiązanych zestawów SDK i narzędzi.

* [Lista liczby węzłów w puli] [ rest_get_node_counts] pobiera liczbę dedykowanych i o niskim priorytecie węzłów obliczeniowych w każdej puli znajdujące się w różnych stanach: tworzenie, bezczynności w trybie offline, przerywane, ponowny rozruch, odtwarzanie z obrazu, uruchamiania i innych. 

  Przez liczenia węzłów z poszczególnymi stanami, można określić, kiedy masz zasoby obliczeniowe odpowiednie do uruchamiania zadań oraz identyfikowanie potencjalnych problemów z puli. Lista puli węzeł zlicza jest dostępna od interfejsu API usługi Batch w wersji 2018-03-01.6.1 i powiązanych zestawów SDK i narzędzi.

Jeśli używasz wersji usługi, która nie obsługuje operacje liczba węzłów typu Liczba zadań, należy użyć zapytania o listę, zliczania tych zasobów. Również użyć zapytania o listę, aby uzyskać informacje dotyczące innych zasobów usługi Batch, takich jak aplikacje, pliki i zadań. Aby uzyskać więcej informacji na temat stosowania filtrów do zapytania dotyczące list zobacz [tworzenia zapytań, aby wyświetlić listę partii zasoby wydajnie](batch-efficient-list-queries.md).

## <a name="task-state-counts"></a>Liczby stanów zadań

Operacji Pobierz zadania jest liczona liczba zadań według następujących stanów:

- **Aktywne** — zadanie, które jest umieszczone w kolejce i mieć możliwość wykonywania, ale nie jest aktualnie przypisana do węzła obliczeniowego. Zadanie jest również `active` , gdy jest [zależne od zadania nadrzędnego](batch-task-dependencies.md) , nie została jeszcze zakończona. 
- **Uruchamianie** -zadań, która ma przypisane do węzła obliczeniowego, ale nie została jeszcze zakończona. Zadanie jest traktowana jako `running` po jego stan jest `preparing` lub `running`, wskazane przez [uzyskać informacje o zadaniu] [ rest_get_task] operacji.
- **Ukończono** — zadanie, które nie jest już kwalifikujących się do uruchomienia, ponieważ albo zakończyło się pomyślnie, lub zakończona niepomyślnie i również wyczerpany limit ponownych prób. 
- **Pomyślnie** — zadanie, którego wynikiem wykonania zadania jest `success`. Batch Określa, czy zadanie zakończyło się pomyślnie lub nie powiodła się sprawdzając `TaskExecutionResult` właściwość [executionInfo] [ rest_get_exec_info] właściwości.
- **Nie powiodło się** zadanie, którego wynikiem wykonania zadania jest `failure`.

Poniższy przykładowy kod .NET pokazuje, jak można pobrać liczby zadań według stanu: 

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

Można użyć podobny wzorzec pozostałym i innych obsługiwanych języków można pobrać liczby zadań dla zadania. 

> [!NOTE]
> Batch wersji interfejsu API usługi przed 2018-08-01.7.0 również zwracać `validationStatus` właściwości w odpowiedzi na pobieranie liczby zadań. Ta właściwość wskazuje, czy usługi Batch wyewidencjonować liczby stanów w celu zachowania spójności ze Stanami zgłoszone w interfejsie API listy zadań. Wartość `validated` wskazuje tylko, że usługi Batch zaznaczone pole wyboru w celu zachowania spójności w co najmniej raz dla zadania. Wartość `validationStatus` właściwości nie wskazuje, czy liczby elementów zwracanych pobieranie liczby zadań są obecnie aktualna.
>

## <a name="node-state-counts"></a>Liczby stanów węzłów

Liczby operacji listy puli węzeł zlicza obliczeń węzły według następujących stanów w każdej puli. Oddzielne łącznej liczby są dostarczane dla węzły dedykowane oraz węzły o niskim priorytecie w każdej puli.

- **Tworzenie** -przydzielone Azure maszynę Wirtualną, która nie została jeszcze uruchomiona do dołączenia do puli.
- **Bezczynne** -węzła obliczeniowego dostępne, które nie jest aktualnie uruchomione zadanie.
- **LeavingPool** -węzeł, który opuszcza puli, ponieważ jawnie usunąć użytkownika lub puli jest zmianą rozmiaru lub skalowanie automatyczne w dół.
- **W trybie offline** -węzeł tej partii nie można użyć do Planuj nowych zadań.
- **Przerywane** -węzeł o niskim priorytecie, który został usunięty z puli, ponieważ Azure odzyskać maszynę Wirtualną. A `preempted` węzła może zostać zainicjowane ponownie po udostępnieniu pojemności maszyn wirtualnych o niskim priorytecie zastąpienia.
- **Ponowne uruchamianie** -węzeł, na którym jest uruchamiana ponownie.
- **Odtwarzanie z obrazu** -węzeł, na którym jest on ponowna instalacja systemu operacyjnego.
- **Uruchamianie** -węzeł, na którym jest uruchomione co najmniej jedno zadanie (innych niż zadania uruchamiania).
- **Uruchamianie** -węzeł, na którym jest uruchamiana usługa partia zadań. 
- **StartTaskFailed** -węzeł, na którym [zadanie podrzędne uruchamiania] [ rest_start_task] nie powiodło się i wyczerpania wszystkich ponownych prób, na którym `waitForSuccess` jest ustawiona na zadanie podrzędne uruchamiania. Węzeł nie jest używany do uruchamiania zadań.
- **Nieznany** — węzeł, utraciło łączność z usługą Batch i którego stan nie jest znana.
- **Korzystanie z niej** -węzła, którego nie można użyć do wykonania zadania podrzędnego z powodu błędów.
- **WaitingForStartTask** -węzeł, na którym zadanie podrzędne uruchamiania uruchomienia, ale `waitForSuccess` jest ustawiony, początek zadanie nie zostało ukończone.

Poniższy fragment kodu języka C# ilustruje sposób wyświetlenia listy liczby węzłów dla wszystkich pul w ramach bieżącego konta:

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts())
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
Poniższy fragment kodu języka C# ilustruje sposób wyświetlenia listy liczby węzłów dla danej puli dla konta bieżącego.

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts(new ODATADetailLevel(filterClause: "poolId eq 'testpool'")))
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
Można użyć podobny wzorzec pozostałym i innych obsługiwanych języków można pobrać liczby węzłów dla pul.
 
## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej o zasadach działania i funkcjach usługi Batch, zobacz temat [Omówienie funkcji usługi Batch](batch-api-basics.md). Artykuł w tym artykule omówiono podstawowe zasoby usługi Batch, takie jak pule, węzły obliczeniowe, zadania i zadania i zawiera omówienie funkcji usługi.

* Aby dowiedzieć się, jak zastosowanie filtrów zapytania, które wyświetla listę zasobów usługi Batch, zobacz [tworzenia zapytań, aby wyświetlić listę partii zasoby wydajnie](batch-efficient-list-queries.md).


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

