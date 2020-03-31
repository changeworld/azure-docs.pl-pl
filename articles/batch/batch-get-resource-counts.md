---
title: Liczba stanów dla zadań i węzłów — usługa Azure Batch | Dokumenty firmy Microsoft
description: Zliczanie stanu zadań usługi Azure Batch i węzłów obliczeniowych, aby ułatwić zarządzanie rozwiązaniami wsadowymi i monitorowanie ich.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 09/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: a7b58e96918d26851812aa96c18043121c081e94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023926"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Monitorowanie rozwiązań wsadowych przez zliczanie zadań i węzłów według stanu

Aby monitorować rozwiązania azure batch na dużą skalę i zarządzać nimi, potrzebujesz dokładnej liczby zasobów w różnych stanach. Usługa Azure Batch zapewnia wydajne operacje w celu uzyskania tych zliczeń dla *zadań* wsadowych i *węzłów obliczeniowych.* Użyj tych operacji zamiast potencjalnie czasochłonnych kwerend listy, które zwracają szczegółowe informacje o dużych kolekcjach zadań lub węzłów.

* [Pobierz liczniki zadań][rest_get_task_counts] pobiera agregującą liczbę aktywnych, uruchomionych i ukończonych zadań w zadaniu i zadań, które zakończyły się powodzeniem lub niepowodzeniem. 

  Zliczając zadania w każdym stanie, można łatwiej wyświetlić postęp zadania dla użytkownika lub wykryć nieoczekiwane opóźnienia lub błędy, które mogą mieć wpływ na zadanie. Pobierz liczniki zadań jest dostępny od wersji interfejsu API usługi wsadowej 2017-06-01.5.1 i powiązanych zestawów SDK i narzędzi.

* [Liczba węzłów puli list][rest_get_node_counts] pobiera liczbę dedykowanych i niskiego priorytetu węzłów obliczeniowych w każdej puli, które znajdują się w różnych stanach: tworzenie, bezczynności, w trybie offline, wywłaszczone, ponowne uruchamianie, ponowne zakładanie, uruchamianie i inne. 

  Zliczając węzły w każdym stanie, można określić, kiedy masz odpowiednie zasoby obliczeniowe do uruchamiania zadań i zidentyfikować potencjalne problemy z pulami. Liczba węzłów puli list jest dostępna od wersji interfejsu API usługi wsadowej 2018-03-01.6.1 i powiązanych zestawów SDK i narzędzi.

Jeśli używasz wersji usługi, która nie obsługuje operacji liczby zadań lub liczby węzłów, użyj kwerendy listy, aby policzyć te zasoby. Użyj również kwerendy listy, aby uzyskać informacje o innych zasobach usługi Batch, takich jak aplikacje, pliki i zadania. Aby uzyskać więcej informacji na temat stosowania filtrów do kwerend listy, zobacz [Tworzenie kwerend, aby skutecznie wyświetlać zasoby partii .](batch-efficient-list-queries.md)

## <a name="task-state-counts"></a>Liczba stanu zadania

Operacja Pobierz liczbę zadań zlicza zadania według następujących stanów:

- **Aktywne** — zadanie, które jest w kolejce i można go uruchomić, ale nie jest obecnie przypisane do węzła obliczeniowego. Zadanie jest `active` również, jeśli jest [zależna od zadania nadrzędnego,](batch-task-dependencies.md) które nie zostało jeszcze ukończone. 
- **Uruchamianie** — zadanie, które zostało przypisane do węzła obliczeniowego, ale nie zostało jeszcze ukończone. Zadanie jest liczone `running` jako wtedy, `preparing` gdy `running`jego stan jest albo , jak wskazano w Pobierz informacje o operacji [zadania.][rest_get_task]
- **Ukończono** — zadanie, które nie kwalifikuje się już do uruchomienia, ponieważ zakończyło się pomyślnie lub zakończyło się niepowodzeniem, a także wyczerpało limit ponawiania. 
- **Powiodło się** — zadanie, którego `success`wynikiem wykonania zadania jest . Usługa Batch określa, czy zadanie zakończyło się pomyślnie, czy nie, sprawdzając `TaskExecutionResult` właściwość właściwości [executionInfo.][rest_get_exec_info]
- **Nie powiodło się** Zadanie, którego wynikiem wykonania `failure`zadania jest .

Poniższy przykład kodu .NET pokazuje, jak pobierać liczbę zadań według stanu: 

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

Można użyć podobnego wzorca dla REST i innych obsługiwanych języków, aby uzyskać liczbę zadań dla zadania. 

> [!NOTE]
> Wersje interfejsu API usługi wsadowej przed 2018-08-01.7.0 również zwrócić `validationStatus` właściwość w odpowiedzi Pobierz liczniki zadań. Ta właściwość wskazuje, czy usługa Batch sprawdzała liczbę stanów pod kątem spójności ze stanami zgłoszonymi w interfejsie API zadań listy. Wartość `validated` wskazuje tylko, że usługa Batch co najmniej raz sprawdzała spójność dla zadania. Wartość `validationStatus` właściwości nie wskazuje, czy liczniki zwracane liczby zadań są obecnie aktualne.
>

## <a name="node-state-counts"></a>Liczba stanu węzła

Operacja Liczba węzłów puli listy zlicza węzły obliczeniowe według następujących stanów w każdej puli. Oddzielne liczby agregacji są dostarczane dla dedykowanych węzłów i węzłów o niskim priorytecie w każdej puli.

- **Tworzenie** — maszyna wirtualna przydzielona do platformy Azure, która jeszcze nie rozpoczęła dołączania do puli.
- **Bezczynność** — dostępny węzeł obliczeniowy, który nie jest aktualnie uruchomiony zadanie.
- **LeavingPool** — węzeł, który opuszcza pulę, ponieważ użytkownik jawnie usunął go lub ponieważ pula jest zmiana rozmiaru lub skalowanie automatyczne w dół.
- **W trybie offline** — węzeł, którego usługa Batch nie może użyć do planowania nowych zadań.
- **Wywłaszczone** — węzeł o niskim priorytecie, który został usunięty z puli, ponieważ platforma Azure odzyskała maszynę wirtualną. Węzeł `preempted` może zostać ponownie zainicjowany, gdy dostępna jest zastępcza pojemność maszyny wirtualnej o niskim priorytecie.
- **Ponowne uruchamianie** — węzeł, który jest ponownie uruchamiany.
- **Reimaging** — węzeł, w którym system operacyjny jest ponownie instalowany.
- **Uruchomione** — węzeł, który uruchamia jedno lub więcej zadań (innych niż zadanie startowe).
- **Uruchamianie** — węzeł, w którym uruchamia się usługa Batch. 
- **StartTaskFailed** — węzeł, w którym [zadanie uruchamiania][rest_start_task] nie powiodło się `waitForSuccess` i wyczerpał wszystkie ponownych prób i na którym jest ustawiony na zadanie start. Węzeł nie nadaje się do uruchamiania zadań.
- **Nieznany** — węzeł, który utracił kontakt z usługą Batch i którego stan nie jest znany.
- **Bezużyteczny** — węzeł, który nie może być używany do wykonywania zadań z powodu błędów.
- **OczekiwanieForStartTask** — węzeł, w którym zadanie `waitForSuccess` uruchamiania rozpoczęło pracę, ale jest ustawione, a zadanie startowe nie zostało ukończone.

Poniższy fragment kodu języka C# pokazuje, jak wyświetlić listę zliczeń węzłów dla wszystkich pul na rachunku bieżącym:

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
Poniższy fragment kodu języka C# pokazuje, jak wyświetlić liczbę węzłów dla danej puli na rachunku bieżącym.

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
Można użyć podobnego wzorca dla REST i innych obsługiwanych języków, aby uzyskać liczbę węzłów dla pul.
 
## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o zasadach działania i funkcjach usługi Batch, zobacz temat [Omówienie funkcji usługi Batch](batch-api-basics.md). W tym artykule omówiono podstawowe zasoby usługi Batch, takie jak pule, węzły obliczeniowe, zadania i zadania, i zawiera omówienie funkcji usługi.

* Aby uzyskać informacje dotyczące stosowania filtrów do kwerend, które wyświetlają zasoby partii, zobacz [Tworzenie kwerend w celu efektywnego wystawiania zasobów partii](batch-efficient-list-queries.md).


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

