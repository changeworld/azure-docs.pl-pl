---
title: Użyj zależności zadań do uruchomienia zadania po zakończeniu innych zadań — partii zadań Azure | Dokumentacja firmy Microsoft
description: Utwórz zadania, które są zależne od ukończenia innych zadań przetwarzania stylu MapReduce i podobnych danych big data obciążeń w partii zadań Azure.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ba85e075c39251b0b3d7c4b8bc3f8d53a1afadf7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
ms.locfileid: "30316821"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Tworzenie zależności zadań do wykonywania zadań, które są zależne od innych zadań

Można określić zależności zadań do uruchomienia zadania lub zestawu zadań dopiero po zakończeniu zadania nadrzędnego. Sytuacje, w którym zależności zadań są przydatne obejmują:

* Styl MapReduce obciążeń w chmurze.
* Zadania, których zadania przetwarzania danych może zostać wyrażona jako ukierunkowanego wykresu acyklicznego (DAG).
* Procesy renderowania wstępnego i renderowania po, gdzie każde zadanie należy wykonać przed rozpoczęciem następnego zadania.
* Inne zadanie w którym zadania podrzędne są zależne od dane wyjściowe zadania nadrzędnego.

Zależności zadań wsadowych umożliwia tworzenie zadań, które są zaplanowane do uruchomienia na węzły obliczeniowe po zakończeniu jednego lub więcej zadań nadrzędnej. Na przykład można utworzyć zadania, który renderuje każdej ramce 3D film z oddzielnych zadań równoległych. Ostatni zadań — "merge zadanie"--scaleń renderowanych ramki do ukończenia filmu tylko wtedy, gdy wszystkie ramki zostały pomyślnie renderowane.

Domyślnie zadania zależne są zaplanowane do uruchomienia, dopiero po pomyślnym zakończeniu zadania nadrzędnego. Można określić akcję zależności, aby zastąpić domyślne zachowanie i uruchamianie zadań, gdy zadanie nadrzędne nie powiedzie się. Zobacz [akcje zależności](#dependency-actions) sekcji, aby uzyskać szczegółowe informacje.  

Można tworzyć zadania, które są zależne od innych zadań w relacji jeden do jednego lub jeden do wielu. Można również utworzyć zależność zakresu, gdzie zadania zależy od zakończenia grupy zadań w określonym zakresie identyfikatorów zadań. Można połączyć te trzy podstawowe scenariusze do tworzenia relacji wiele do wielu.

## <a name="task-dependencies-with-batch-net"></a>Zależności zadań z partiami platformy .NET
W tym artykule omówiono sposób konfigurowania zależności zadań za pomocą [partiami platformy .NET] [ net_msdn] biblioteki. Możemy najpierw przedstawia sposób do [włączyć zależności zadań](#enable-task-dependencies) na zadaniach, a następnie pokazują, jak [skonfigurować zadania z zależnościami](#create-dependent-tasks). Opisano również sposób określić akcję zależności do uruchomienia zadania zależne, jeśli element nadrzędny nie powiedzie się. Ponadto omówiono [scenariusze zależności](#dependency-scenarios) obsługującego partii.

## <a name="enable-task-dependencies"></a>Włącz zależności zadań
Aby użyć zależności zadań w partii aplikacji, najpierw należy skonfigurować zadania do użycia zależności zadań. W partiami platformy .NET, należy włączyć ją w Twojej [CloudJob] [ net_cloudjob] przez ustawienie jej [UsesTaskDependencies] [ net_usestaskdependencies] właściwości `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

W poprzednim fragmencie kodu, "batchClient" jest wystąpieniem [BatchClient] [ net_batchclient] klasy.

## <a name="create-dependent-tasks"></a>Utwórz zadania zależne
Aby utworzyć zadanie, które jest zależna od zakończenia jednego lub więcej zadań nadrzędnej, można określić, że zadanie "zależy od" innych zadań. W partiami platformy .NET, należy skonfigurować [CloudTask][net_cloudtask].[ DependsOn] [ net_dependson] właściwości z wystąpieniem [TaskDependencies] [ net_taskdependencies] klasy:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Następujący fragment kodu tworzy zależnego zadania o identyfikatorze zadania "Kwiaty". Zadanie "Kwiaty" zależy od zadania "Ustaniu" i "Sun". Zadanie "kwiaty" zostanie zaplanowane do uruchomienia w węźle obliczeń tylko po zadań, które "Ustaniu" i "Sun" została ukończona pomyślnie.

> [!NOTE]
> Zadanie jest uznawany za zostanie ukończona pomyślnie, gdy jest on w **ukończone** stanu i jego **kod zakończenia** jest `0`. W partiami platformy .NET, oznacza to [CloudTask][net_cloudtask].[ Stan] [ net_taskstate] wartość właściwości `Completed` i CloudTask [TaskExecutionInformation][net_taskexecutioninformation].[ ExitCode] [ net_exitcode] wartość właściwości jest `0`.
> 
> 

## <a name="dependency-scenarios"></a>Scenariusze zależności
Istnieją trzy scenariusze zależności podstawowe zadania, których można używać w partii zadań Azure: jeden do jednego, jeden do wielu, a zadania o identyfikatorze zakresu zależności. Te można łączyć zapewnienie scenariusza czwarty wiele do wielu.

| Scenariusz&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Przykład |  |
|:---:| --- | --- |
|  [One-to-one](#one-to-one) |*taskB* zależy od *taskA* <p/> *taskB* nie będzie można zaplanować wykonywanie do momentu *taskA* zostało ukończone pomyślnie |![Diagram: jeden do jednego zadania zależności][1] |
|  [Jeden do wielu](#one-to-many) |*zadanieC* zależy od *zadaniaA* i *zadaniaB* <p/> *taskC* nie zostanie zaplanowane do wykonania przed zakończeniem *taskA* i *taskB* zostały ukończone pomyślnie |![Diagram: jeden do wielu zadań zależności][2] |
|  [Zakres identyfikator zadania](#task-id-range) |*taskD* zależy od zakresu zadań <p/> *taskD* nie będzie można zaplanowane do uruchomienia aż do zadań z identyfikatorami *1* za pośrednictwem *10* zostały ukończone pomyślnie |![Diagram: Zadanie identyfikator zakresu zależności][3] |

> [!TIP]
> Można utworzyć **wiele do wielu** relacje, np. gdy zadania C, D E i F każdego zależą od zadania, A i B. Jest to przydatne, na przykład w zrównoleglone przetwarzania wstępnego scenariuszach, w którym zadania podrzędne są zależne od danych wyjściowych wielu zadań nadrzędnego.
> 
> W przykładach w tej sekcji zależne zadanie jest uruchamiane tylko wtedy, gdy pomyślnego wykonania zadania nadrzędnego. To zachowanie jest domyślne zachowanie dla zadania zależne. Po niepowodzeniu zadaniem nadrzędnym, określając akcji zależności, aby zastąpić zachowanie domyślne można uruchomić zadanie zależne. Zobacz [akcje zależności](#dependency-actions) sekcji, aby uzyskać szczegółowe informacje.

### <a name="one-to-one"></a>Jeden do jednego
W relacji jeden do jednego zadania zależy od pomyślne zakończenie zadania jednego zdarzenia nadrzędnego. Aby utworzyć zależność, podaj identyfikator pojedyncze zadanie [TaskDependencies][net_taskdependencies].[ OnId] [ net_onid] metody statycznej podczas wypełniania [DependsOn] [ net_dependson] właściwość [CloudTask][net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Jeden do wielu
W relacji jeden do wielu zadania zależy od ukończenia wielu zadań nadrzędnej. Aby utworzyć zależność, przedstawiają kolekcję zadań identyfikatorów [TaskDependencies][net_taskdependencies].[ OnIds] [ net_onids] metody statycznej podczas wypełniania [DependsOn] [ net_dependson] właściwość [CloudTask][net_cloudtask].

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
``` 

### <a name="task-id-range"></a>Zakres identyfikator zadania
Zależność od zakresu nadrzędnego zadania, zadania zależy od zakończenia zadania, których identyfikatory znajdują się w zakresie.
Aby utworzyć zależność, podaj pierwszy i ostatni identyfikatorów zadań w zakresie do [TaskDependencies][net_taskdependencies].[ OnIdRange] [ net_onidrange] metody statycznej podczas wypełniania [DependsOn] [ net_dependson] właściwość [CloudTask][net_cloudtask].

> [!IMPORTANT]
> Jeśli używasz zakresy identyfikatorów zadań dla zależności, identyfikatorów zadań w zakresie *musi* być liczbami w postaci ciągu wartości będące liczbami całkowitymi.
> 
> Każde zadanie w zakresie muszą spełniać zależności, wykonując pomyślnie lub wykonując z błąd, który jest mapowany na akcję zależności ustawioną **Satisfy**. Zobacz [akcje zależności](#dependency-actions) sekcji, aby uzyskać szczegółowe informacje.
>
>

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>Akcje zależności

Domyślnie zadanie zależne lub zestawu zadań działa tylko po pomyślnym zakończeniu zadania nadrzędnego. W niektórych scenariuszach może być uruchomienie zadania zależne, nawet jeśli zadaniem nadrzędnym nie powiedzie się. Domyślne zachowanie można zastąpić, określając akcji zależności. Akcja zależności Określa, czy zadanie zależne mogą być uruchamiane, oparte na powodzenie lub niepowodzenie zadaniem nadrzędnym. 

Na przykład załóżmy, że zadanie zależne oczekuje na dane z ukończenia zadania nadrzędnego. Jeśli poprzednie zadanie nie powiedzie się, zadanie zależne nadal można uruchomić przy użyciu starszych danych. W takim przypadku akcji zależności można określić, że zadanie zależne jest uprawniona do uruchamiania niezależnie od awarii zadaniem nadrzędnym.

Akcja zależności jest oparta na warunku wyjścia dla zadania nadrzędnego. Można określić akcję zależności dla żadnej z następujących warunków zakończenia; dla platformy .NET, zobacz [ExitConditions] [ net_exitconditions] klasy, aby uzyskać szczegółowe informacje:

- Po wystąpieniu błędu przetwarzania wstępnego.
- Po wystąpieniu błędu przekazywania plików. Jeśli zadanie kończy działanie z kodem zakończenia, która została określona za pomocą **exitCodes** lub **exitCodeRanges**, a następnie pierwszeństwo napotka błąd, akcji określonej przez kod zakończenia przekazywania pliku.
- Gdy zadanie kończy działanie z kodem zakończenia zdefiniowanych przez **ExitCodes** właściwości.
- Gdy zadanie kończy działanie z kodem zakończenia, która wykracza poza zakres określony przez **ExitCodeRanges** właściwości.
- W przypadku domyślnej, jeśli zadanie kończy działanie z kodem zakończenia nie jest zdefiniowany przez **ExitCodes** lub **ExitCodeRanges**, lub jeśli zadanie kończy działanie z błędem przetwarzania wstępnego i **PreProcessingError** właściwość nie jest ustawiona, lub jeśli zadanie nie powiedzie się z plikiem błąd przekazywania i **FileUploadError** nie ustawiono właściwości. 

Aby określić akcję zależności w programie .NET, należy ustawić [ExitOptions][net_exitoptions].[ DependencyAction] [ net_dependencyaction] właściwości warunku exit. **DependencyAction** właściwość przyjmuje jeden z dwóch wartości:

- Ustawienie **DependencyAction** właściwości **Satisfy** wskazuje, czy zadania zależne kwalifikują się do uruchomienia, jeśli zadaniem nadrzędnym kończy działanie z powodu określonego błędu.
- Ustawienie **DependencyAction** właściwości **bloku** wskazuje, czy zadania zależne nie będą kwalifikowały się do uruchomienia.

Ustawieniem domyślnym dla **DependencyAction** właściwość jest **Satisfy** dla kod zakończenia 0, a **bloku** dla wszystkich innych warunków zakończenia.

Poniższy kod ustawia fragment **DependencyAction** właściwości dla zadania nadrzędnego. Jeśli zadaniem nadrzędnym kończy działanie z błędem przetwarzania wstępnego lub z kodami określonego błędu zależnego zadania jest zablokowany. Jeśli zadaniem nadrzędnym kończy działanie z inny błąd inną niż zero, zadanie zależne nie kwalifikuje się do uruchomienia.

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>Przykład kodu
[TaskDependencies] [ github_taskdependencies] przykładowy projekt jest jednym z [przykłady kodu partii zadań Azure] [ github_samples] w witrynie GitHub. Przedstawiono to rozwiązanie Visual Studio:

- Jak włączyć zadania zależności w zadaniu
- Tworzenie zadań, które są zależne od innych zadań
- Jak wykonać te zadania w puli węzłów obliczeniowych.

## <a name="next-steps"></a>Kolejne kroki
### <a name="application-deployment"></a>Wdrażanie aplikacji
[Pakietów aplikacji](batch-application-packages.md) funkcji partii zapewnia prosty sposób zarówno, jak wdrożyć i wersja aplikacji, które wykonania zadań na węzłach obliczeniowych.

### <a name="installing-applications-and-staging-data"></a>Instalowanie aplikacji i danych na potrzeby przemieszczania
Zobacz [instalowania aplikacji i danych na partii przemieszczania węzły obliczeniowe] [ forum_post] na forum usługi partia zadań Azure Omówienie metody przygotowania węzły do uruchamiania zadań. Napisane przez jeden z członków zespołu partii zadań Azure, ten wpis jest dobrym Elementarz na różne sposoby, aby skopiować aplikacje, dane wejściowe zadania i inne pliki do węzłów obliczeniowych.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_exitconditions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitconditions
[net_exitoptions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_dependencyaction]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions#Microsoft_Azure_Batch_ExitOptions_DependencyAction
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagram: jeden do jednego zależności"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagram: jeden do wielu zależności"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagram: zadanie identyfikator zakresu zależności"
