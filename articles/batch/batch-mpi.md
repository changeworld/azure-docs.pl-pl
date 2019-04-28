---
title: Użyj zadania obejmujące wiele wystąpień do uruchamiania aplikacji MPI — Azure Batch | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykonać interfejsu przekazywania komunikatów (MPI) applications za pomocą typu zadania obejmujące wiele wystąpień w usłudze Azure Batch.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 83e34bd7-a027-4b1b-8314-759384719327
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.date: 03/13/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7fe75dabe098cf98f0c3c04d592a32d6a44cebf8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775328"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Zadania obejmujące wiele wystąpień umożliwiają uruchamianie aplikacji interfejsu przekazywania komunikatów (MPI) w usłudze Batch

Zadania obejmujące wiele wystąpień umożliwiają jednoczesnego uruchamiania zadania usługi Azure Batch na wielu węzłach obliczeniowych. Te zadania umożliwiają o wysokiej wydajności obliczeniowej scenariuszy, takich jak aplikacje interfejsu przekazywania komunikatów (MPI) w usłudze Batch. W tym artykule dowiesz się, jak wykonywać zadania obejmujące wiele wystąpień przy użyciu [platformy .NET usługi Batch] [ api_net] biblioteki.

> [!NOTE]
> Przykłady w niniejszym artykule skupić się na platformie .NET usługi Batch, MS-MPI, i węzły obliczeniowe Windows, pojęcia dotyczące zadania obejmujące wiele wystąpień, które zostały omówione w tym miejscu są stosowane do innych platform i technologii (Python i Intel MPI w węzłach systemu Linux, na przykład).
>
>

## <a name="multi-instance-task-overview"></a>Omówienie zadań w wielu wystąpieniach
W usłudze Batch, każde zadanie jest zazwyczaj wykonywane na jednym węźle obliczeń — przedstawia wiele zadań do zadania, a usługa Batch harmonogramy poszczególnych zadań do wykonania w węźle. Jednakże, konfigurując zadania podrzędnego **ustawienia wielu wystąpień**, wskazujemy partii, zamiast tego utworzyć jedno zadanie podstawowego i kilka podzadań, które następnie są wykonywane na wielu węzłach.

![Omówienie zadań w wielu wystąpieniach][1]

Gdy prześlesz zadanie przy użyciu ustawienia wielu wystąpień do zadania usługi Batch wykonuje kilka czynności, unikatowe do zadania obejmujące wiele wystąpień:

1. Usługa Batch tworzy jeden **podstawowego** i kilka **podzadania** zgodnie z ustawieniami obejmujące wiele wystąpień. Łączna liczba zadań (podstawowe oraz wszystkie podzadania) jest zgodna z liczbą **wystąpień** (węzły obliczeniowe) określonymi w ustawieniach obejmujące wiele wystąpień.
2. Batch wskazuje na jeden z węzłów obliczeniowych jako **wzorca**i planuje główne zadanie do wykonania we wzorcu. Planuje ona podzadań, które można wykonać w pozostałych węzłów obliczeniowych przydzielony do zadania obejmujące wiele wystąpień, co podzadania na węzeł.
3. Podstawowe i wszystkie podzadania pobrać dowolny **wspólne pliki zasobów** określonymi w ustawieniach obejmujące wiele wystąpień.
4. Po zasobów wspólne pliki zostały pobrane, podstawowej i wykonanie podzadań **polecenia koordynacji** określonymi w ustawieniach obejmujące wiele wystąpień. Polecenie koordynacji jest zwykle używane w celu przygotowania węzłów do wykonywania zadania. Może to obejmować uruchamianie usługi w tle (takie jak [Microsoft MPI][msmpi_msdn]firmy `smpd.exe`) i weryfikowania, czy węzły są gotowe do przetwarzania komunikatów z węzłami.
5. Głównym zadaniem, wykonuje **polecenia aplikacji** w węźle głównym *po* polecenia koordynacji została zakończona pomyślnie podstawowym, a wszystkie podzadania. Polecenie aplikacji jest wiersz polecenia samo zadanie obejmujące wiele wystąpień i jest wykonywane tylko przez główne zadanie. W [MS MPI][msmpi_msdn]-oparty na dyskach, jest to, gdzie można wykonać przy użyciu aplikacji wykorzystujących MPI `mpiexec.exe`.

> [!NOTE]
> Chociaż jest funkcjonalnie distinct, "task obejmujące wiele wystąpień" nie jest typem unikatowy zadania, takie jak [StartTask] [ net_starttask] lub [JobPreparationTask] [ net_jobprep]. Zadanie obejmujące wiele wystąpień jest po prostu standardowe zadania przetwarzania wsadowego ([CloudTask] [ net_task] platformie .NET usługi Batch) skonfigurowano którego ustawienia wielu wystąpień. W tym artykule nazywamy jako **zadanie podrzędne obejmujące wiele wystąpień**.
>
>

## <a name="requirements-for-multi-instance-tasks"></a>Wymagania dotyczące zadania obejmujące wiele wystąpień
Zadania obejmujące wiele wystąpień wymagają puli z **komunikacji między węzłami włączone**i **wykonywania zadań jednoczesnych wyłączone**. Aby wyłączyć wykonywanie zadań jednoczesnych, ustaw [CloudPool.MaxTasksPerComputeNode](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool) właściwości na wartość 1.

> [!NOTE]
> Batch [limity](batch-quota-limit.md#pool-size-limits) rozmiar puli, który ma włączoną komunikacji między węzłami.


Ten fragment kodu pokazuje, jak utworzyć pulę dla zadania obejmujące wiele wystąpień przy użyciu biblioteki .NET usługi Batch.

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
> Jeśli zostanie podjęta próba uruchomienia zadania obejmujące wiele wystąpień w puli, komunikacji między węzłami, wyłączone lub z *maxTasksPerNode* wartość większą niż 1, nigdy nie zaplanowano zadania — na czas nieokreślony pozostaje on w stanie "aktywny". 


### <a name="use-a-starttask-to-install-mpi"></a>Użyć funkcji StartTask do zainstalowania MPI
Do uruchamiania aplikacji MPI zadania obejmujące wiele wystąpień, należy najpierw zainstalować implementacja interfejsu MPI (MS-MPI lub Intel MPI, na przykład) w węzłach obliczeniowych w puli. Jest to dobry moment, aby użyć [StartTask][net_starttask], które wykonuje, gdy węzeł dołącza do puli lub ponownego uruchomienia. Ten fragment kodu tworzy w podzadanie StartTask, który określa pakiet instalacyjny MS MPI jako [pliku zasobów][net_resourcefile]. Wiersz polecenia zadania podrzędnego uruchamiania jest wykonywana po pobraniu pliku zasobów do węzła. W tym przypadku wiersza polecenia przeprowadza instalację nienadzorowaną MS MPI.

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
Po wybraniu [obsługą dostępu RDMA rozmiar](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) A9 dla węzłów obliczeniowych w puli usługi Batch, np. aplikacji MPI korzystać z zalet platformy Azure o wysokiej wydajności i niskich opóźnieniach zdalnego pamięci dostępu (RDMA) sieci.

Wyszukaj rozmiarów, określony jako "Technologii RDMA" w następujących artykułach:

* **CloudServiceConfiguration** pul

  * [Rozmiary usług Cloud Services](../cloud-services/cloud-services-sizes-specs.md) (tylko Windows)
* **VirtualMachineConfiguration** pul

  * [Rozmiary maszyn wirtualnych na platformie Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux)
  * [Rozmiary maszyn wirtualnych na platformie Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows)

> [!NOTE]
> Aby móc korzystać z funkcji RDMA [węzłów obliczeniowych systemu Linux](batch-linux-nodes.md), należy użyć **Intel MPI** w węzłach. 
>

## <a name="create-a-multi-instance-task-with-batch-net"></a>Tworzenie zadania obejmujące wiele wystąpień przy użyciu platformy .NET usługi Batch
Teraz, gdy Omówiliśmy już wymagania puli i instalacji pakietu MPI, Utwórzmy zadanie obejmujące wiele wystąpień. W tym fragmencie kodu, możemy utworzyć standardowego [CloudTask][net_task], skonfiguruj jego [MultiInstanceSettings] [ net_multiinstance_prop] właściwości. Jak wspomniano wcześniej, zadanie obejmujące wiele wystąpień nie jest typem różne zadania, ale standardowe zadania przetwarzania wsadowego skonfigurowano ustawienia wielu wystąpień.

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

## <a name="primary-task-and-subtasks"></a>Podstawowe zadania i podzadania
Podczas tworzenia ustawienia wielu wystąpień dla zadania podrzędnego, określasz liczbę węzłów obliczeniowych, które są do wykonania zadania. Po przesłaniu zadania do zadania usługa Batch tworzy jeden **podstawowego** zadań i wystarczająco **podzadania** liczbę węzłów, które można określić zgodnych ze sobą.

Te zadania są przypisywane identyfikator liczbą całkowitą z zakresu od 0 do *numberOfInstances* - 1. Zadania o identyfikatorze 0 jest głównym zadaniem, a wszystkie inne identyfikatory są podzadania. Na przykład tworząc następujące ustawienia obejmujące wiele wystąpień zadania główne zadanie może mieć identyfikator 0 i podzadań znajdują się identyfikatory 1 do 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Węzeł główny
Po przesłaniu zadania obejmujące wiele wystąpień usługi Batch określa jeden z węzłów obliczeniowych jako węzeł "główną" i planuje główne zadanie do wykonania na węzła głównego. Podzadania są zaplanowane do wykonania w pozostałych węzłach przydzielony do zadania obejmujące wiele wystąpień.

## <a name="coordination-command"></a>Polecenia koordynacji
**Polecenia koordynacji** jest wykonywana przez podstawowy i podzadania.

Blokuje wywołania polecenia koordynacji — partii nie jest wykonywane polecenie aplikacji, dopóki polecenia koordynacji zwrócił się pomyślnie dla wszystkich podzadań. Polecenia koordynacji powinny w związku z tym start wszystkich usług wymaganych tła, sprawdź, czy są one gotowe do użycia, a następnie zamknij. Na przykład tego polecenia koordynacji rozwiązania przy użyciu MS MPI w wersji 7 uruchamia usługę SMPD w węźle kończy pracę:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Zwróć uwagę na użycie `start` w tym poleceniu koordynacji. Jest to wymagane, ponieważ `smpd.exe` aplikacji nie może zwracać natychmiast po wykonaniu. Bez użycia [start] [ cmd_start] polecenia tego polecenia koordynacji nie zwróci i dlatego mogłyby spowodować zablokowanie polecenie aplikacji działa.

## <a name="application-command"></a>Polecenie aplikacji
Gdy podstawowe zadania i wszystkie podzadania zakończą pracę wykonywanie polecenia koordynacji, wiersza polecenia zadania obejmujące wiele wystąpień jest wykonywana przez główne zadanie *tylko*. Nazywamy to **polecenia aplikacji** w odróżnieniu od polecenia koordynacji.

W przypadku aplikacji MS MPI polecenie aplikacji do wykonywania aplikacji z obsługą MPI przy użyciu `mpiexec.exe`. Na przykład Oto polecenie aplikacji dla rozwiązania przy użyciu MS-MPI w wersji 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

> [!NOTE]
> Ponieważ MS MPI `mpiexec.exe` używa `CCP_NODES` zmiennej domyślnie (zobacz [zmienne środowiskowe](#environment-variables)) powyżej wiersza polecenia aplikacji przykład wyłącza je.
>
>

## <a name="environment-variables"></a>Zmienne środowiskowe
Usługa Batch tworzy kilka [zmienne środowiskowe] [ msdn_env_var] specyficzne dla zadania obejmujące wiele wystąpień w węzłach obliczeniowych przydzielony do zadania obejmujące wiele wystąpień. Wiersze polecenia koordynacji i aplikacji może odwoływać się do tych zmiennych środowiskowych jak skrypty i programy, które są wykonywane.

Następujące zmienne środowiskowe są tworzone przez usługę Batch do użytku przez zadania podrzędne obejmujące wiele wystąpień:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Aby uzyskać szczegółowe informacje na ten temat i inne zadanie wsadowe obliczeniowych zmienne środowiskowe węzła, łącznie z ich zawartość i widoczności, zobacz [zmienne środowiskowe węzła obliczeniowego][msdn_env_var].

> [!TIP]
> Wsadowe systemu Linux MPI przykładowy kod zawiera przykład sposobu użycia niektóre z tych zmiennych środowiskowych można. [Cmd koordynacji] [ coord_cmd_example] skryptu powłoki systemowej pobiera typowych aplikacji i danych wejściowych plików z usługi Azure Storage, włącza udziale Network File System (NFS) węzła głównego i konfiguruje innych węzłów przydzielony do zadania obejmujące wiele wystąpień jako klientów systemu plików NFS.
>
>

## <a name="resource-files"></a>Pliki zasobów
Istnieją dwa rodzaje plików zasobów, należy wziąć pod uwagę dla zadania obejmujące wiele wystąpień: **wspólne pliki zasobów** , *wszystkich* podzadania pobierają (podstawowych i podzadań), a **pliki zasobów** określona dla wielu wystąpień zadań, które *tylko podstawowy* zadań pliki do pobrania.

Można określić co najmniej jedną **wspólne pliki zasobów** w ustawieniach obejmujące wiele wystąpień zadania. Następujące wspólne pliki zasobów są pobierane z [usługi Azure Storage](../storage/common/storage-introduction.md) do każdego węzła **udostępnionego katalogu zadań** podstawowym, a wszystkie podzadania. Dostęp udostępnionego katalogu zadania z aplikacji i koordynacji wierszy polecenia przy użyciu `AZ_BATCH_TASK_SHARED_DIR` zmiennej środowiskowej. `AZ_BATCH_TASK_SHARED_DIR` Ścieżka jest taka sama w każdym węźle przydzielony do zadania obejmujące wiele wystąpień, więc można udostępniać polecenie pojedynczego koordynacji między główną wszystkie podzadania. Batch nie "Udostępnianie" katalogu, w tym sensie dostępu zdalnego, ale można go użyć jako instalacji lub punktu, jak wspomniano wcześniej w porady od zmiennych środowiskowych.

Pliki zasobów, które określisz dla zadania obejmujące wiele wystąpień, sama zostaną pobrane do katalogu roboczego zadania, `AZ_BATCH_TASK_WORKING_DIR`, domyślnie. Jak wspomniano wcześniej, w przeciwieństwie do wspólne pliki zasobów tylko podstawowy podzadanie pobiera pliki zasobów określona dla samo zadanie obejmujące wiele wystąpień.

> [!IMPORTANT]
> Zawsze używaj zmiennych środowiskowych `AZ_BATCH_TASK_SHARED_DIR` i `AZ_BATCH_TASK_WORKING_DIR` do odwoływania się do tych katalogów w swojej wiersze polecenia. Nie należy próbować ręcznie utworzyć ścieżki.
>
>

## <a name="task-lifetime"></a>Okres istnienia zadania
Okres istnienia zadania podstawowego określa okres istnienia zadania całego wielu wystąpieniach. Gdy podstawowy kończy działanie, zostają zakończone wszystkie podzadania. Kod zakończenia podstawowy jest kod zakończenia zadania i dlatego jest używana do ustalenia powodzenia lub niepowodzenia zadania dla celów ponownych prób.

W przypadku awarii wszystkich podzadań, zakończone z kodem zwrotu różna od zera, na przykład zadanie całego obejmujące wiele wystąpień kończy się niepowodzeniem. Zadanie obejmujące wiele wystąpień jest następnie przerwana i ponowione maksymalnie limit ponownych prób.

Jeśli usuniesz zadanie obejmujące wiele wystąpień, podstawowe i wszystkie podzadania również zostaną usunięte przez usługę Batch. Wszystkie podzadania katalogi i ich pliki są usuwane z węzłów obliczeniowych, podobnie jak w przypadku standardowych zadań.

[TaskConstraints] [ net_taskconstraints] zadania obejmujące wiele wystąpień, takie jak [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime] [ net_taskconstraint_maxwallclock], i [RetentionTime] [ net_taskconstraint_retention] właściwości, są uznawane jako są standardowe zadania i mają zastosowanie do podstawowej i wszystkie podzadania. Jednakże jeśli zmienisz [RetentionTime] [ net_taskconstraint_retention] właściwości po dodaniu zadania obejmujące wiele wystąpień do zadania, a ta zmiana jest stosowane tylko do podstawowe zadania. Wszystkie podzadania w dalszym ciągu używać oryginalny [RetentionTime][net_taskconstraint_retention].

Z listy ostatnich zadań węzła obliczeniowego odzwierciedla identyfikator podzadania, jeśli ostatnie zadanie było częścią zadania obejmujące wiele wystąpień.

## <a name="obtain-information-about-subtasks"></a>Uzyskać informacje na temat podzadań
Aby uzyskać informacje na podzadania, za pomocą biblioteki .NET usługi Batch, wywołać [CloudTask.ListSubtasks] [ net_task_listsubtasks] metody. Ta metoda zwraca informacje o wszystkich podzadań i informacje o węźle obliczeniowym, wykonywanego zadania. Z tych informacji można określić katalog główny każdego z nich, identyfikator puli, jego bieżący stan i kod zakończenia. Te informacje można używać w połączeniu z [PoolOperations.GetNodeFile] [ poolops_getnodefile] metodę, aby uzyskać pliki w podzadanie. Należy pamiętać, że ta metoda nie zwraca informacje o zadaniu podstawowego (identyfikator: 0).

> [!NOTE]
> Jeżeli nie określono inaczej, metod .NET usługi Batch, które działają na wielu wystąpieniach [CloudTask] [ net_task] się zastosować *tylko* z podstawowym zadaniem. Na przykład gdy wywołujesz [CloudTask.ListNodeFiles] [ net_task_listnodefiles] metody zadania obejmujące wiele wystąpień, zwracane są tylko pliki główne zadanie.
>
>

Poniższy fragment kodu przedstawia sposób uzyskiwania informacji w podzadanie, a także żądania zawartość pliku z węzłów, na których są wykonywane.

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
[MultiInstanceTasks] [ github_mpi] przykładowy kod w serwisie GitHub pokazuje, jak przy użyciu zadania obejmujące wiele wystąpień do uruchamiania [MS MPI] [ msmpi_msdn] aplikacji Węzły obliczeniowe usługi Batch. Postępuj zgodnie z instrukcjami w [przygotowania](#preparation) i [wykonywania](#execution) do uruchomienia przykładu.

### <a name="preparation"></a>Przygotowanie
1. Wykonaj dwa pierwsze kroki w [sposobach kompilowania i uruchamiania prostego programu MS MPI][msmpi_howto]. Spełnia on wymagania wstępne dotyczące następny krok.
2. Tworzenie *wersji* wersję [MPIHelloWorld] [ helloworld_proj] przykładowy program MPI. To jest program, który będzie uruchamiany w węzłach obliczeniowych przez zadanie podrzędne obejmujące wiele wystąpień.
3. Tworzenie pliku zip zawierającego `MPIHelloWorld.exe` (który został utworzony w kroku 2) i `MSMpiSetup.exe` (który został pobrany w kroku 1). Ten plik zip będzie przekazać jako pakiet aplikacji w następnym kroku.
4. Użyj [witryny Azure portal] [ portal] do tworzenia instancji [aplikacji](batch-application-packages.md) o nazwie "MPIHelloWorld" i określ plik zip utworzony w poprzednim kroku jako wersji "1.0" pakiet aplikacji. Zobacz [przekazywanie aplikacji i zarządzanie nimi](batch-application-packages.md#upload-and-manage-applications) Aby uzyskać więcej informacji.

> [!TIP]
> Tworzenie *wersji* wersję `MPIHelloWorld.exe` tak, aby nie trzeba uwzględnić wszelkie dodatkowe zależności (na przykład `msvcp140d.dll` lub `vcruntime140d.dll`) w pakiecie aplikacji.
>
>

### <a name="execution"></a>Wykonanie
1. Pobierz [azure-batch-samples] [ github_samples_zip] z usługi GitHub.
2. Otwórz MultiInstanceTasks **rozwiązania** w programie Visual Studio 2017. `MultiInstanceTasks.sln` Plik rozwiązania znajduje się w:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
3. Wprowadź poświadczenia konta usługi Batch i Storage w `AccountSettings.settings` w **Microsoft.Azure.Batch.Samples.Common** projektu.
4. **Kompilowanie i uruchamianie** rozwiązania MultiInstanceTasks do wykonania MPI przykładowej aplikacji w węzłach obliczeniowych w puli usługi Batch.
5. *Opcjonalnie*: Użyj [witryny Azure portal] [ portal] lub [programu Batch Explorer] [ batch_labs] zbadanie przykładową pulę, zadania i zadania ("MultiInstanceSamplePool"," MultiInstanceSampleJob","MultiInstanceSampleTask") przed usunięciem zasobów.

> [!TIP]
> Możesz pobrać [programu Visual Studio Community] [ visual_studio] za darmo, jeśli nie masz programu Visual Studio.
>
>

Dane wyjściowe z `MultiInstanceTasks.exe` jest podobny do następującego:

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

## <a name="next-steps"></a>Kolejne kroki
* W tym artykule omówiono Microsoft HPC i usługi Azure Batch Team blog [MPI pomocy technicznej dla systemu Linux w usłudze Azure Batch][blog_mpi_linux]i zawiera informacje na temat korzystania z [OpenFOAM] [ openfoam] przy użyciu usługi Batch. Można znaleźć przykłady kodu Python dla [OpenFOAM przykładu w usłudze GitHub][github_mpi].
* Dowiedz się, jak [tworzyć pule węzłów obliczeniowych systemu Linux](batch-linux-nodes.md) do użytku w rozwiązaniach usługi Azure Batch MPI.

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

[1]: ./media/batch-mpi/batch_mpi_01.png "Omówienie obejmujące wiele wystąpień"
