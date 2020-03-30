---
title: Tworzenie zależności zadań do uruchamiania zadań — usługa Azure Batch
description: Tworzenie zadań, które zależą od ukończenia innych zadań do przetwarzania mapReduce stylu i podobnych obciążeń dużych zbiorów danych w usłudze Azure Batch.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca771117e889afc8e143c4ca4626ab2d3bb4da2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022906"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Tworzenie zależności zadań w celu uruchamiania zadań zależnych od innych zadań

Zależności zadań można zdefiniować w celu uruchomienia zadania lub zestawu zadań tylko po zakończeniu zadania nadrzędnego. Niektóre scenariusze, w których zależności zadań są przydatne obejmują:

* MapReduce stylu obciążeń w chmurze.
* Zadania, których zadania przetwarzania danych mogą być wyrażone jako ukierunkowany wykres acykliczny (DAG).
* Procesy renderowania wstępnego i post-renderingu, w których każde zadanie musi zostać ukończone przed rozpoczęciem następnego zadania.
* Każde inne zadanie, w którym zadania podrzędne zależą od wyników zadań nadrzędnych.

Za pomocą zależności zadań usługi Batch można tworzyć zadania, które są zaplanowane do wykonania w węzłach obliczeniowych po zakończeniu jednego lub więcej zadań nadrzędnych. Można na przykład utworzyć zadanie, które renderuje każdą klatkę filmu 3D z oddzielnymi zadaniami równoległymi. Ostateczne zadanie — "zadanie scalania" - scala renderowane klatki z pełnym filmem dopiero po pomyślnym renderowaniu wszystkich klatek.

Domyślnie zadania zależne są planowane do wykonania dopiero po pomyślnym zakończeniu zadania nadrzędnego. Można określić akcję zależności, aby zastąpić domyślne zachowanie i uruchamiać zadania, gdy zadanie nadrzędne zakończy się niepowodzeniem. Zobacz [akcje zależności](#dependency-actions) sekcji, aby uzyskać szczegółowe informacje.  

Można tworzyć zadania, które zależą od innych zadań w relacji jeden do jednego lub jeden do wielu. Można również utworzyć zależność zakresu, gdzie zadanie zależy od ukończenia grupy zadań w określonym zakresie identyfikatorów zadań. Można połączyć te trzy podstawowe scenariusze, aby utworzyć relacje wiele do wielu.

## <a name="task-dependencies-with-batch-net"></a>Zależności zadań z wydzieleniam .NET
W tym artykule omówimy sposób konfigurowania zależności zadań przy użyciu biblioteki [Batch .NET.][net_msdn] Najpierw pokazujemy, jak [włączyć zależność zadań od](#enable-task-dependencies) zadań, a następnie zademonstrujemy sposób [konfigurowania zadania z zależnościami.](#create-dependent-tasks) Opisano również, jak określić akcję zależności do uruchamiania zadań zależnych, jeśli element nadrzędny nie powiedzie się. Na koniec omówimy [scenariusze zależności,](#dependency-scenarios) które obsługuje usługa Batch.

## <a name="enable-task-dependencies"></a>Włączanie zależności zadań
Aby użyć zależności zadań w aplikacji batch, należy najpierw skonfigurować zadanie do używania zależności zadań. W programie Batch .NET włącz go na swoim [cloudjob,][net_cloudjob] ustawiając jego `true` [Właściwość UsesTaskDependencies][net_usestaskdependencies] na:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

W poprzednim fragmentie kodu "batchClient" jest wystąpieniem [klasy BatchClient.][net_batchclient]

## <a name="create-dependent-tasks"></a>Tworzenie zadań zależnych
Aby utworzyć zadanie, które zależy od ukończenia jednego lub więcej zadań nadrzędnych, można określić, że zadanie "zależy od" innych zadań. W obszarze Batch .NET skonfiguruj [program CloudTask][net_cloudtask]. [DependsOn][net_dependson] właściwość z wystąpieniem [TaskDependencies][net_taskdependencies] klasy:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Ten fragment kodu tworzy zadanie zależne o identyfikatorze zadania "Kwiaty". Zadanie "Kwiaty" zależy od zadań "Deszcz" i "Słońce". Zadanie "Kwiaty" zostanie zaplanowane do uruchomienia w węźle obliczeniowym dopiero po pomyślnym zakończeniu zadań "Deszcz" i "Słońce".

> [!NOTE]
> Domyślnie zadanie jest uważane za ukończone pomyślnie, gdy jest w `0`stanie **ukończonym,** a jego kod **zakończenia** jest . W partii .NET oznacza to [CloudTask][net_cloudtask]. [Wartość][net_taskstate] właściwości `Completed` stanu i CloudTask's [TaskExecutionInformation][net_taskexecutioninformation]. Wartość właściwości [ExitCode][net_exitcode] to `0`. Aby dowiedzieć się, jak to zmienić, zobacz [sekcję Akcje zależności.](#dependency-actions)
> 
> 

## <a name="dependency-scenarios"></a>Scenariusze zależności
Istnieją trzy podstawowe scenariusze zależności zadań, których można użyć w usłudze Azure Batch: zależność zakresu jeden do jednego, jeden do wielu i zakres identyfikatora zadań. Można je połączyć, aby zapewnić czwarty scenariusz, wiele do wielu.

| Scenariusz&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Przykład |  |
|:---:| --- | --- |
|  [Jeden do jednego](#one-to-one) |*taskB* zależy od *taskA* <p/> *taskB* nie zostanie zaplanowane do wykonania, dopóki *zadanieA* nie zostanie pomyślnie ukończone |![Diagram: zależność zadania jeden do jednego][1] |
|  [Jeden do wielu](#one-to-many) |*taskC* zależy zarówno od *taskA, jak* i *taskB* <p/> *TaskC* nie zostanie zaplanowane do wykonania, dopóki zarówno *taskA* i *taskB* nie zostaną pomyślnie ukończone |![Diagram: zależność zadania jeden do wielu][2] |
|  [Zakres identyfikatora zadania](#task-id-range) |*taskD* zależy od zakresu zadań <p/> *TaskD* nie zostanie zaplanowane do wykonania, dopóki zadania o identyfikatorach *od 1* do *10* nie zostaną pomyślnie ukończone |![Diagram: Zależność zakresu identyfikatora zadania][3] |

> [!TIP]
> Można tworzyć relacje **wiele do wielu,** na przykład gdzie zadania C, D, E i F zależą od zadań A i B. Jest to przydatne, na przykład w równoległych scenariuszach przetwarzania wstępnego, w których zadania podrzędne zależą od danych wyjściowych wielu zadań nadrzędnych.
> 
> W przykładach w tej sekcji zadanie zależne jest uruchamiane tylko po pomyślnym wykonaniu zadań nadrzędnych. To zachowanie jest domyślnym zachowaniem dla zadania zależnego. Zadanie zależne można uruchomić po wykonaniu zadania nadrzędnego, określając akcję zależności w celu zastąpienia zachowania domyślnego. Zobacz [akcje zależności](#dependency-actions) sekcji, aby uzyskać szczegółowe informacje.

### <a name="one-to-one"></a>Jeden do jednego
W relacji jeden-do-jednego zadanie zależy od pomyślnego zakończenia jednego zadania nadrzędnego. Aby utworzyć zależność, podaj identyfikator pojedynczego zadania do [TaskDependencies][net_taskdependencies]. [OnId][net_onid] metoda statyczna podczas wypełniania [DependsOn][net_dependson] właściwość [CloudTask][net_cloudtask].

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
W relacji jeden do wielu zadanie zależy od ukończenia wielu zadań nadrzędnych. Aby utworzyć zależność, podaj kolekcję identyfikatorów zadań do [TaskDependencies][net_taskdependencies]. [OnIds][net_onids] metoda statyczna podczas wypełniania [DependsOn][net_dependson] właściwość [CloudTask][net_cloudtask].

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

### <a name="task-id-range"></a>Zakres identyfikatora zadania
W zależności od zakresu zadań nadrzędnych zadanie zależy od ukończenia zadań, których identyfikatory znajdują się w zakresie.
Aby utworzyć zależność, podaj pierwsze i ostatnie identyfikatory zadań w zakresie do [TaskDependencies][net_taskdependencies]. [OnIdRange][net_onidrange] metoda statyczna podczas wypełniania [DependsOn][net_dependson] właściwość [CloudTask][net_cloudtask].

> [!IMPORTANT]
> W przypadku używania zakresów identyfikatorów zadań dla zależności zakresy tylko zadania z identyfikatorami reprezentującymi wartości całkowite będą wybierane przez zakres. Tak więc `1..10` zakres `3` wybierze zadania i `7`, ale nie `5flamingoes`. 
> 
> Wiodące zera nie są znaczące podczas oceny zależności zakresu, `4` `04` więc `004` zadania z identyfikatorami ciągów i będą `4`wszystkie *w* zakresie i wszystkie będą traktowane jako zadanie, więc pierwszy do wykonania zadowoli zależność.
> 
> Każde zadanie w zakresie musi spełniać zależność, albo przez ukończenie pomyślnie lub przez ukończenie z błędem, który jest mapowany na akcję zależności ustawioną na **Spełnij**. Zobacz [akcje zależności](#dependency-actions) sekcji, aby uzyskać szczegółowe informacje.
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

Domyślnie zadanie zależne lub zestaw zadań jest uruchamiane tylko po pomyślnym zakończeniu zadania nadrzędnego. W niektórych scenariuszach można uruchomić zadania zależne, nawet jeśli zadanie nadrzędne nie powiedzie się. Zachowanie domyślne można zastąpić, określając akcję zależności. Akcja zależności określa, czy zadanie zależne kwalifikuje się do uruchomienia na podstawie powodzenia lub niepowodzenia zadania nadrzędnego. 

Załóżmy na przykład, że zadanie zależne oczekuje na dane po zakończeniu zadania nadrzędnego. Jeśli zadanie nadrzędne zakończy się niepowodzeniem, zadanie zależne może nadal być w stanie uruchomić przy użyciu starszych danych. W takim przypadku akcja zależności można określić, że zadanie zależne kwalifikuje się do uruchomienia pomimo niepowodzenia zadania nadrzędnego.

Akcja zależności jest oparta na warunku zakończenia zadania nadrzędnego. Można określić akcję zależności dla dowolnego z następujących warunków zakończenia; dla .NET, zobacz [ExitConditions][net_exitconditions] klasy, aby uzyskać szczegółowe informacje:

- Po wystąpieniu błędu przetwarzania wstępnego.
- Po wystąpieniu błędu przekazywania pliku. Jeśli zadanie kończy pracę z kodem zakończenia, który został określony za pośrednictwem **exitCodes** lub **exitCodeRanges**, a następnie napotka błąd przekazywania pliku, akcja określona przez kod zakończenia ma pierwszeństwo.
- Gdy zadanie kończy pracę z kodem zakończenia zdefiniowanym przez **ExitCodes** właściwości.
- Gdy zadanie kończy pracę z kodem zakończenia, który mieści się w zakresie określonym przez **ExitCodeRanges** właściwości.
- Przypadek domyślny, jeśli zadanie kończy działanie z kodem zakończenia nie zdefiniowanym przez **ExitCodes** lub **ExitCodeRanges**, lub jeśli zadanie kończy działanie z błędem przetwarzania wstępnego i właściwość **PreProcessingError** nie jest ustawiona lub jeśli zadanie zakończy się niepowodzeniem z błędem przekazywania pliku i właściwość **FileUploadError** nie jest ustawiona. 

Aby określić akcję zależności w .NET, ustaw [opcję ExitOptions][net_exitoptions]. [Właściwość DependencyAction][net_dependencyaction] dla warunku zakończenia. Właściwość **DependencyAction** przyjmuje jedną z dwóch wartości:

- Ustawienie **DependencyAction** właściwość **na satisfy** wskazuje, że zadania zależne kwalifikują się do uruchomienia, jeśli zadanie nadrzędne kończy działanie z określonym błędem.
- Ustawienie **DependencyAction** właściwość **bloku** wskazuje, że zadania zależne nie kwalifikują się do uruchomienia.

Domyślnym ustawieniem **właściwości DependencyAction** jest **Satisfy** for exit code 0 i **Block** for all other exit conditions.

Poniższy fragment kodu ustawia **właściwość DependencyAction** dla zadania nadrzędnego. Jeśli zadanie nadrzędne kończy pracę z błędem przetwarzania wstępnego lub z określonymi kodami błędów, zadanie zależne jest blokowane. Jeśli zadanie nadrzędne kończy pracę z innym błędem niezerowym, zadanie zależne kwalifikuje się do uruchomienia.

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
Przykładowy projekt [TaskDependencies][github_taskdependencies] jest jednym z [przykładowych przykładów kodu usługi Azure Batch][github_samples] w usłudze GitHub. To rozwiązanie programu Visual Studio pokazuje:

- Jak włączyć zależność zadania od zadania
- Jak tworzyć zadania zależne od innych zadań
- Jak wykonać te zadania w puli węzłów obliczeniowych.

## <a name="next-steps"></a>Następne kroki
### <a name="application-deployment"></a>Wdrażanie aplikacji
Funkcja [pakietów aplikacji](batch-application-packages.md) usługi Batch zapewnia łatwy sposób wdrażania i wersji aplikacji wykonywanych przez zadania w węzłach obliczeniowych.

### <a name="installing-applications-and-staging-data"></a>Instalowanie aplikacji i danych przemieszczania
Zobacz [Instalowanie aplikacji i danych przemieszczania w węzłach obliczeniowych usługi Batch na][forum_post] forum usługi Azure Batch, aby uzyskać omówienie metod przygotowywania węzłów do uruchamiania zadań. Napisany przez jednego z członków zespołu usługi Azure Batch, ten post jest dobrym podkładem na różne sposoby kopiowania aplikacji, danych wejściowych zadań i innych plików do węzłów obliczeniowych.

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

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagram: zależność jeden do jednego"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagram: zależność od jednego do wielu"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagram: zależność zakresu identyfikatora zadania"
