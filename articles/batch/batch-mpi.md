---
title: Użycie wielu wystąpień zadań w celu uruchamiania aplikacji MPI - partii zadań Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie wykonywania aplikacji komunikat interfejsu (Passing Interface), za pomocą typu zadania wielu wystąpień w partii zadań Azure.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 83e34bd7-a027-4b1b-8314-759384719327
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.date: 5/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0fb5ea21c6403369cbcb60df58c0f70a57a61d4e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30160760"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Użycie wielu wystąpień zadań w celu uruchamiania aplikacji komunikat interfejsu (Passing Interface) w partii

Mająca wiele wystąpień zadań umożliwiają uruchomić zadanie partii zadań Azure jednocześnie na wielu węzłach obliczeniowych. Te zadania umożliwiają wysokiej wydajności obliczeniowej scenariuszy, takich jak aplikacje interfejsu Message (Passing) w partii. W tym artykule opisano sposób wykonania wielu wystąpień zadania przy użyciu [partiami platformy .NET] [ api_net] biblioteki.

> [!NOTE]
> Przykłady w tym artykule dotyczą partiami platformy .NET, MS-MPI i węzły obliczeniowe systemu Windows, wielu wystąpień zadań omówione w tym miejscu są stosowane do innych platform i technologii (Python i Intel MPI w węzłach Linux, na przykład).
>
>

## <a name="multi-instance-task-overview"></a>Omówienie zadań wielu wystąpień
W zadaniu wsadowym, każde zadanie jest zazwyczaj wykonywane na jednym węźle obliczeń — przesłać wielu zadań do zadania, a usługa partia zadań harmonogramy poszczególnych zadań do wykonania w węźle. Jednakże, konfigurując zadania **ustawień wielu wystąpieniach**, poinformuj partii, zamiast tego utworzyć jedno zadanie podstawowego i kilka podzadań, które następnie są wykonywane na wielu węzłach.

![Omówienie zadań wielu wystąpień][1]

Po przesłaniu zadania o wielu wystąpieniach ustawienia do zadania wsadowego wykonuje kilka kroków unikatowy do wielu wystąpień zadań:

1. Usługa partia zadań tworzy jeden **głównej** i kilka **podzadania** na podstawie ustawień wielu wystąpień. Łączna liczba zadań (podstawowym oraz wszystkie podzadania) jest zgodna z liczbą **wystąpień** (węzły obliczeniowe) określonym w ustawieniach wielu wystąpień.
2. Wsadowe wskazuje na jeden z węzłów obliczeniowych jako **wzorca**oraz ustalenia jego harmonogramu głównej zadań do wykonania na głównym. Planowana podzadania do wykonania na pozostałą część węzły obliczeniowe przydzielone do wielu wystąpień zadania, co podzadanie na węzeł.
3. Podstawową i wszystkie podzadania pobrać żadnej **wspólne pliki zasobów** określonymi w ustawieniach wielu wystąpień.
4. Po wspólnych zasobów pliki zostały pobrane, podstawową i wykonywanie podzadania **polecenia koordynacji** określonymi w ustawieniach wielu wystąpień. Polecenie koordynacji zwykle służy do przygotowania węzły do wykonywania zadania. Mogą to być uruchomienie usługi tła (takich jak [Microsoft MPI][msmpi_msdn]w `smpd.exe`) i sprawdzanie, czy węzły są gotowe do przetworzenia komunikatów między węzłami.
5. Podstawowe zadania wykonuje **polecenia aplikacji** w węźle głównym *po* pomyślnie wykonać polecenia koordynacji serwerem podstawowym, a wszystkie podzadania. Polecenie aplikacji jest to samo zadanie wielu wystąpień wiersz polecenia i są wykonywane tylko przez podstawowe zadania. W [MS-MPI][msmpi_msdn]— na podstawie rozwiązania, jest to, gdzie można wykonywać przy użyciu aplikacji obsługującej na MPI `mpiexec.exe`.

> [!NOTE]
> Chociaż funkcjonalnie distinct, "zadanie wielu wystąpieniach" nie jest typu unikatowy zadań, takich jak [StartTask] [ net_starttask] lub [JobPreparationTask] [ net_jobprep]. Zadanie wielu wystąpień jest po prostu standardowe zadania wsadowego ([CloudTask] [ net_task] w partiami platformy .NET) o wielu wystąpieniach ustawienia zostały skonfigurowane. W tym artykule określane jako **wielu wystąpień zadań**.
>
>

## <a name="requirements-for-multi-instance-tasks"></a>Wymagania dotyczące zadań w wielu wystąpieniach
Mająca wiele wystąpień zadania wymagają z pulą **włączono komunikację między węzłami**oraz z **wykonywanie zadań jednoczesnych wyłączone**. Aby wyłączyć wykonywanie zadań jednoczesnych, ustaw [CloudPool.MaxTasksPerComputeNode](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool#Microsoft_Azure_Batch_CloudPool_MaxTasksPerComputeNode) właściwości na wartość 1.

> [!NOTE]
> Wsadowe [limity](batch-quota-limit.md#other-limits) rozmiar puli, który ma włączoną komunikację między węzłami.


Następujący fragment kodu przedstawia sposób tworzenia puli dla wielu wystąpień zadań za pomocą biblioteki partiami platformy .NET.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

> [!NOTE]
> Jeśli zostanie podjęta próba uruchomienia zadania wielu wystąpień w puli, bez wyłączone komunikacji między węzłami lub z *maxTasksPerNode* wartość większą niż 1, nie jest zaplanowane zadanie — nieskończoność pozostaje w stanie "aktywny". 
>
> Mająca wiele wystąpień zadania mogą wykonywać tylko w przypadku węzłów w pulach utworzone po 14 grudnia 2015 r.
>
>

### <a name="use-a-starttask-to-install-mpi"></a>Należy zainstalować MPI StartTask
Do uruchamiania aplikacji MPI o wielu wystąpieniach zadania, należy najpierw zainstalować implementacja interfejsu MPI (MS-MPI lub MPI firmy Intel, na przykład) w węzłach obliczeniowych w puli. Jest to odpowiedni moment, aby użyć [StartTask][net_starttask], który wykonuje zawsze, gdy węzeł dołącza pulę lub ponownego uruchomienia. Następujący fragment kodu tworzy StartTask, który określa pakiet instalacyjny MS-MPI jako [pliku zasobu][net_resourcefile]. Wiersz polecenia zadania uruchamiania jest wykonywany po pobraniu pliku zasobu do węzła. W takim przypadku wiersza polecenia przeprowadza instalację nienadzorowaną MS-MPI.

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
Po wybraniu [z funkcją RDMA rozmiar](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) A9 dla węzłów obliczeniowych w puli partii, np. aplikacji MPI można korzystać z platformy Azure wysokiej wydajności i małych opóźnieniach zdalnego pamięci bezpośredniego dostępu (do pamięci RDMA) sieci.

Wyszukaj rozmiary określony jako "RDMA stanie" w następujących artykułach:

* **CloudServiceConfiguration** pule

  * [Rozmiary dla usług w chmurze](../cloud-services/cloud-services-sizes-specs.md) (tylko system Windows)
* **VirtualMachineConfiguration** pule

  * [Rozmiary maszyn wirtualnych na platformie Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux)
  * [Rozmiary maszyn wirtualnych na platformie Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (system Windows)

> [!NOTE]
> Aby móc korzystać z funkcji RDMA [węzły obliczeniowe Linux](batch-linux-nodes.md), należy użyć **Intel MPI** w węzłach. 
>

## <a name="create-a-multi-instance-task-with-batch-net"></a>Utwórz zadanie wielu wystąpień z partiami platformy .NET
Teraz, kiedy możemy zostały objęte wymagań puli i instalacji pakietu MPI, Utwórzmy zadań wielu wystąpień. W tym fragmencie, utworzymy standard [CloudTask][net_task], skonfiguruj jego [MultiInstanceSettings] [ net_multiinstance_prop] właściwości. Jak wspomniano wcześniej, wielu wystąpieniach zadanie nie jest typem różne zadania, ale zadania wsadowego standardowego skonfigurowane przy użyciu ustawień wielu wystąpień.

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
Podczas tworzenia ustawienia wielu wystąpień dla tego zadania można określić numer węzłów obliczeniowych, które są komponentu. Podczas przesyłania zadań do zadania, usługa partia zadań tworzy jeden **głównej** zadań i wystarczającą ilość **podzadania** razem spełniających liczba węzłów określona.

Te zadania są przypisane identyfikator liczbą całkowitą z zakresu od 0 do *numberOfInstances* - 1. Zadania o identyfikatorze 0 jest głównym zadaniem, a wszystkie inne identyfikatory są podzadania. Na przykład jeśli tworzysz wiele wystąpień następujących ustawień zadania głównym zadaniem będzie mieć identyfikator 0 i podzadań znajdują się identyfikatory 1 do 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Węzła głównego
Po przesłaniu zadania wielu wystąpień usługi partia zadań określa jeden z węzłów obliczeniowych jako węzeł "główny" oraz ustalenia jego harmonogramu głównej zadań do wykonania w węźle głównym. Do wykonania w pozostałych węzłach przydzielone do wielu wystąpień zadania zaplanowane podzadań.

## <a name="coordination-command"></a>Polecenie koordynacji
**Polecenia koordynacji** jest wykonywane przez serwer podstawowy i podzadania.

Wywołanie polecenia koordynacji blokuje — partii nie wykonuj polecenia aplikacji, aż polecenie koordynacji zwrócił pomyślnie dla wszystkich zadań podrzędnych. Polecenie koordynacji powinny w związku z tym uruchomić wszystkie wymagane tła usługi, sprawdź, czy są gotowe do użycia, a następnie zamknij. Na przykład to polecenie koordynacji rozwiązania przy użyciu MS-MPI w wersji 7 uruchamia usługę SMPD w węźle kończy pracę:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Zwróć uwagę na użycie `start` w tym poleceniu koordynacji. Jest to wymagane, ponieważ `smpd.exe` aplikacji nie może zwracać natychmiast po wykonaniu. Bez użycia [start] [ cmd_start] polecenia, to polecenie koordynacji nie zwróci i w związku z tym uniemożliwiają uruchamianie polecenia aplikacji.

## <a name="application-command"></a>Polecenie aplikacji
Po głównym zadaniem i wszystkie podzadania zostało ukończone, wykonując polecenie koordynacji, wiersz polecenia zadania wielu wystąpień jest wykonywana przez podstawowe zadania *tylko*. Nazywamy to **polecenia aplikacji** odróżniający go od polecenia koordynacji.

Dla aplikacji MS MPI, użyj polecenia aplikacji do wykonywania aplikacji z obsługą MPI z `mpiexec.exe`. Na przykład poniżej przedstawiono polecenia aplikacji dotyczących rozwiązania przy użyciu MS-MPI w wersji 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

> [!NOTE]
> Ponieważ MS MPI `mpiexec.exe` używa `CCP_NODES` zmiennej domyślnie (zobacz [zmiennych środowiskowych](#environment-variables)) powyżej wiersza polecenia aplikacji przykład wyklucza go.
>
>

## <a name="environment-variables"></a>Zmienne środowiskowe
Tworzy kilka partii [zmiennych środowiskowych] [ msdn_env_var] specyficzne dla zadania wielu wystąpień w węzłach obliczeniowych przydzielony do zadania wielu wystąpień. Jak skrypty i programy, które są one wykonywane wiersze polecenia koordynacji i aplikacji może się odwoływać zmienne środowiskowe.

Następujące zmienne środowiskowe są tworzone przez usługę partii do użycia przez wiele wystąpień zadania:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Aby uzyskać szczegółowe informacje o tych i innych partii obliczeniowe węzła zmiennych środowiskowych, łącznie z ich zawartość i widoczność, zobacz [obliczeniowe zmiennych środowiskowych węzła][msdn_env_var].

> [!TIP]
> Przykładowy kod partii Linux MPI zawiera przykład sposobu niektóre zmienne środowiskowe użycia. [Cmd koordynacji] [ coord_cmd_example] skryptu Bash pobiera typowych aplikacji i danych wejściowych plików z usługi Azure Storage, włącza udziału sieciowego systemu plików (NFS), w węźle głównym i konfiguruje innych węzłów przydzielony do zadania wielu wystąpień jako klienci systemu plików NFS.
>
>

## <a name="resource-files"></a>Pliki zasobów
Istnieją dwa zestawy plików zasobów wziąć pod uwagę dla wielu wystąpień zadań: **wspólne pliki zasobów** który *wszystkie* zadania Pobierz (podstawowych i podzadania) i **pliki zasobów** określona dla wielu wystąpień zadań, które *tylko podstawowy* zadań pliki do pobrania.

Można określić jedną lub więcej **wspólne pliki zasobów** w ustawieniach wielu wystąpień dla zadania. Te wspólne pliki zasobów są pobierane z [usługi Azure Storage](../storage/common/storage-introduction.md) w każdym węźle **zadań udostępnionego katalogu** serwerem podstawowym, a wszystkie podzadania. Udostępniony katalog zadania mogą korzystać z aplikacji i koordynacji wierszy polecenia, za pomocą `AZ_BATCH_TASK_SHARED_DIR` zmiennej środowiskowej. `AZ_BATCH_TASK_SHARED_DIR` Jest identyczne w każdym węźle przydzielone do wielu wystąpień zadania, w związku z tym można udostępnić polecenie pojedynczego koordynację między serwerem podstawowym i wszystkie podzadania. Partii nie "Udostępnij" katalog w znaczeniu dostępu zdalnego, ale można go użyć jako instalacji lub współużytkować punkt, jak wspomniano wcześniej w końcówką zmiennych środowiskowych.

Pliki zasobów, które można określić dla samo zadanie wielu wystąpieniach są pobierane do katalogu roboczego zadania, `AZ_BATCH_TASK_WORKING_DIR`, domyślnie. Jak wspomniano, w przeciwieństwie do plików wspólnych zasobów, głównym zadaniem pobiera pliki zasobów określony dla samo zadanie wielu wystąpień.

> [!IMPORTANT]
> Należy zawsze używać zmiennych środowiskowych `AZ_BATCH_TASK_SHARED_DIR` i `AZ_BATCH_TASK_WORKING_DIR` do odwoływania się do tych katalogów w Twojej wiersze polecenia. Nie należy ręcznie utworzyć ścieżki.
>
>

## <a name="task-lifetime"></a>Cykl życia zadania
Okres istnienia głównym zadaniem kontroluje cykl życia zadania całego wielu wystąpień. Gdy kończy się serwerem podstawowym, są zamykane wszystkie podzadania. Kod zakończenia podstawowych jest kod zakończenia zadania, a w związku z tym służy do określania powodzenie lub niepowodzenie zadania dla celów ponów próbę.

W przypadku awarii dowolnego podzadań, został zakończony z kodem zwrotnym zera, na przykład cały wielu wystąpień niepowodzenia zadania. Zadanie wielu wystąpień jest następnie przerwane i ponowione do jego limit ponownych prób.

Podczas usuwania zadania wielu wystąpień serwera podstawowego i wszystkie podzadania są również usuwane przez usługi partia zadań. Podzadanie wszystkie katalogi i pliki są usuwane z węzłów obliczeniowych, podobnie jak w przypadku zadania standardowego.

[TaskConstraints] [ net_taskconstraints] dla wielu wystąpień zadania, takie jak [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime] [ net_taskconstraint_maxwallclock], i [RetentionTime] [ net_taskconstraint_retention] właściwości, są honorowane standardowe zadania, i mają zastosowanie do serwera podstawowego i wszystkie podzadania. Jednak w przypadku zmiany [RetentionTime] [ net_taskconstraint_retention] właściwości po dodaniu zadań wielu wystąpień do zadania, ta zmiana jest stosowane tylko do głównej zadań. Wszystkie podzadania nadal używać oryginalnej [RetentionTime][net_taskconstraint_retention].

Listy ostatnich zadań węźle obliczeń odzwierciedla identyfikator podzadaniem ostatnie zadanie było częścią zadań wielu wystąpień.

## <a name="obtain-information-about-subtasks"></a>Uzyskaj informacje na temat podzadania
Aby uzyskać informacje na podzadania przy użyciu biblioteki partiami platformy .NET, należy wywołać [CloudTask.ListSubtasks] [ net_task_listsubtasks] metody. Ta metoda zwraca informacje o wszystkich podzadań oraz informacje o węźle obliczeń, które wykonywane zadania. Z tych informacji można określić katalogu głównego każdego podzadania, identyfikator puli, jego bieżący stan i kod zakończenia. Te informacje można użyć w połączeniu z [PoolOperations.GetNodeFile] [ poolops_getnodefile] metoda uzyskania podzadanie plików. Należy pamiętać, że ta metoda nie zwraca informacje o zadaniu głównej (identyfikator: 0).

> [!NOTE]
> O ile nie określono inaczej, metody partiami platformy .NET, które działają na wielu wystąpieniach [CloudTask] [ net_task] się zastosować *tylko* z podstawowym zadaniem. Na przykład podczas wywoływania [CloudTask.ListNodeFiles] [ net_task_listnodefiles] metody w wielu wystąpieniach zadania, zwracane są tylko pliki głównym zadaniem.
>
>

Poniższy fragment kodu przedstawia sposób uzyskiwania informacji podzadania, jak również zawartość pliku żądania w węzłach, na których są wykonywane.

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
[MultiInstanceTasks] [ github_mpi] przykładowy kod w serwisie GitHub pokazano, jak używane do uruchamiania zadań wielowystąpieniowy [MS-MPI] [ msmpi_msdn] aplikację na Węzły obliczeniowe partii. Postępuj zgodnie z instrukcjami [przygotowania](#preparation) i [wykonywania](#execution) do uruchomienia przykładu.

### <a name="preparation"></a>Przygotowanie
1. Postępuj zgodnie z instrukcjami dwóch pierwszych [jak skompilować i uruchomić prosty program MS-MPI][msmpi_howto]. Spełnia on wymagania wstępne dotyczące następny krok.
2. Tworzenie *wersji* wersji [MPIHelloWorld] [ helloworld_proj] przykładowy program MPI. Jest to program, który zostanie uruchomione dla węzłów obliczeniowych w zadaniu wielu wystąpień.
3. Tworzenie pliku zip zawierającego `MPIHelloWorld.exe` (która będzie utworzony w kroku 2) i `MSMpiSetup.exe` (który został pobrany w kroku 1). Ten plik zip będzie przekazać jako pakietu aplikacji w następnym kroku.
4. Użyj [portalu Azure] [ portal] do tworzenia instancji [aplikacji](batch-application-packages.md) o nazwie "MPIHelloWorld" i określ plik zip został utworzony w poprzednim kroku jako wersji "1.0" pakiet aplikacji. Zobacz [przekazywanie aplikacji i zarządzanie nimi](batch-application-packages.md#upload-and-manage-applications) Aby uzyskać więcej informacji.

> [!TIP]
> Tworzenie *wersji* wersji `MPIHelloWorld.exe` , dzięki czemu nie trzeba obejmują dodatkowe zależności (na przykład `msvcp140d.dll` lub `vcruntime140d.dll`) do pakietu aplikacji.
>
>

### <a name="execution"></a>Wykonywanie
1. Pobierz [azure partii próbek] [ github_samples_zip] z usługi GitHub.
2. Otwórz MultiInstanceTasks **rozwiązania** w programie Visual Studio 2015 lub nowszego. `MultiInstanceTasks.sln` Plik rozwiązania znajduje się w:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
3. Wprowadź poświadczenia konta wsadowego i magazynowania w `AccountSettings.settings` w **Microsoft.Azure.Batch.Samples.Common** projektu.
4. **Tworzenie i uruchamianie** rozwiązania MultiInstanceTasks wykonać MPI przykładową aplikację w węzłach obliczeń w puli partii.
5. *Opcjonalne*: Użyj [portalu Azure] [ portal] lub [BatchLabs] [ batch_labs] do sprawdzenia puli próbki, zadań i zadań (" MultiInstanceSamplePool","MultiInstanceSampleJob","MultiInstanceSampleTask") przed usunięciem zasobów.

> [!TIP]
> Możesz pobrać [Visual Studio Community] [ visual_studio] bezpłatnie, jeśli nie masz programu Visual Studio.
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
* W tym artykule omówiono Microsoft HPC & Azure partii Team blog [MPI obsługę systemu Linux na partii zadań Azure][blog_mpi_linux]oraz informacje na temat używania [OpenFOAM] [ openfoam] z partii. Można znaleźć przykłady kodu języka Python dla [przykład OpenFOAM w serwisie GitHub][github_mpi].
* Dowiedz się, jak [tworzenia pul węzły obliczeniowe Linux](batch-linux-nodes.md) do użycia w rozwiązań MPI usługi partia zadań Azure.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchLabs/
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
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

[1]: ./media/batch-mpi/batch_mpi_01.png "Informacje o wielu wystąpieniach"
