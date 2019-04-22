---
title: Utrwalanie zadań i zadań danych wyjściowych do usługi Azure Storage za pomocą biblioteki Konwencji plików dla platformy .NET — Azure Batch | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać biblioteki Konwencji plików usługi Batch Azure dla platformy .NET umożliwia utrwalanie partii zadań i zadań danych wyjściowych do usługi Azure Storage i wyświetlanie utrwalonych danych wyjściowych w witrynie Azure portal.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d30a5ca0910c5ceebb38dec7b4cdbffd9b3cf27e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58916788"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>Utrwalanie danych i zadań do usługi Azure Storage za pomocą biblioteki Konwencji plików usługi Batch dla platformy .NET

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Jednym ze sposobów, aby zachować dane zadania jest użycie [biblioteki Konwencji plików usługi Batch Azure dla platformy .NET][nuget_package]. Biblioteki Konwencji plików upraszcza proces zadanie danych wyjściowych do usługi Azure Storage do przechowywania i pobierania. Możesz użyć biblioteki Konwencji plików w kodzie zadań i klienta &mdash; w kodzie utrwalanie plików, zadań i w kodzie klienta, aby wyświetlić listę i pobierać je. Kod zadania umożliwia również biblioteki pobierać dane wyjściowe zadania nadrzędnego, takie jak [zależności zadań podrzędnych](batch-task-dependencies.md) scenariusza.

Pobieranie plików danych wyjściowych za pomocą biblioteki Konwencji plików, można znaleźć plików dla danego zadania, wyświetlając je według Identyfikatora i przeznaczenia. Nie trzeba znać nazwy lub lokalizacje plików. Na przykład możesz wyświetlić listę wszystkich plików pośrednich dla danego zadania za pomocą biblioteki Konwencji plików lub pobieranie pliku w wersji zapoznawczej dla danego zadania.

> [!TIP]
> Począwszy od wersji 2017-05-01, interfejs API usługi Batch obsługuje utrwalanie danych wyjściowych do usługi Azure Storage dla zadań i zadania Menedżer zadania, które działają w przypadku pul utworzonych za pomocą konfiguracji maszyny wirtualnej. Interfejs API usługi Batch zapewnia prosty sposób utrwalanie danych wyjściowych z kodem, który tworzy zadanie i służy jako alternatywa dla biblioteki Konwencji plików. Można zmodyfikować aplikację kliencką usługi Batch, aby utrwalić dane wyjściowe bez konieczności aktualizowania aplikacji, w którym działa zadanie. Aby uzyskać więcej informacji, zobacz [utrwalanie zadań dane do usługi Azure Storage za pomocą usługi Batch usług interfejsu API](batch-task-output-files.md).

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Kiedy używać biblioteki Konwencji plików, aby zachować dane wyjściowe zadania?

Usługa Azure Batch udostępnia więcej niż jeden sposób, aby zachować dane wyjściowe zadania. Konwencje plików najlepiej nadaje się do tych scenariuszy:

- Można łatwo modyfikować kodu dla aplikacji, w którym działa zadanie do utrwalania plików za pomocą biblioteki Konwencji plików.
- Chcesz się przesyłanie strumieniowe danych do usługi Azure Storage, gdy zadanie jest uruchomione.
- Chcesz zachować dane z pul utworzonych za pomocą konfiguracji usługi w chmurze lub konfigurację maszyny wirtualnej.
- Aplikację kliencką lub innych zadań w ramach zadania musi zlokalizować i pobieranie plików wyjściowych zadania według Identyfikatora lub według celu.
- Chcesz wyświetlić dane wyjściowe zadania w witrynie Azure portal.

Jeśli scenariusz różni się od przedstawionych powyżej, może być konieczne należy wziąć pod uwagę innego podejścia. Aby uzyskać więcej informacji na temat innych opcji utrwalanie danych wyjściowych zadania, zobacz [utrwalanie danych wyjściowych zadań i zadań w usłudze Azure Storage](batch-task-output.md).

## <a name="what-is-the-batch-file-conventions-standard"></a>Co to jest standardowe konwencje plików usługi Batch?

[Standardowe konwencje pliku wsadowego](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) zawiera schemat nazewnictwa miejsce docelowe, kontenerów i obiektów blob ścieżek, do których Twoje pliki wyjściowe są zapisywane. Pliki utrwalone w magazynie platformy Azure ze standardem Konwencji plików są automatycznie dostępne pod kątem wyświetlania w portalu Azure. Portal zna konwencji nazewnictwa i dlatego można wyświetlić pliki, które stosować się do niego.

Biblioteki Konwencji plików dla platformy .NET automatycznie nazwy magazynu, kontenerów i plików wyjściowych zadania zgodnie z konwencjami plików standardowych. Biblioteki Konwencji plików udostępnia również metody służące do wykonywania zapytań plików wyjściowych w usłudze Azure Storage przy użyciu Identyfikatora zadania, identyfikator zadania: czy przeznaczenia.

Jeśli tworzysz z języka innego niż .NET, można zaimplementować standardowej konwencji plików samodzielnie w aplikacji. Aby uzyskać więcej informacji, zobacz [implementuje standardowe konwencje pliku wsadowego](batch-task-output.md#implement-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Połączyć konto usługi Azure Storage ze swoim kontem usługi Batch

Można utrwalić danych wyjściowych do usługi Azure Storage za pomocą biblioteki Konwencji plików, należy najpierw połączyć konto usługi Azure Storage z kontem usługi Batch. Jeśli użytkownik jeszcze tego nie zrobiono, połączyć konto usługi Storage z kontem usługi Batch przy użyciu [witryny Azure portal](https://portal.azure.com):

1. W witrynie Azure Portal przejdź do swojego konta usługi Batch.
1. W obszarze **ustawienia**, wybierz opcję **konta magazynu**.
1. Jeśli nie masz jeszcze konta magazynu skojarzonego z kontem usługi Batch, kliknij przycisk **konta usługi Storage (Brak)**.
1. Wybierz konto magazynu z listy dla Twojej subskrypcji. Aby uzyskać najlepszą wydajność należy użyć konta usługi Azure Storage, która znajduje się w tym samym regionie co konto usługi Batch, na którym są uruchomione zadania podrzędne.

## <a name="persist-output-data"></a>Utrwalanie danych wyjściowych

Można utrwalić danych wyjściowych zadań i zadań, za pomocą biblioteki Konwencji plików, należy utworzyć kontener w usłudze Azure Storage, a następnie zapisz dane wyjściowe do kontenera. Użyj [biblioteki klienta usługi Azure Storage dla platformy .NET](https://www.nuget.org/packages/WindowsAzure.Storage) w kodzie zadania, aby przekazać dane wyjściowe zadania do kontenera. 

Aby uzyskać więcej informacji na temat pracy z kontenerów i obiektów blob w usłudze Azure Storage, zobacz [wprowadzenie do usługi Azure Blob storage przy użyciu platformy .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

> [!WARNING]
> Wszystkie dane wyjściowe zadań i zadań trwały Konwencji plików biblioteki są przechowywane w tym samym kontenerze. Jeśli dużą liczbę zadań do utrwalania plików w tym samym czasie [magazynu limity ograniczania przepływności](../storage/common/storage-performance-checklist.md#blobs) może wtedy zostać wymuszone.

### <a name="create-storage-container"></a>Tworzenie kontenera magazynu

Aby zachować dane wyjściowe zadania do usługi Azure Storage, najpierw utwórz kontener, wywołując [CloudJob][net_cloudjob].[ PrepareOutputStorageAsync][net_prepareoutputasync]. Ta metoda rozszerzenia ma [CloudStorageAccount] [ net_cloudstorageaccount] obiektu jako parametr. Tworzy kontener o nazwie zgodnie ze standardem Konwencji plików, tak aby jego zawartość jest wykrywane przez usługę witryny Azure portal oraz metody pobierania omówione w dalszej części tego artykułu.

Zazwyczaj umieścisz kod, aby utworzyć kontener w aplikacji klienckiej &mdash; aplikacja, która tworzy swoje pul, zadań i zadań.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Dane wyjściowe zadania Store

Teraz, gdy przygotowanej kontenera w usłudze Azure Storage, zadania można zapisywać dane wyjściowe do kontenera za pomocą [TaskOutputStorage] [ net_taskoutputstorage] znaleźć klasy biblioteki Konwencji plików.

W kodzie zadań, należy najpierw utworzyć [TaskOutputStorage] [ net_taskoutputstorage] obiektu, a następnie, gdy zadanie zakończy pracę, wywołaj [TaskOutputStorage] [ net_taskoutputstorage]. [SaveAsync] [ net_saveasync] metodę, aby zapisać dane wyjściowe do usługi Azure Storage.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

`kind` Parametru [TaskOutputStorage](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage).[ SaveAsync](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync#overloads) metoda kategoryzuje utrwalonych plików. Istnieją cztery wstępnie zdefiniowane [TaskOutputKind] [ net_taskoutputkind] typów: `TaskOutput`, `TaskPreview`, `TaskLog`, i `TaskIntermediate.` można również definiować niestandardowe kategorie danych wyjściowych.

Te typy danych wyjściowych umożliwiają określenie typu danych wyjściowych, aby wyświetlić listę później zapytania usługi Batch dla utrwalonych danych wyjściowych dla danego zadania. Innymi słowy po wyświetleniu listy danych wyjściowych dla zadania, będzie można filtrować listę na jednym z typów danych wyjściowych. Na przykład "Proszę *Podgląd* danych wyjściowych dla zadania *109*." Więcej informacji o ofercie i pobieranie danych wyjściowych jest wyświetlany w pobieranie danych wyjściowych, w dalszej części tego artykułu.

> [!TIP]
> Rodzaj wyjścia określa również, gdzie w witrynie Azure portal danego pliku pojawia się: *TaskOutput*-skategoryzowane plików są wyświetlane w obszarze **plików wyjściowych zadania**, i *TaskLog* plików są wyświetlane w obszarze **zadań dzienniki**.

### <a name="store-job-outputs"></a>Dane wyjściowe zadania Store

Oprócz przechowywania danych wyjściowych zadania, można przechowywać dane wyjściowe skojarzone z całego zadania. Na przykład w zadaniu scalania zadania renderowania movie, można utrwalić pełni renderowanego filmu jako dane wyjściowe zadania. Po zakończeniu zadania aplikacji klienckiej, można wyświetlić listę i pobieranie danych wyjściowych dla zadania i nie jest konieczne zapytania poszczególne zadania.

Store dane wyjściowe zadania, wywołując [JobOutputStorage][net_joboutputstorage].[ SaveAsync] [ net_joboutputstorage_saveasync] metody, a następnie określ [JobOutputKind] [ net_joboutputkind] i nazwa pliku:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Podobnie jak w przypadku **TaskOutputKind** typ danych wyjściowych zadania, możesz użyć [JobOutputKind] [ net_joboutputkind] typu do kategoryzowania zadania użytkownika utrwalone plików. Ten parametr umożliwia kwerendy dla określonego typu danych wyjściowych (lista). **JobOutputKind** typu zawiera zarówno dane wyjściowe, jak i w wersji zapoznawczej kategorie i obsługuje tworzenie niestandardowych kategorii.

### <a name="store-task-logs"></a>Dzienniki zadania Store

Oprócz przechowywanie plików do trwałego magazynu po ukończeniu zadania lub zadania, możesz potrzebować do utrwalania plików, które zostały zaktualizowane podczas wykonywania zadania &mdash; pliki dziennika lub `stdout.txt` i `stderr.txt`, na przykład. W tym celu biblioteki Konwencji plików usługi Batch Azure zapewnia [TaskOutputStorage][net_taskoutputstorage].[ SaveTrackedAsync] [ net_savetrackedasync] metody. Za pomocą [SaveTrackedAsync][net_savetrackedasync], można śledzić aktualizacji do pliku na węzeł (z interwałem określonym przez użytkownika) i utrwalenia tych aktualizacji do usługi Azure Storage.

W poniższym fragmencie kodu użyto [SaveTrackedAsync] [ net_savetrackedasync] można zaktualizować `stdout.txt` w usłudze Azure Storage co 15 sekund, podczas wykonywania zadania:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
     await Task.Delay(stdoutFlushDelay);
}
```

Komentarze sekcji `Code to process data and produce output file(s)` jest symbolem zastępczym dla kodu, który zwykle wykonać zadanie. Na przykład Niewykluczone, że kod, który pobiera dane z usługi Azure Storage i wykonuje na nim transformacji lub obliczeń. Ważną częścią tego fragmentu kodu jest prezentacja, jak może zawijać się takiego kodu w `using` bloku, aby okresowo aktualizować plik z [SaveTrackedAsync][net_savetrackedasync].

Agent węzła to program, który działa w każdym węźle w puli i udostępnia interfejs poleceń i kontroli między węzłem, a usługa Batch. `Task.Delay` Wywołanie jest wymagane na końcu niniejszego `using` bloku, aby upewnić się, że agent węzła czasu, aby opróżnić zawartość standardowe wyjście pliku stdout.txt w węźle. To opóźnienie może to oznaczać przeoczyć ostatnich kilku sekund danych wyjściowych. To opóźnienie może nie być wymagana dla wszystkich plików.

> [!NOTE]
> Po włączeniu pliku ze śledzeniem **SaveTrackedAsync**, tylko *dołącza* śledzonych pliku zostaną utrwalone w usłudze Azure Storage. Metoda ta jest przydatna tylko w przypadku śledzenia-obracanie pliki dziennika lub innych plików, które zostały napisane przy użyciu operacji dołączania do końca pliku.

## <a name="retrieve-output-data"></a>Pobieranie danych wyjściowych

Po pobraniu utrwalonych danych wyjściowych za pomocą biblioteki Konwencji plików usługi Batch Azure, w tym zadania i zadania skoncentrowane na sposób. Możesz zażądać danych wyjściowych dla danego zadania lub zadania bez znajomości jego ścieżka w usłudze Azure Storage lub nawet jej nazwę pliku. Zamiast tego żądania plików wyjściowych przez zadanie lub zadanie identyfikatora.

Poniższy fragment kodu wykonuje iterację przez zadania podrzędne, drukuje pewne informacje dotyczące plików wyjściowych dla zadania, a następnie pobiera pliki z usługi Storage.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (OutputFileReference output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="view-output-files-in-the-azure-portal"></a>Wyświetl dane wyjściowe pliki w witrynie Azure portal

Witryny Azure portal Wyświetla pliki danych wyjściowych zadań i dzienników, które są zachowywane do połączonej usługi Azure Storage account using [standardowe konwencje pliku wsadowego](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). Można zaimplementować te konwencje samodzielnie w języku wybranym, lub można użyć biblioteki Konwencji plików w aplikacjach .NET.

Aby włączyć wyświetlanie plików danych wyjściowych w portalu, muszą spełniać następujące wymagania:

1. Połączyć konto usługi Azure Storage ze swoim kontem usługi Batch.
1. Gdy utrwalanie danych wyjściowych, należy stosować się do wstępnie zdefiniowanych konwencje nazewnictwa dla magazynu, kontenerów i plików. Biblioteki Konwencji plików można znaleźć definicji tych konwencji [README][github_file_conventions_readme]. Jeśli używasz [Konwencji plików usługi Batch Azure] [ nuget_package] biblioteki, aby zachować dane wyjściowe, pliki są zachowywane zgodnie ze standardem Konwencji plików.

Aby wyświetlić pliki wyjściowe zadania i dzienniki w witrynie Azure portal, przejdź do zadania, którego dane wyjściowe, które Cię interesują, następnie kliknij przycisk **zapisane pliki wyjściowe** lub **zapisane dzienniki**. Ta ilustracja przedstawia **zapisane pliki wyjściowe** dla zadania o identyfikatorze "007":

![Blok danych wyjściowych zadania w witrynie Azure portal][2]

## <a name="code-sample"></a>Przykład kodu

[PersistOutputs] [ github_persistoutputs] przykładowy projekt jest jednym z [przykładów kodu usługi Azure Batch] [ github_samples] w witrynie GitHub. To rozwiązanie programu Visual Studio pokazuje, jak zachować dane wyjściowe zadania do trwałego magazynu za pomocą biblioteki Konwencji plików usługi Batch Azure. Do uruchomienia przykładu, wykonaj następujące kroki:

1. Otwórz projekt w **programu Visual Studio 2017**.
2. Dodawanie usługi Batch i Storage **poświadczenia konta** do **AccountSettings.settings** w projekcie Microsoft.Azure.Batch.Samples.Common.
3. **Tworzenie** (ale nie należy uruchamiać) to rozwiązanie. Jeśli zostanie wyświetlony monit, należy przywrócić wszystkie pakiety NuGet.
4. Użyj witryny Azure portal, aby przekazać [pakiet aplikacji](batch-application-packages.md) dla **PersistOutputsTask**. Obejmują `PersistOutputsTask.exe` i ustaw jego zestawów zależnych w pakiecie .zip, identyfikator aplikacji, aby "PersistOutputsTask" i "1.0" wersja pakietu aplikacji.
5. **Rozpocznij** (Uruchom) **PersistOutputs** projektu.
6. Po wyświetleniu monitu wybierz technologię stanów trwałych, można użyć do uruchamiania przykładu, należy wprowadzić **1** do uruchomienia przykładu, aby zachować dane wyjściowe zadania za pomocą biblioteki Konwencji plików. 

## <a name="next-steps"></a>Kolejne kroki

### <a name="get-the-batch-file-conventions-library-for-net"></a>Pobierz biblioteki Konwencji plików usługi Batch dla platformy .NET

Biblioteki Konwencji plików usługi Batch dla platformy .NET jest dostępna w [NuGet][nuget_package]. Rozszerza biblioteki [CloudJob] [ net_cloudjob] i [CloudTask] [ net_cloudtask] klas z nowych metod. Zobacz też [dokumentację referencyjną](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files) biblioteki Konwencji plików.

[Kod źródłowy] [ github_file_conventions] Konwencji plików biblioteki jest dostępna w usługi GitHub na platformie Microsoft Azure SDK dla platformy .NET repozytorium. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Zapoznaj się z innych metod utrwalanie danych wyjściowych

- Zobacz [utrwalanie danych wyjściowych zadań i zadań w usłudze Azure Storage](batch-task-output.md) omówienie utrwalanie danych zadań i zadań.
- Zobacz [utrwalanie zadań dane do usługi Azure Storage za pomocą usługi Batch usług interfejsu API](batch-task-output-files.md) dowiesz się, jak używać interfejsu API usługi Batch do utrwalenia danych wyjściowych.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Zapisane pliki wyjściowe i zapisane dzienniki selektory w portalu"
[2]: ./media/batch-task-output/task-output-02.png "Blok danych wyjściowych zadania w witrynie Azure portal"
