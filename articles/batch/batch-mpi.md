---
title: Uruchamianie aplikacji MPI — Usługa Azure Batch | Dokumenty firmy Microsoft
description: Dowiedz się, jak wykonać aplikacje interfejsu MPI (Message Passing Interface) przy użyciu typu zadania z wieloma wystąpieniami w usłudze Azure Batch.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 83e34bd7-a027-4b1b-8314-759384719327
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.date: 03/13/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1896fea3c401299b4f77235ab3c02d85708b7041
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023671"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Uruchamianie aplikacji mpi (Message Passing Interface) w umykaniu za pomocą zadań wielu wystąpień w uprowadzanie aplikacji interfejsu MPI (Message Passing Interface) w upartyjniku

Zadania z wieloma wystąpieniami umożliwiają jednoczesne uruchamianie zadania usługi Azure Batch w wielu węzłach obliczeniowych. Te zadania umożliwiają wysokiej wydajności scenariuszy obliczeniowych, takich jak message passing interface (MPI) aplikacji w ulotce. W tym artykule dowiesz się, jak wykonywać zadania z wieloma wystąpieniami przy użyciu biblioteki [Batch .NET.][api_net]

> [!NOTE]
> Podczas gdy przykłady w tym artykule koncentrują się na węzłach obliczeniowych Batch .NET, MS-MPI i Windows, pojęcia o zadaniach wielu wystąpień omówione w tym miejscu mają zastosowanie do innych platform i technologii (na przykład Python i Intel MPI w węzłach systemu Linux).
>
>

## <a name="multi-instance-task-overview"></a>Omówienie zadań wieloapadowych
W usłudze Batch każde zadanie jest zwykle wykonywane w jednym węźle obliczeniowym — przesyłasz wiele zadań do zadania, a usługa Batch planuje każde zadanie do wykonania w węźle. Jednak konfigurując **ustawienia wielu wystąpień**zadania, należy poinformować firmę Batch, aby zamiast tego utworzyła jedno zadanie podstawowe i kilka podzadań, które są następnie wykonywane w wielu węzłach.

![Omówienie zadań wieloapadowych][1]

Po przesłaniu zadania z ustawieniami wielu wystąpień do zadania usługa Batch wykonuje kilka kroków unikatowych dla zadań z wieloma wystąpieniami:

1. Usługa Batch tworzy jedno **podstawowe** i kilka **podzadań** na podstawie ustawień wielu wystąpień. Całkowita liczba zadań (podstawowe plus wszystkie podzadania) odpowiada liczbie **wystąpień (węzłów** obliczeniowych) określonych w ustawieniach wielu wystąpień.
2. Partia wyznacza jeden z węzłów obliczeniowych jako **wzorca**i planuje zadanie podstawowe do wykonania na wzorcu. Planuje podzadania do wykonania w pozostałej części węzłów obliczeniowych przydzielonych do zadania wieloodstanowienia, po jednym podzadaniu na węzeł.
3. Podstawowe i wszystkie podzadęcia pobierają wszystkie **typowe pliki zasobów** określone w ustawieniach wielu wystąpień.
4. Po pobraniu typowych plików zasobów podstawowe i podzadania wykonują **polecenie koordynacji** określone w ustawieniach wielu wystąpień. Polecenie koordynacji jest zwykle używane do przygotowania węzłów do wykonania zadania. Może to obejmować uruchamianie usług w tle `smpd.exe`(takich jak [Microsoft MPI][msmpi_msdn]) i sprawdzanie, czy węzły są gotowe do przetwarzania komunikatów między węzłami.
5. Zadanie podstawowe wykonuje **polecenie aplikacji** w węźle głównym *po* pomyślnym wykonaniu polecenia koordynacji przez podstawowe i wszystkie podzadacje. Polecenie application jest wierszem polecenia samego zadania z wieloma wystąpieniami i jest wykonywane tylko przez zadanie podstawowe. W rozwiązaniu opartym na [technologii MS-MPI][msmpi_msdn]jest to miejsce, `mpiexec.exe`w którym można wykonać aplikację z obsługą MPI przy użyciu .

> [!NOTE]
> Chociaż jest funkcjonalnie odrębne, "zadanie wieloodstanowienia" nie jest unikatowym typem zadania, takim jak [StartTask][net_starttask] lub [JobPreparationTask][net_jobprep]. Zadanie z wieloma wystąpieniami jest po prostu standardowym zadaniem usługi Batch[(CloudTask][net_task] in Batch .NET), którego ustawienia wielu wystąpień zostały skonfigurowane. W tym artykule nazywamy to **zadaniem wieloadłowym**.
>
>

## <a name="requirements-for-multi-instance-tasks"></a>Wymagania dotyczące zadań z wieloma wystąpieniami
Zadania z wieloma wystąpieniami wymagają puli z **włączoną komunikacją między węzłami**i **z wyłączonym wykonywaniem równoczesnych zadań.** Aby wyłączyć równoczesne wykonywanie zadań, należy ustawić właściwość [CloudPool.MaxTasksPerComputeNode](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool) na 1.

> [!NOTE]
> Partia [ogranicza](batch-quota-limit.md#pool-size-limits) rozmiar puli, która ma włączoną komunikację między węzłami.


Ten fragment kodu pokazuje, jak utworzyć pulę dla zadań z wieloma wystąpieniami przy użyciu biblioteki Batch .NET.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

> [!NOTE]
> Jeśli spróbujesz uruchomić zadanie wielu wystąpień w puli z komunikacją między węzłową wyłączona lub z wartością *maxTasksPerNode* większą niż 1, zadanie nigdy nie zostanie zaplanowane — pozostaje przez czas nieokreślony w stanie "aktywnym". 


### <a name="use-a-starttask-to-install-mpi"></a>Instalowanie interfejsu MPI za pomocą zadania startowego
Aby uruchomić aplikacje MPI z zadaniem wieloadłowym, należy najpierw zainstalować implementację MPI (na przykład MS-MPI lub Intel MPI) w węzłach obliczeniowych w puli. Jest to dobry moment, aby użyć [StartTask][net_starttask], który jest wykonywany za każdym razem, gdy węzeł łączy puli lub jest ponownie uruchamiany. Ten fragment kodu tworzy element StartTask określający pakiet instalacyjny systemu MS-MPI jako [plik zasobów][net_resourcefile]. Wiersz polecenia zadania startowego jest wykonywany po pobraniu pliku zasobu do węzła. W takim przypadku wiersz polecenia wykonuje instalację nienadzorowane ms-MPI.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin)),
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Zdalny bezpośredni dostęp do pamięci (RDMA)
Po wybraniu [rozmiaru obsługującego rdma,](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) takiego jak A9 dla węzłów obliczeniowych w puli usługi Batch, aplikacja MPI może korzystać z sieci zdalnego dostępu do pamięci bezpośredniej (RDMA) platformy Azure o wysokiej wydajności i niskim opóźnieniu.

Poszukaj rozmiarów określonych jako "rdma capable" w następujących artykułach:

* Pule **cloudserviceconfiguration**

  * [Rozmiary usług w chmurze](../cloud-services/cloud-services-sizes-specs.md) (tylko system Windows)
* **Pule VirtualMachineConfiguration**

  * [Rozmiary maszyn wirtualnych na platformie Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux)
  * [Rozmiary maszyn wirtualnych na platformie Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows)

> [!NOTE]
> Aby skorzystać z rdma w [węzłach obliczeniowych systemu Linux,](batch-linux-nodes.md)należy użyć **intel mpi** w węzłach. 
>

## <a name="create-a-multi-instance-task-with-batch-net"></a>Tworzenie zadania z wieloma wystąpieniami za pomocą usługi Batch .NET
Teraz, gdy omówiliśmy wymagania puli i instalację pakietu MPI, utwórzmy zadanie wieloadłowiowe. W tym urywek tworzymy standardowe [CloudTask][net_task], a następnie skonfigurować jego [MultiInstanceSettings][net_multiinstance_prop] właściwości. Jak wspomniano wcześniej, zadanie z wieloma wystąpieniami nie jest odrębnym typem zadania, ale standardowym zadaniem usługi Batch skonfigurowanym z ustawieniami wielu wystąpień.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Zadanie podstawowe i podzadęcie
Podczas tworzenia ustawień wielu wystąpień dla zadania należy określić liczbę węzłów obliczeniowych, które mają wykonać zadanie. Podczas przesyłania zadania do zadania usługa Batch tworzy jedno **zadanie podstawowe** i wystarczającą liczbę **podzadań,** które razem odpowiadają określonej liczbie węzłów.

Te zadania są przypisywane identyfikator liczby całkowitej w zakresie od 0 do *numberOfInstances* - 1. Zadanie z identyfikatorem 0 jest zadaniem podstawowym, a wszystkie inne identyfikatory są podzadańami. Na przykład, jeśli utworzysz następujące ustawienia wielu wystąpień dla zadania, zadanie podstawowe będzie miało identyfikator 0, a podzadacje będą miały identyfikatory od 1 do 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Węzeł główny
Podczas przesyłania zadania z wieloma wystąpieniami usługa Batch wyznacza jeden z węzłów obliczeniowych jako węzeł "główny" i planuje zadanie podstawowe do wykonania w węźle głównym. Podzadęcie jest zaplanowane do wykonania w pozostałych węzłach przydzielonych do zadania z wieloma wystąpieniami.

## <a name="coordination-command"></a>Polecenie koordynacja
**Polecenie koordynacji** jest wykonywane zarówno przez zadania podstawowe, jak i podzadań.

Wywołanie polecenia koordynacji jest blokowanie --Batch nie wykonuje polecenia aplikacji, dopóki polecenie koordynacji nie zwróci pomyślnie dla wszystkich podzadań. W związku z tym polecenie koordynacji należy uruchomić wszystkie wymagane usługi w tle, sprawdzić, czy są one gotowe do użycia, a następnie zakończyć działanie. Na przykład to polecenie koordynacji dla rozwiązania przy użyciu ms-MPI w wersji 7 uruchamia usługę SMPD w węźle, a następnie kończy pracę:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Należy zwrócić `start` uwagę na użycie w tym poleceniu koordynacji. Jest to wymagane, `smpd.exe` ponieważ aplikacja nie zwraca natychmiast po wykonaniu. Bez użycia polecenia [start][cmd_start] to polecenie koordynacji nie zwróci i w związku z tym zablokuje uruchomienie polecenia aplikacji.

## <a name="application-command"></a>Polecenie Application
Po zakończeniu wykonywania polecenia koordynacji zadania podstawowego i wszystkich podzadań wiersz polecenia zadania z wieloma wystąpieniami jest wykonywany *tylko*przez zadanie podstawowe . Nazywamy to **poleceniem aplikacji,** aby odróżnić go od polecenia koordynacji.

W przypadku aplikacji MS-MPI użyj polecenia aplikacji, aby wykonać `mpiexec.exe`aplikację obsługującą mpi za pomocą programu . Na przykład w tym miejscu znajduje się polecenie aplikacji dla rozwiązania przy użyciu systemu MS-MPI w wersji 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

> [!NOTE]
> Ponieważ ms-MPI `mpiexec.exe` używa zmiennej `CCP_NODES` domyślnie (zobacz [zmienne środowiskowe) przykładowy](#environment-variables)wiersz polecenia aplikacji powyżej wyklucza ją.
>
>

## <a name="environment-variables"></a>Zmienne środowiskowe
Usługa Batch tworzy kilka [zmiennych środowiskowych specyficznych][msdn_env_var] dla zadań wielu wystąpień w węzłach obliczeniowych przydzielonych do zadania z wieloma wystąpieniami. Linie poleceń koordynacji i aplikacji mogą odwoływać się do tych zmiennych środowiskowych, podobnie jak skrypty i programy, które wykonują.

Następujące zmienne środowiskowe są tworzone przez usługę Batch do użytku przez zadania z wieloma wystąpieniami:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Aby uzyskać szczegółowe informacje na temat tych i innych zmiennych środowiskowych węzłów obliczeniowych batch, w tym ich zawartości i widoczności, zobacz [Zmienne środowiskowe węzła obliczeniowego][msdn_env_var].

> [!TIP]
> Przykład kodu MPI systemu Windows batch linux zawiera przykład, jak można użyć kilku z tych zmiennych środowiskowych. Skrypt [bash cmd koordynuje][coord_cmd_example] pobieranie typowych plików aplikacji i plików wejściowych z usługi Azure Storage, włącza udział systemu plików NFS w węźle głównym i konfiguruje inne węzły przydzielone do zadania wielu wystąpień jako klienci systemu plików NFS.
>
>

## <a name="resource-files"></a>Pliki zasobów
Istnieją dwa zestawy plików zasobów do rozważenia dla zadań z wieloma wystąpieniami: **typowe pliki zasobów** pobierane przez *wszystkie* zadania (zarówno podstawowe, jak i podzadęcia) oraz **pliki zasobów** określone dla samego zadania z wieloma wystąpieniami, które są pobierane tylko za zadanie *podstawowe.*

Można określić jeden lub więcej **typowych plików zasobów** w ustawieniach wielu wystąpień dla zadania. Te typowe pliki zasobów są pobierane z [usługi Azure Storage](../storage/common/storage-introduction.md) do katalogu **udostępnionego zadania** każdego węzła przez podstawowe i wszystkie podzadań. Dostęp do katalogu udostępnionego zadania można uzyskać z `AZ_BATCH_TASK_SHARED_DIR` wierszy poleceń aplikacji i koordynacji przy użyciu zmiennej środowiskowej. Ścieżka `AZ_BATCH_TASK_SHARED_DIR` jest identyczna w każdym węźle przydzielonym do zadania z wieloma wystąpieniami, dzięki czemu można udostępnić pojedyncze polecenie koordynacji między podstawowym i wszystkimi podzadaniami. Usługa Batch nie "udostępnia" katalogu w sensie dostępu zdalnego, ale można go używać jako punktu instalacji lub udostępniania, jak wspomniano wcześniej w końcówce zmiennych środowiskowych.

Pliki zasobów określone dla samego zadania z wieloma wystąpieniami są domyślnie `AZ_BATCH_TASK_WORKING_DIR`pobierane do katalogu roboczego zadania. Jak wspomniano, w przeciwieństwie do typowych plików zasobów, tylko podstawowe zadanie pobiera pliki zasobów określone dla samego zadania wieloastowidanowego.

> [!IMPORTANT]
> Zawsze używaj `AZ_BATCH_TASK_SHARED_DIR` zmiennych `AZ_BATCH_TASK_WORKING_DIR` środowiskowych i odwołuje się do tych katalogów w wierszach poleceń. Nie próbuj konstruować ścieżek ręcznie.
>
>

## <a name="task-lifetime"></a>Okres istnienia zadania
Okres istnienia zadania podstawowego kontroluje okres istnienia całego zadania z wieloma wystąpieniami. Po zakończeniu podstawowego, wszystkie podzadacje są zakończone. Kod zakończenia podstawowego jest kod zakończenia zadania i dlatego jest używany do określenia sukcesu lub niepowodzenia zadania do celów ponawiania.

Jeśli którykolwiek z podzadań zakończyć się niepowodzeniem, zamykanie z kodem zwracanym niezerowe, na przykład całe zadanie wielooddziałowe kończy się niepowodzeniem. Zadanie z wieloma wystąpieniami jest następnie zakończone i ponowione, aż do jego limitu ponawiania.

Po usunięciu zadania z wieloma wystąpieniami podstawowe i wszystkie podzadacje są również usuwane przez usługę Batch. Wszystkie katalogi podzadania i ich pliki są usuwane z węzłów obliczeniowych, podobnie jak w przypadku zadania standardowego.

[TaskConstraints][net_taskconstraints] dla zadania wielomandowy, takich jak [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime][net_taskconstraint_maxwallclock]i [RetentionTime][net_taskconstraint_retention] właściwości, są honorowane, ponieważ są one dla zadania standardowego i stosuje się do podstawowych i wszystkich podzadania. Jednak jeśli zmienisz [właściwość RetentionTime][net_taskconstraint_retention] po dodaniu zadania z wieloma wystąpieniami do zadania, ta zmiana zostanie zastosowana tylko do zadania podstawowego. Wszystkie podzadanie nadal używa oryginalnego [retencji .][net_taskconstraint_retention]

Lista ostatnich zadań węzła obliczeniowego odzwierciedla identyfikator podzadania, jeśli ostatnie zadanie było częścią zadania z wieloma wystąpieniami.

## <a name="obtain-information-about-subtasks"></a>Uzyskiwanie informacji o podzadęciu
Aby uzyskać informacje o podzadęciu przy użyciu biblioteki Batch .NET, należy wywołać metodę [CloudTask.ListSubtasks.][net_task_listsubtasks] Ta metoda zwraca informacje o wszystkich podzadaniach oraz informacje o węźle obliczeniowym, który wykonał zadania. Na podstawie tych informacji można określić katalog główny każdego podzadęcie, identyfikator puli, jego bieżący stan, kod zakończenia i inne. Można użyć tych informacji w połączeniu z [PoolOperations.GetNodeFile][poolops_getnodefile] metody, aby uzyskać pliki podzadania. Należy zauważyć, że ta metoda nie zwraca informacji dla zadania podstawowego (identyfikator 0).

> [!NOTE]
> O ile nie określono inaczej, metody Batch .NET, które działają na wiele wystąpień [CloudTask][net_task] sam stosuje się *tylko* do zadania podstawowego. Na przykład po wywołaniu [CloudTask.ListNodeFiles][net_task_listnodefiles] metody na zadanie z wieloma wystąpieniami, zwracane są tylko pliki zadania podstawowego.
>
>

Poniższy fragment kodu pokazuje, jak uzyskać informacje o podzadaniu, a także zawartość pliku żądania z węzłów, w których zostały wykonane.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == SubtaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>Przykład kodu
Przykład kodu [MultiInstanceTasks][github_mpi] w usłudze GitHub pokazuje, jak używać zadania z wieloma wystąpieniami do uruchamiania aplikacji [MS-MPI][msmpi_msdn] w węzłach obliczeniowych usługi Batch. Wykonaj kroki w [przygotowaniu](#preparation) i [wykonaniu,](#execution) aby uruchomić próbkę.

### <a name="preparation"></a>Przygotowywanie
1. Wykonaj pierwsze dwa kroki w [Jak skompilować i uruchomić prosty program MS-MPI][msmpi_howto]. Spełnia to wymagania wstępne dla następującego kroku.
2. Tworzenie wersji *wersji* programu [MPIHelloWorld][helloworld_proj] próbki MPI. Jest to program, który będzie uruchamiany na węzłach obliczeniowych przez zadanie z wieloma wystąpieniami.
3. Utwórz plik zip `MPIHelloWorld.exe` zawierający (który został `MSMpiSetup.exe` zbudowany krok 2) i (który został pobrany krok 1). Przekażesz ten plik zip jako pakiet aplikacji w następnym kroku.
4. Użyj [witryny Azure portal,][portal] aby utworzyć [aplikację](batch-application-packages.md) Batch o nazwie "MPIHelloWorld" i określić plik zip utworzony w poprzednim kroku jako wersja "1.0" pakietu aplikacji. Aby uzyskać więcej [informacji, zobacz Przekazywanie aplikacji i zarządzanie nimi.](batch-application-packages.md#upload-and-manage-applications)

> [!TIP]
> Skompiluj `MPIHelloWorld.exe` *wersję wersji,* aby nie trzeba było dołączać `msvcp140d.dll` żadnych `vcruntime140d.dll`dodatkowych zależności (na przykład lub) w pakiecie aplikacji.
>
>

### <a name="execution"></a>Wykonanie
1. Pobierz [przykłady partii azure][github_samples_zip] z usługi GitHub.
2. Otwórz **rozwiązanie** MultiInstanceTasks w programie Visual Studio 2019. Plik `MultiInstanceTasks.sln` rozwiązania znajduje się w:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
3. Wprowadź poświadczenia konta usługi Batch `AccountSettings.settings` i Storage w projekcie **Microsoft.Azure.Batch.Samples.Common.**
4. **Tworzenie i uruchamianie** rozwiązania MultiInstanceTasks do wykonania przykładowej aplikacji MPI w węzłach obliczeniowych w puli usługi Batch.
5. *Opcjonalnie:* Przed usunięciem zasobów użyj [portalu Azure][portal] lub [Eksploratora wsadowego,][batch_labs] aby zbadać pulę próbkowania, zadanie i zadanie ("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask").

> [!TIP]
> Możesz pobrać [visual studio społeczności][visual_studio] za darmo, jeśli nie masz visual studio.
>
>

Wyjście `MultiInstanceTasks.exe` z jest podobne do następujących:

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Następne kroki
* W blogu microsoft HPC & Azure Batch Team omówiono [obsługę mpi dla systemu Linux w usłudze Azure Batch][blog_mpi_linux]i zawiera informacje na temat korzystania z usługi [OpenFOAM][openfoam] z usługą Batch. Przykłady kodu języka Python można znaleźć dla [przykładu OpenFOAM na GitHub][github_mpi].
* Dowiedz się, jak [tworzyć pule węzłów obliczeniowych systemu Linux](batch-linux-nodes.md) do użycia w rozwiązaniach usługi Azure Batch MPI.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: https://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: https://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
[openfoam]: http://www.openfoam.com/
[visual_studio]: https://www.visualstudio.com/vs/community/

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[portal]: https://portal.azure.com
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Omówienie wielu wystąpień"
