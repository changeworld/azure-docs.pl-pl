---
title: Zależności zadań umożliwiają uruchamianie zadań w oparciu o ukończenia innych zadań — Azure Batch | Dokumentacja firmy Microsoft
description: Utwórz zadania, które są zależne od ukończenia innych zadań przetwarzania styl MapReduce i podobne dane big Data obciążeń w usłudze Azure Batch.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca6918b809a9b4ede3fffb151c7fa5183ae03b47
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60550379"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Tworzenie zależności zadań podrzędnych do uruchomienia zadania, które są zależne od innych zadań

Można zdefiniować zależności zadań podrzędnych do uruchomienia zadania lub zestawu zadań, tylko wtedy, gdy zadanie nadrzędne zostało zakończone. Sytuacje, w którym współzależności zadań są przydatne to:

* Styl MapReduce obciążeń w chmurze.
* Zadania, których zadania przetwarzania danych mogą być wyrażone jako skierowanym grafie acyklicznym (DAG).
* Renderowanie przed i po renderowanie procesy gdy każde zadanie należy wykonać przed rozpoczęciem następnego zadania.
* Każde inne zadanie, w którym zadania podrzędne są zależne od danych wyjściowych zadania nadrzędnego.

Zależności zadań podrzędnych usługi Batch można utworzyć zadania, które są zaplanowane do wykonania w węzłach obliczeniowych, po zakończeniu co najmniej jednego zadania nadrzędnego. Na przykład można utworzyć zadanie, które renderuje każdej ramce 3D filmu przy użyciu oddzielnych zadań równoległych. Końcowe zadań — "merge task"--scalenia wyrenderowane ramki w filmie ukończone tylko wtedy, gdy wszystkie ramki zostało pomyślnie wyrenderowane.

Domyślnie zadania zależne są zaplanowane do wykonania tylko wtedy, gdy zadanie nadrzędne zostało pomyślnie zakończone. Można określić akcję zależności, aby zastąpić domyślne zachowanie i uruchamiania zadań, gdy zadanie nadrzędne nie powiodło się. Zobacz [akcje zależności](#dependency-actions) sekcji, aby uzyskać szczegółowe informacje.  

Można tworzyć zadania, które są zależne od innych zadań w relacji jeden do jednego lub jeden do wielu. Można również utworzyć zależności zakresu, w których zadanie zależy od ukończenia grupy zadań w ramach określonego zakresu identyfikatorów zadań. Można połączyć te trzy podstawowe scenariusze w celu utworzenia relacji wiele do wielu.

## <a name="task-dependencies-with-batch-net"></a>Zależności zadań podrzędnych przy użyciu platformy .NET usługi Batch
W tym artykule omówiono sposób konfigurowania zależności zadań podrzędnych za pomocą [platformy .NET usługi Batch] [ net_msdn] biblioteki. Najpierw pokazujemy, jak do [Włącz zależności zadań](#enable-task-dependencies) na Twoje zadania i następnie pokazują, jak [konfigurowania zadania z zależnościami](#create-dependent-tasks). Opisano również sposób określić akcję zależności do uruchomienia zadania zależne, jeśli element nadrzędny zakończy się niepowodzeniem. Na koniec omówimy [scenariuszy zależności](#dependency-scenarios) , usługa Batch obsługuje.

## <a name="enable-task-dependencies"></a>Włącz zależności zadań podrzędnych
Aby użyć zależności zadań podrzędnych w aplikacji usługi Batch, należy najpierw skonfigurować zadania, aby używały zależności zadań. Na platformie .NET usługi Batch, należy włączyć ją w swojej [CloudJob] [ net_cloudjob] , ustawiając jego [UsesTaskDependencies] [ net_usestaskdependencies] właściwości `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

W poprzednim fragmencie kodu "klienta" to wystąpienie [BatchClient] [ net_batchclient] klasy.

## <a name="create-dependent-tasks"></a>Tworzenie zadania zależne
Aby utworzyć zadanie, który zależy od ukończenia co najmniej jedno zadanie nadrzędne, można określić, że zadanie "zależy od" inne zadania. Na platformie .NET usługi Batch, należy skonfigurować [CloudTask][net_cloudtask].[ DependsOn] [ net_dependson] właściwości z wystąpieniem [TaskDependencies] [ net_taskdependencies] klasy:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Ten fragment kodu tworzy zależne zadania o identyfikatorze zadania "Kwiatów". Zadanie "Kwiatów" zależy od zadania, "Rain" i "Sun". Zadanie "kwiatów" zostanie zaplanowane do uruchomienia w węźle obliczeniowym dopiero po zadania, które "Rain" i "Sun" została ukończona pomyślnie.

> [!NOTE]
> Domyślnie zadanie jest uznawane za zostanie ukończona pomyślnie, gdy jest on w **ukończone** stanu i jego **kod zakończenia** jest `0`. Na platformie .NET usługi Batch, oznacza to, [CloudTask][net_cloudtask].[ Stan] [ net_taskstate] wartość właściwości `Completed` i CloudTask [TaskExecutionInformation][net_taskexecutioninformation].[ ExitCode] [ net_exitcode] wartość właściwości jest `0`. Jak zmienić, zobacz [akcje zależności](#dependency-actions) sekcji.
> 
> 

## <a name="dependency-scenarios"></a>Scenariusze zależności
Istnieją trzy scenariusze zależności podstawowe zadania, które można użyć w usłudze Azure Batch: jeden do jednego, jeden do wielu i identyfikatorze zadania zakresu zależności. Te można łączyć zapewnienie czwarty scenariuszu wiele do wielu.

| Scenariusz&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Przykład |  |
|:---:| --- | --- |
|  [One-to-one](#one-to-one) |*Zadania_podrzędnego_b* zależy od *Zadaniaa* <p/> *Zadaniab* nie można zaplanować wykonywanie aż do *Zadaniaa* została pomyślnie zakończona |![Diagram: jeden do jednego zadania zależności][1] |
|  [Jeden do wielu](#one-to-many) |*zadanieC* zależy od *zadaniaA* i *zadaniaB* <p/> *Zadanie_podrzędne_c* nie można zaplanować wykonywanie przed zakończeniem *Zadania_podrzędnego_a* i *Zadania_podrzędnego_b* zostały ukończone pomyślnie |![Diagram: jeden do wielu zadań zależności][2] |
|  [Zakres Identyfikatora zadania](#task-id-range) |*Zadanie_podrzędne_d* zależy od zakresu zadań podrzędnych <p/> *Zadanie_podrzędne_d* nie można zaplanować wykonywanie aż do zadania za pomocą identyfikatorów *1* za pośrednictwem *10* zostały ukończone pomyślnie |![Diagram: Zależność zakresu identyfikator zadania][3] |

> [!TIP]
> Możesz utworzyć **wiele do wielu** relacje, takie jak gdzie zadania C, D, E i f. każdego zależą od zadania, A i B. Jest to przydatne, na przykład w przypadku równoległego przetwarzania wstępnego scenariuszy, w którym swoje zadania podrzędne są zależne od danych wyjściowych wielu zadań nadrzędnych.
> 
> W przykładach w tej sekcji zależne zadanie jest uruchamiane tylko wtedy, gdy pomyślnego wykonania zadania nadrzędnego. To zachowanie jest zachowaniem domyślnym zadanie zależne. Po niepowodzeniu zadania nadrzędnego, określając akcji zależności, aby zastąpić domyślne zachowanie, możesz uruchomić zadanie zależne. Zobacz [akcje zależności](#dependency-actions) sekcji, aby uzyskać szczegółowe informacje.

### <a name="one-to-one"></a>Jeden do jednego
W relacji jeden do jednego zadania zależy od pomyślnego zakończenia zadania jedną jednostkę nadrzędną. Aby utworzyć zależność, należy podać identyfikator pojedynczego zadania, aby [TaskDependencies][net_taskdependencies].[ OnId] [ net_onid] metody statycznej podczas wypełniania [DependsOn] [ net_dependson] właściwość [CloudTask] [ net_cloudtask].

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
W relacji jeden do wielu zadań zależy od ukończenia wielu zadaniom nadrzędnym. Aby utworzyć zależność, należy podać zbiór identyfikatorów zadań [TaskDependencies][net_taskdependencies].[ OnIds] [ net_onids] metody statycznej podczas wypełniania [DependsOn] [ net_dependson] właściwość [CloudTask] [ net_cloudtask].

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

### <a name="task-id-range"></a>Zakres Identyfikatora zadania
W zależności na szeroką gamę zadaniom nadrzędnym zadania zależy od ukończenia zadań, których identyfikatory leży w zakresie.
Aby utworzyć zależność, podaj pierwsze i ostatnie identyfikatorów zadań w zakresie do [TaskDependencies][net_taskdependencies].[ OnIdRange] [ net_onidrange] metody statycznej podczas wypełniania [DependsOn] [ net_dependson] właściwość [CloudTask] [ net_cloudtask].

> [!IMPORTANT]
> Użycie zakresów identyfikator zadania dla zależności, zostanie wybrany tylko zadania z identyfikatorami reprezentujących wartości liczby całkowitej przez zakres. Dlatego zakres `1..10` wybierze zadania `3` i `7`, ale nie `5flamingoes`. 
> 
> Zer wiodących nie są istotne podczas obliczania zakresu zależności, aby zadania za pomocą ciągu identyfikatorów `4`, `04` i `004` będzie *w ramach* zakres i ich wszystkie traktowane jak zadanie `4`, więc pierwszy z nich do ukończenia będą spełniały warunki zależności.
> 
> Każde zadanie w zakresie muszą spełniać zależności, pomyślne zakończenie działania lub wykonując za pomocą błędu, który jest mapowany na ustawienie akcji zależności **Satisfy**. Zobacz [akcje zależności](#dependency-actions) sekcji, aby uzyskać szczegółowe informacje.
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

## <a name="dependency-actions"></a>Zależność działania

Domyślnie zadanie zależne lub zestawu zadań jest uruchamiana tylko wtedy, gdy zadanie nadrzędne zostało pomyślnie zakończone. W niektórych scenariuszach może być uruchomienie zadania zależne, nawet, jeśli zadanie nadrzędne nie powiedzie się. Zachowanie domyślne można przesłonić, określając akcji zależności. Akcji zależności Określa, czy zadanie zależne, kwalifikują się do uruchomienia, oparte na powodzenie lub niepowodzenie zadania nadrzędnego. 

Na przykład załóżmy, że zadanie zależne oczekuje na dane z zakończenia zadania nadrzędnego. Jeśli zadanie nadrzędne nie powiedzie się, zadanie zależne nadal można przeprowadzić przy użyciu starszych danych. W tym przypadku akcja zależności można określić, że zadanie zależne kwalifikuje się do uruchomienia pomimo awarii zadania nadrzędnego.

Akcja zależności opiera się na warunek wyjścia dla zadania nadrzędnego. Można określić akcję zależności dla żadnego z następujących warunków zakończenia; dla platformy .NET, zobacz [ExitConditions] [ net_exitconditions] klasy, aby uzyskać szczegółowe informacje:

- Gdy wystąpi błąd przetwarzania wstępnego.
- Gdy wystąpi błąd przekazywania plików. Jeśli zadanie kończy działanie z kodem zakończenia, który został określony za pomocą **exitCodes** lub **exitCodeRanges**, a następnie napotka błąd, akcji określonej przez kod zakończenia przekazywania pliku ma pierwszeństwo.
- Gdy zadanie kończy działanie z kodem zakończenia zdefiniowane przez **ExitCodes** właściwości.
- Gdy zadanie kończy działanie z kodem zakończenia, który mieści się w zakresie określonym przez **ExitCodeRanges** właściwości.
- Przypadek domyślny, jeśli zadanie kończy działanie z kodem zakończenia nie jest zdefiniowany przez **ExitCodes** lub **ExitCodeRanges**, czy zadanie kończy działanie z błędem przetwarzania wstępnego i **PreProcessingError** właściwość nie jest ustawiona, błąd przekazywania lub zadanie nie powiedzie się z plikiem i **FileUploadError** nie ustawiono właściwości. 

Aby określić akcję zależności na platformie .NET, należy ustawić [ExitOptions][net_exitoptions].[ DependencyAction] [ net_dependencyaction] właściwość warunek wyjścia. **DependencyAction** właściwości ma jedną z dwóch wartości:

- Ustawienie **DependencyAction** właściwości **Satisfy** oznacza, że zadania zależne są uprawnieni do uruchomienia, jeśli zadanie nadrzędne kończy pracę z określonym błędem.
- Ustawienie **DependencyAction** właściwości **bloku** wskazuje, że zadania zależne nie kwalifikują się do uruchomienia.

Domyślne ustawienie dla **DependencyAction** właściwość jest **Satisfy** kod zakończenia 0, a **bloku** dla wszystkich pozostałych warunków zakończenia.

Poniższy kod ustawia fragment **DependencyAction** właściwość dla zadania nadrzędnego. Jeśli zadanie nadrzędne kończy się błąd przetwarzania wstępnego, ani wymienione kody błędów, zadanie zależne jest zablokowany. Jeśli zadanie nadrzędne kończy pracę z innymi błędu różny od zera, zadanie zależne kwalifikuje się do uruchomienia.

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
[TaskDependencies] [ github_taskdependencies] przykładowy projekt jest jednym z [przykładów kodu usługi Azure Batch] [ github_samples] w witrynie GitHub. To rozwiązanie programu Visual Studio pokazuje:

- Jak włączyć zależności zadania dla zadania
- Sposób tworzenia zadania, które są zależne od innych zadań
- Jak wykonać te zadania w puli węzłów obliczeniowych.

## <a name="next-steps"></a>Kolejne kroki
### <a name="application-deployment"></a>Wdrażanie aplikacji
[Pakiety aplikacji](batch-application-packages.md) funkcji usługi Batch zapewnia prosty sposób na wartość oba wdrożenia i wersji aplikacji uruchamianych przez zadania podrzędne w węzłach obliczeniowych.

### <a name="installing-applications-and-staging-data"></a>Instaluje aplikacje i przemieszcza dane
Zobacz [instaluje aplikacje i przemieszcza dane w usłudze Batch węzłów obliczeniowych] [ forum_post] na forum usługi Azure Batch omówienie metod w celu przygotowania węzłów do uruchamiania zadań. Zapisane przez jeden z członków zespołu usługi Azure Batch, ten wpis jest dobre podstawy na różne sposoby, aby skopiować aplikacje, dane wejściowe zadania i inne pliki do węzłów obliczeniowych.

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
[net_dependencyaction]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
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
