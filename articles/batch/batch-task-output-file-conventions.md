---
title: Utrwalanie i zadań dane wyjściowe do usługi Azure Storage z biblioteką konwencje plików dla platformy .NET — partii zadań Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać biblioteki konwencje plików usługi partia zadań Azure dla platformy .NET do utrwalenia partii zadań i zadania dane wyjściowe do magazynu Azure i wyświetlić utrwalonych danych wyjściowych w portalu Azure.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bbfb40b3740f9ea43df327a01ba6f4cf52d80457
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net-to-persist"></a>Utrwalić danych i zadań do magazynu Azure za pomocą biblioteki konwencje pliku wsadowego dla platformy .NET utrwalić 

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Jednym ze sposobów utrwalenia danych zadań jest użycie [biblioteki konwencje plików usługi partia zadań Azure dla platformy .NET][nuget_package]. Biblioteki konwencje plików upraszcza proces dane wyjściowe zadania do magazynu Azure do przechowywania i pobierania jej. Biblioteki konwencje plików w kodzie zarówno zadania, jak i klienta można używać &mdash; kod zadania utrwalanie plików i kod klienta do listy i pobierania ich. Kod zadania umożliwia także biblioteki można pobrać dane wyjściowe zadania nadrzędnego, takich jak w [zadań zależności](batch-task-dependencies.md) scenariusza. 

Aby pobrać pliki wyjściowe z biblioteką konwencje plików, można znaleźć plików dla danego zadania lub zadania wystawiając według Identyfikatora i celu. Nie trzeba znać nazwy i lokalizacje plików. Można na przykład korzystania z biblioteki konwencje plików, aby wyświetlić listę wszystkich plików pośrednich dla danego zadania lub Pobierz plik podglądu dla danego zadania.

> [!TIP]
> Począwszy od wersji 2017-05-01, interfejs API usługi partii obsługuje trwałych danych wyjściowych do magazynu Azure do wykonywania zadań i zadania Menedżer zadania, które będą uruchamiane w pulach utworzone za pomocą konfiguracji maszyny wirtualnej. Usługi interfejsu API partii zapewnia prosty sposób, aby zachować dane wyjściowe z kodem, tworzy zadanie, który stanowi alternatywę dla biblioteki konwencje plików. Można zmodyfikować partii aplikacji klienta do utrwalenia danych wyjściowych bez konieczności aktualizacji aplikacji, która działa zadania. Aby uzyskać więcej informacji, zobacz [utrwalanie zadań dane do magazynu Azure z instancją usług interfejsu API](batch-task-output-files.md).
> 
> 

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Kiedy używać biblioteki konwencje plików do utrwalenia danych wyjściowych zadania?

Partia zadań Azure zawiera więcej niż jeden sposób, aby zachować dane wyjściowe zadania. Konwencje plików najlepiej nadaje się do tych scenariuszy:

- Można łatwo zmodyfikować kod aplikacji, która działa zadanie do utrwalenia plików za pomocą biblioteki konwencje plików.
- Chcesz strumienia danych do magazynu Azure, podczas gdy zadanie jest uruchomione.
- Chcesz zachować dane z zestawów utworzonych za pomocą konfiguracji usługi w chmurze lub konfiguracji maszyny wirtualnej.
- Aplikacja kliencka lub innych zadań w zadaniu musi znaleźć i pobrać pliki wyjściowe zadań według Identyfikatora lub cel. 
- Chcesz wyświetlić dane wyjściowe zadania w portalu Azure.

Jeśli scenariusz różni się od wymienione powyżej, może być konieczne należy wziąć pod uwagę różne podejścia. Aby uzyskać więcej informacji dotyczących innych opcji trwałych danych wyjściowych zadania, zobacz [utrwalić danych wyjściowych i zadań do magazynu Azure](batch-task-output.md). 

## <a name="what-is-the-batch-file-conventions-standard"></a>Co to jest standardowe konwencje pliku wsadowego?

[Standard konwencje pliku wsadowego](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) zawiera schemat nazewnictwa dla docelowego kontenery i ścieżki obiektów blob, do których zapisu plików wyjściowych. Pliki utrwalone w magazynie Azure, z ze standardem konwencje plików są automatycznie dostępne do wyświetlenia w portalu Azure. Portal zna konwencji nazewnictwa i dlatego można wyświetlać pliki, które stosować się do niego.

Biblioteka konwencje plików dla platformy .NET automatycznie nazwy kontenery magazynu, a pliki wyjściowe zadań zgodnie z standardowej konwencji pliku. Biblioteka konwencje plików udostępnia również metody służące do badania plików wyjściowych w usłudze Azure Storage według Identyfikatora zadania, identyfikator zadania lub cel.   

Jeśli tworzysz języka innego niż .NET, można zaimplementować standardowej konwencji pliku samodzielnie w aplikacji. Aby uzyskać więcej informacji, zobacz [standard o konwencje pliku wsadowego](batch-task-output.md#about-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Łączenie konta magazynu Azure do konta partii zadań

Aby zachować dane wyjściowe do magazynu Azure za pomocą biblioteki konwencje plików, możesz połączyć konto magazynu Azure do konta partii zadań. Jeśli jeszcze tego nie zrobiono tego wcześniej, Połącz konto magazynu do konta partii zadań za pomocą [portalu Azure](https://portal.azure.com):

1. W witrynie Azure Portal przejdź do swojego konta usługi Batch. 
2. W obszarze **ustawienia**, wybierz pozycję **konta magazynu**.
3. Jeśli nie masz już konto magazynu skojarzonych z Twoim kontem usługi partia zadań, kliknij przycisk **konta magazynu, (Brak)**.
4. Wybierz konto magazynu z listy dla Twojej subskrypcji. Aby uzyskać najlepszą wydajność należy użyć konta usługi Azure Storage, który znajduje się w tym samym regionie co konta usługi partia zadań, na którym są uruchomione zadania.

## <a name="persist-output-data"></a>Utrwalić danych wyjściowych

Aby zachować dane wyjściowe poleceń i zadań z biblioteką konwencje plików, utworzyć kontener w usłudze Azure Storage, a następnie zapisz dane wyjściowe do kontenera. Użyj [biblioteki klienta usługi Azure Storage dla platformy .NET](https://www.nuget.org/packages/WindowsAzure.Storage) w kodzie zadań, aby przekazać dane wyjściowe zadania do kontenera. 

Aby uzyskać więcej informacji na temat pracy z kontenerów i obiektów blob w magazynie Azure, zobacz [Rozpoczynanie pracy z magazynem obiektów Blob platformy Azure przy użyciu platformy .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

> [!WARNING]
> Wszystkie dane wyjściowe poleceń i zadań utrwalonych z konwencjami pliku biblioteki są przechowywane w tym samym kontenerze. Duża liczba zadań próby utrwalić pliki w tym samym czasie [magazynu ograniczenie](../storage/common/storage-performance-checklist.md#blobs) może wtedy zostać wymuszone.
> 
> 

### <a name="create-storage-container"></a>Tworzenie kontenera magazynu

Aby zachować dane wyjściowe zadania do magazynu Azure, najpierw utworzyć kontener przez wywołanie metody [CloudJob][net_cloudjob].[ PrepareOutputStorageAsync][net_prepareoutputasync]. Ta metoda rozszerzenia przyjmuje [CloudStorageAccount] [ net_cloudstorageaccount] obiektu jako parametr. Tworzy kontener o nazwie zgodnie ze standardowej konwencji plik tak, aby jego zawartość jest łatwy w portalu Azure i metod pobierania omówiony w dalszej części tego artykułu.

Zwykle umieścisz kod, aby utworzyć kontener w aplikacji klienta &mdash; aplikacji, która tworzy z pul, zadań i zadań.

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

### <a name="store-task-outputs"></a>Zadanie magazynu danych wyjściowych

Teraz, gdy zostały przygotowane kontenera w magazynie Azure, zadania można zapisać danych wyjściowych do kontenera przy użyciu [TaskOutputStorage] [ net_taskoutputstorage] klasy znaleziony w bibliotece konwencje plików.

W kodzie zadań, należy najpierw utworzyć [TaskOutputStorage] [ net_taskoutputstorage] obiekt, a następnie wywołaj po ukończeniu pracy zadania [TaskOutputStorage][net_taskoutputstorage].[ SaveAsync] [ net_saveasync] metodę, aby zapisać dane wyjściowe do magazynu Azure.

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

`kind` Parametr [TaskOutputStorage](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx).[ SaveAsync](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx) metody kategoryzuje utrwalonego plików. Istnieją cztery wstępnie zdefiniowane [TaskOutputKind] [ net_taskoutputkind] typów: `TaskOutput`, `TaskPreview`, `TaskLog`, i `TaskIntermediate.` można również definiować niestandardowe kategorie danych wyjściowych.

Te typy danych wyjściowych pozwala określić typ danych wyjściowych, aby wyświetlić listę później kwerendy partii dla utrwalonych danych wyjściowych z danego zadania. Innymi słowy gdy listy danych wyjściowych zadania, można filtrować listy na jeden z typów danych wyjściowych. Na przykład "Udostępnij *Podgląd* dane wyjściowe zadania *109*." Więcej informacji na temat wyświetlania i pobierania danych wyjściowych jest wyświetlana w [pobrać dane wyjściowe](#retrieve-output) dalszej części tego artykułu.

> [!TIP]
> Rodzaj wyjścia Określa, gdzie w portalu Azure określony plik jest wyświetlany: *TaskOutput*-skategoryzowane pliki są wyświetlane w obszarze **pliki wyjściowe zadania**, i *TaskLog* pliki są wyświetlane w obszarze **zadań dzienniki**.
> 
> 

### <a name="store-job-outputs"></a>Dane wyjściowe zadania magazynu

Oprócz przechowywania danych wyjściowych zadania, można przechowywać dane wyjściowe skojarzone z całego zadania. Na przykład w zadaniu scalania zadania renderowania movie, można utrwalić pełni renderowanego filmu jako dane wyjściowe zadania. Po zakończeniu zadania aplikacji klienta, można wyświetlić listę i pobrać dane wyjściowe do zadania i nie wymaga się zapytanie o poszczególnych zadań.

Przechowywanie danych wyjściowych zadania, wywołując [JobOutputStorage][net_joboutputstorage].[ SaveAsync] [ net_joboutputstorage_saveasync] metody, a następnie określ [JobOutputKind] [ net_joboutputkind] i nazwa pliku:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Jak **TaskOutputKind** typu dla danych wyjściowych zadania, możesz użyć [JobOutputKind] [ net_joboutputkind] typu kategoryzację zadania przez utrwalone plików. Ten parametr umożliwia kwerendy dla określonego typu danych wyjściowych (lista). **JobOutputKind** typu kategoriami zarówno dane wyjściowe, jak i w wersji zapoznawczej i obsługuje tworzenie niestandardowych kategorii.

### <a name="store-task-logs"></a>Dzienniki zadania magazynu

Oprócz przechowywanie plików do magazynu trwałego po zakończeniu zadania lub zadania, konieczne może być utrwalić pliki, które zostały zaktualizowane podczas wykonywania zadania &mdash; pliki dziennika lub `stdout.txt` i `stderr.txt`, na przykład. W tym celu Biblioteka konwencje pliku wsadowego Azure udostępnia [TaskOutputStorage][net_taskoutputstorage].[ SaveTrackedAsync] [ net_savetrackedasync] metody. Z [SaveTrackedAsync][net_savetrackedasync], można śledzić aktualizacji do pliku na węzeł (w przedziale czasu, który określisz) i zachować tych aktualizacji do magazynu Azure.

W poniższy fragment kodu używamy [SaveTrackedAsync] [ net_savetrackedasync] zaktualizować `stdout.txt` w usłudze Azure Storage co 15 s podczas wykonywania zadania:

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

Komentarze sekcji `Code to process data and produce output file(s)` jest symbolem zastępczym dla kodu, który przeprowadza się zwykle zadania. Na przykład może być kodu, który pobiera dane z usługi Azure Storage i wykonuje przekształcenie lub obliczeń na nim. Ważnym elementem ta Wstawka kodu jest prezentacja, jak może zawijać się taki kod w `using` bloku celu okresowej aktualizacji pliku z [SaveTrackedAsync][net_savetrackedasync].

Agent węzła jest program, który jest uruchamiany na każdym węźle w puli i udostępnia interfejs polecenia i kontroli między węzłem a usługa partia zadań. `Task.Delay` Wywołanie jest wymagane na końcu niniejszego `using` bloku, aby upewnić się, że agent węzeł ma czas, aby opróżnić zawartość poza standardowe do pliku stdout.txt w węźle. To opóźnienie może to oznaczać sygnałów ostatniego kilka sekund danych wyjściowych. Tego opóźnienia nie może być wymagane dla wszystkich plików.

> [!NOTE]
> Po włączeniu pliku śledzenia z **SaveTrackedAsync**, tylko *dołącza* śledzonych pliku są zachowywane do magazynu Azure. Ta metoda służy tylko do śledzenia-obracanie pliki dziennika lub innych plików, które zostały napisane z operacji dołączania na końcu pliku.
> 
> 

## <a name="retrieve-output-data"></a>Pobieranie danych wyjściowych

Podczas pobierania danych wyjściowych utrwalonego za pomocą biblioteki konwencje pliku wsadowego Azure, w tym zadań i zadania skoncentrowane na sposób. Można zażądać danych wyjściowych dla danego zadania lub zadania bez dokładnej znajomości ścieżki w usłudze Azure Storage lub nawet jego nazwa pliku. Zamiast tego można zażądać plików wyjściowych przez zadanie lub zadania identyfikatora.

Poniższy fragment kodu iteruje zadania, drukuje niektóre informacje na temat plików wyjściowych dla zadania i pobiera pliki z magazynu.

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

## <a name="view-output-files-in-the-azure-portal"></a>Pliki wyjściowe widoku w portalu Azure

Azure portal Wyświetla pliki wyjściowe zadań i konta przy użyciu dzienników, które są trwałe do połączonej usługi Azure Storage [standard konwencje pliku wsadowego](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). Można zaimplementować te konwencje samodzielnie w języku wybranym, lub można użyć biblioteki konwencje plików w aplikacji platformy .NET.

Aby włączyć wyświetlanie plików wyjściowych w portalu, musi spełniać następujące wymagania:

1. [Łączenie konta usługi Azure Storage](#requirement-linked-storage-account) do konta partii zadań.
2. Gdy przechowywanie danych wyjściowych, należy stosować się do wstępnie zdefiniowanych konwencje nazewnictwa dla kontenerów magazynów i plików. W bibliotece konwencje plików można znaleźć definicji tych konwencji [README][github_file_conventions_readme]. Jeśli używasz [konwencje pliku wsadowego Azure] [ nuget_package] biblioteki, aby zachować dane wyjściowe, pliki są zachowywane zgodnie ze standardowej konwencji pliku.

Aby wyświetlić dzienniki i pliki wyjściowe zadania w portalu Azure, przejdź do zadań, której wyjście myślisz, następnie kliknij opcję **zapisane pliki wyjściowe** lub **zapisane dzienniki**. Ten obraz zawiera **zapisane pliki wyjściowe** dla zadania o identyfikatorze "007":

![Blok danych wyjściowych zadania w portalu Azure][2]

## <a name="code-sample"></a>Przykład kodu

[PersistOutputs] [ github_persistoutputs] przykładowy projekt jest jednym z [przykłady kodu partii zadań Azure] [ github_samples] w witrynie GitHub. To rozwiązanie Visual Studio pokazuje, jak przy użyciu biblioteki konwencje pliku wsadowego Azure do utrwalenia danych wyjściowych zadania do magazynu trwałego. Aby uruchomić przykład, wykonaj następujące kroki:

1. Otwórz projekt w **programu Visual Studio 2015 lub nowsza**.
2. Dodaj wsadowego i magazynowania **poświadczenia konta** do **AccountSettings.settings** w projekcie Microsoft.Azure.Batch.Samples.Common.
3. **Tworzenie** (ale nie jest uruchomiony) rozwiązania. Jeśli zostanie wyświetlony monit, należy przywrócić wszystkie pakiety NuGet.
4. Użyj portalu Azure, aby przekazać [pakiet aplikacji](batch-application-packages.md) dla **PersistOutputsTask**. Obejmują `PersistOutputsTask.exe` i jego zestawów zależnych pakietu ZIP, Ustaw identyfikator aplikacji na "PersistOutputsTask" i "1.0" wersja pakietu aplikacji.
5. **Uruchom** (Uruchom) **PersistOutputs** projektu.
6. Po wyświetleniu monitu wybierz technologię trwałości tak, aby użyć do uruchomienia przykładu, wprowadź **1** uruchamianie przykładowych za pomocą biblioteki konwencje plików do utrwalenia danych wyjściowych zadania. 

## <a name="next-steps"></a>Kolejne kroki

### <a name="get-the-batch-file-conventions-library-for-net"></a>Pobierz biblioteki konwencje pliku wsadowego dla platformy .NET

Jest dostępna w bibliotece konwencje pliku wsadowego dla platformy .NET [NuGet][nuget_package]. Rozszerza biblioteki [CloudJob] [ net_cloudjob] i [CloudTask] [ net_cloudtask] klas z nowych metod. Zobacz też [odwołania dokumentacji](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files) biblioteki konwencje plików.

[Kod źródłowy] [ github_file_conventions] konwencje plik biblioteki jest dostępna w witrynie GitHub w zestaw Microsoft Azure SDK dla platformy .NET repozytorium. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Eksploruj inne podejścia trwałych danych wyjściowych

- Zobacz [utrwalić danych wyjściowych i zadań do magazynu Azure](batch-task-output.md) omówienie trwałych danych zadań i zadania.
- Zobacz [utrwalanie zadań dane do magazynu Azure z instancją usług interfejsu API](batch-task-output-files.md) Aby dowiedzieć się, jak używać usługi interfejsu API partii do utrwalenia danych wyjściowych.

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
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Pliki wyjściowe zapisane i zapisane dzienniki selektory w portalu"
[2]: ./media/batch-task-output/task-output-02.png "Blok danych wyjściowych zadania w portalu Azure"
