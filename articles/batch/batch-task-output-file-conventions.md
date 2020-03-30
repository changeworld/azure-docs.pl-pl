---
title: Utrwalanie danych wyjściowych w usłudze Azure Storage za pomocą biblioteki konwencj plików platformy .NET — usługa Azure Batch
description: Dowiedz się, jak używać biblioteki konwencje plików wsadowych platformy Azure dla platformy .NET do utrwalania zadania usługi Batch & danych wyjściowych zadania do usługi Azure Storage i wyświetlania tego danych wyjściowych w witrynie Azure portal.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 693017e529f2869c16d94c30cdf48ec228df3276
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022872"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>Utrwalanie danych zadania i zadania w usłudze Azure Storage za pomocą biblioteki Konwencje plików wsadowych dla platformy .NET

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Jednym ze sposobów utrwalania danych zadań jest użycie [biblioteki konwencje plików wsadowych platformy Azure dla platformy .NET.][nuget_package] Biblioteka Konwencje plików upraszcza proces przechowywania danych wyjściowych zadań w usłudze Azure Storage i pobierania ich. Biblioteka Konwencje plików służy do tworzenia &mdash; kodu zadania i klienta w kodzie zadania do utrwalania plików oraz w kodzie klienta, aby je wyświetlić i pobrać. Kod zadania można również użyć biblioteki do pobierania danych wyjściowych zadań nadrzędnych, takich jak w scenariuszu [zależności zadań.](batch-task-dependencies.md)

Aby pobrać pliki wyjściowe z biblioteką Konwencje plików, można zlokalizować pliki dla danego zadania lub zadania, wymieniając je według identyfikatora i celu. Nie musisz znać nazw ani lokalizacji plików. Na przykład można użyć biblioteki Konwencje plików, aby wyświetlić listę wszystkich plików pośrednich dla danego zadania lub uzyskać plik podglądu dla danego zadania.

> [!TIP]
> Począwszy od wersji 2017-05-01 interfejs API usługi Batch obsługuje utrwalanie danych wyjściowych do usługi Azure Storage dla zadań i zadań menedżera zadań, które są uruchamiane w pulach utworzonych za pomocą konfiguracji maszyny wirtualnej. Interfejs API usługi wsadowej zapewnia prosty sposób utrwalania danych wyjściowych z poziomu kodu, który tworzy zadanie i służy jako alternatywa dla biblioteki Konwencje plików. Można zmodyfikować aplikacje klienckie batch do utrwalania danych wyjściowych bez konieczności aktualizowania aplikacji, która jest uruchomiona zadanie. Aby uzyskać więcej informacji, zobacz [Utrwalanie danych zadań do usługi Azure Storage za pomocą interfejsu API usługi wsadowej](batch-task-output-files.md).

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Kiedy muszę używać biblioteki Konwencje plików do utrwalania danych wyjściowych zadań?

Usługa Azure Batch udostępnia więcej niż jeden sposób utrwalania danych wyjściowych zadań. Konwencje plików najlepiej nadaje się do tych scenariuszy:

- Można łatwo zmodyfikować kod aplikacji, która jest uruchomiona zadanie do utrwalania plików przy użyciu biblioteki Konwencje plików.
- Chcesz przesyłać strumieniowo dane do usługi Azure Storage, gdy zadanie jest nadal uruchomione.
- Chcesz utrwalić dane z pul utworzonych za pomocą konfiguracji usługi w chmurze lub konfiguracji maszyny wirtualnej.
- Aplikacja kliencka lub inne zadania w zadaniu musi zlokalizować i pobrać pliki wyjściowe zadania według identyfikatora lub celu.
- Chcesz wyświetlić dane wyjściowe zadań w witrynie Azure portal.

Jeśli scenariusz różni się od wymienionych powyżej, może być konieczne rozważenie innego podejścia. Aby uzyskać więcej informacji na temat innych opcji utrwalania danych wyjściowych zadań, zobacz [Utrwalanie zadania i danych wyjściowych zadań w usłudze Azure Storage.](batch-task-output.md)

## <a name="what-is-the-batch-file-conventions-standard"></a>Co to jest standard konwencji plików wsadowych?

[Standard Konwencje plików wsadowych](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files) zawiera schemat nazewnictwa kontenerów docelowych i ścieżek obiektów blob, do których są zapisywane pliki wyjściowe. Pliki utrwalone w usłudze Azure Storage, które są zgodne ze standardem Konwencje plików, są automatycznie dostępne do wyświetlania w witrynie Azure portal. Portal jest świadomy konwencji nazewnictwa i tak może wyświetlać pliki, które są do niej zgodne.

Biblioteka Konwencje plików dla platformy .NET automatycznie nazywa kontenery magazynu i pliki wyjściowe zadań zgodnie ze standardem Konwencje plików. Biblioteka Konwencje plików zawiera również metody do wykonywania zapytań plików wyjściowych w usłudze Azure Storage zgodnie z identyfikatorem zadania, identyfikatorem zadania lub celem.

Jeśli tworzysz z języka innego niż .NET, można zaimplementować standard konwencje plików samodzielnie w aplikacji. Aby uzyskać więcej informacji, zobacz [Implementowanie standardu Konwencje plików wsadowych](batch-task-output.md#implement-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Łączenie konta usługi Azure Storage z kontem usługi Batch

Aby utrwalić dane wyjściowe do usługi Azure Storage przy użyciu biblioteki konwencje plików, należy najpierw połączyć konto usługi Azure Storage z kontem usługi Batch. Jeśli jeszcze tego nie zrobiono, połącz konto magazynu z kontem usługi Batch przy użyciu [witryny Azure portal:](https://portal.azure.com)

1. W witrynie Azure Portal przejdź do swojego konta usługi Batch.
1. W obszarze **Ustawienia**wybierz pozycję **Konto magazynu**.
1. Jeśli z kontem usługi Batch nie jest jeszcze skojarzone konto magazynu, kliknij pozycję **Konto magazynu (Brak)**.
1. Wybierz konto magazynu z listy subskrypcji. Aby uzyskać najlepszą wydajność, należy użyć konta usługi Azure Storage, które znajduje się w tym samym regionie co konto usługi Batch, na którym są uruchomione zadania.

## <a name="persist-output-data"></a>Utrwalanie danych wyjściowych

Aby utrwalić dane wyjściowe zadania i zadania za pomocą biblioteki Konwencje plików, utwórz kontener w usłudze Azure Storage, a następnie zapisz dane wyjściowe w kontenerze. Użyj [biblioteki klienta usługi Azure Storage dla platformy .NET](https://www.nuget.org/packages/WindowsAzure.Storage) w kodzie zadania, aby przekazać dane wyjściowe zadania do kontenera.

Aby uzyskać więcej informacji na temat pracy z kontenerami i obiektami blob w usłudze Azure Storage, zobacz [Wprowadzenie do usługi Azure Blob Storage przy użyciu platformy .NET.](../storage/blobs/storage-dotnet-how-to-use-blobs.md)

> [!WARNING]
> Wszystkie dane wyjściowe zadań i zadań utrwalone z biblioteki Konwencje plików są przechowywane w tym samym kontenerze. Jeśli duża liczba zadań próbuje utrwalić pliki w tym samym czasie, limity ograniczania przepustowości usługi Azure Storage mogą być wymuszane. Aby uzyskać więcej informacji na temat ograniczania przepustowości, zobacz [Lista kontrolna wydajności i skalowalności magazynu obiektów Blob](../storage/blobs/storage-performance-checklist.md).

### <a name="create-storage-container"></a>Tworzenie kontenera magazynu

Aby utrwalić dane wyjściowe zadania w usłudze Azure Storage, należy najpierw utworzyć kontener, wywołując [usługę CloudJob][net_cloudjob]. [PrzygotowanieOutputStorageAsync][net_prepareoutputasync]. Ta metoda rozszerzenia przyjmuje [CloudStorageAccount][net_cloudstorageaccount] obiektu jako parametr. Tworzy kontener o nazwie zgodnie ze standardem Konwencje plików, dzięki czemu jego zawartość są wykrywalne przez witrynę Azure portal i metody pobierania omówione w dalszej części artykułu.

Zazwyczaj należy umieścić kod, aby utworzyć kontener &mdash; w aplikacji klienckiej aplikacji, która tworzy pule, zadania i zadania.

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

### <a name="store-task-outputs"></a>Przechowywanie wyjść zadań

Teraz, gdy masz przygotowany kontener w usłudze Azure Storage, zadania można zapisać dane wyjściowe do kontenera przy użyciu [TaskOutputStorage][net_taskoutputstorage] klasy znalezionej w bibliotece konwencje plików.

W kodzie zadania najpierw utwórz obiekt [TaskOutputStorage,][net_taskoutputstorage] a następnie po zakończeniu pracy zadania, wywołanie [taskoutputStorage][net_taskoutputstorage]. [SaveAsync][net_saveasync] metoda, aby zapisać swoje dane wyjściowe w usłudze Azure Storage.

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

Parametr `kind` [TaskOutputStorage](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage). [SaveAsync](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync#overloads) Metoda kategoryzuje utrwalone pliki. Istnieją cztery wstępnie zdefiniowane typy [TaskOutputKind:][net_taskoutputkind] `TaskOutput`, `TaskPreview`, `TaskLog`i `TaskIntermediate.` można również zdefiniować niestandardowe kategorie danych wyjściowych.

Te typy danych wyjściowych umożliwiają określenie, jaki typ danych wyjściowych ma być wyszcze ominięty podczas późniejszego wykonywania kwerendy batch dla utrwalonych wyjść danego zadania. Innymi słowy podczas listy wyjść dla zadania, można filtrować listę na jednym z typów danych wyjściowych. Na przykład "Daj mi dane wyjściowe *podglądu* dla zadania *109*." Więcej informacji na temat aukcji i pobierania danych wyjściowych pojawia się w Pobierz dane wyjściowe w dalszej części artykułu.

> [!TIP]
> Rodzaj danych wyjściowych określa również, gdzie w portalu Azure pojawia się określony plik: *TaskOutput*-categorized files appear under **Task output files**, and *TaskLog* files appear under **Task logs**.

### <a name="store-job-outputs"></a>Przechowywanie wyjść z zadań

Oprócz przechowywania wyjść zadań można przechowywać dane wyjściowe skojarzone z całym zadaniem. Na przykład w zadaniu scalania zadania renderowania filmu można utrwalić w pełni renderowany film jako dane wyjściowe zadania. Po zakończeniu zadania aplikacja kliencka może wyświetlić listę i pobrać dane wyjściowe dla zadania i nie musi wykonywać zapytań o poszczególne zadania.

Dane wyjściowe zadania magazynu, wywołując [joboutputStorage][net_joboutputstorage]. [SaveAsync][net_joboutputstorage_saveasync] metody i określić [JobOutputKind][net_joboutputkind] i nazwę pliku:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Podobnie jak w przypadku **taskOutputKind** typu dla wyjść zadań, należy użyć [JobOutputKind][net_joboutputkind] typu do kategoryzowanie zadań utrwalonych plików. Ten parametr umożliwia późniejsze zapytanie o (listę) określonego typu danych wyjściowych. **JobOutputKind** typ zawiera zarówno dane wyjściowe i podgląd kategorii i obsługuje tworzenie kategorii niestandardowych.

### <a name="store-task-logs"></a>Przechowywanie dzienników zadań

Oprócz utrwalania pliku do trwałego magazynu po zakończeniu zadania lub zadania może być konieczne utrwalenie plików, które są aktualizowane podczas wykonywania plików dziennika zadań &mdash; lub `stdout.txt` `stderr.txt`, na przykład. W tym celu biblioteka konwencje plików wsadowych platformy Azure udostępnia [program TaskOutputStorage][net_taskoutputstorage]. [SaveTrackedAsync][net_savetrackedasync] metoda. Z [SaveTrackedAsync][net_savetrackedasync], można śledzić aktualizacje pliku w węźle (w określonym przedziale czasu) i utrwalić te aktualizacje usługi Azure Storage.

We wliczeniu kodu poniżej używamy [savetrackedasync][net_savetrackedasync] do aktualizowania `stdout.txt` w usłudze Azure Storage co 15 sekund podczas wykonywania zadania:

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

Skomentowana `Code to process data and produce output file(s)` sekcja jest symbolem zastępczym dla kodu, który zwykle wykonuje zadanie. Na przykład może mieć kod, który pobiera dane z usługi Azure Storage i wykonuje transformację lub obliczenia na nim. Ważną częścią tego fragmentu kodu jest pokazanie, jak można `using` zawinąć taki kod w bloku, aby okresowo aktualizować plik za pomocą [programu SaveTrackedAsync][net_savetrackedasync].

Agent węzła jest programem, który działa na każdym węźle w puli i zapewnia interfejs polecenia i kontroli między węzłem a usługą Batch. Wywołanie `Task.Delay` jest wymagane na końcu `using` tego bloku, aby upewnić się, że agent węzła ma czas, aby opróżnić zawartość standardu do pliku stdout.txt w węźle. Bez tego opóźnienia można przegapić ostatnie kilka sekund wyjścia. Opóźnienie to może nie być wymagane dla wszystkich plików.

> [!NOTE]
> Po włączeniu śledzenia plików za pomocą **savetrackedAsync,** tylko *dołącza* do śledzonego pliku są utrwalone w usłudze Azure Storage. Ta metoda służy tylko do śledzenia nieobrotowych plików dziennika lub innych plików, które są zapisywane z operacjami dołączania na końcu pliku.

## <a name="retrieve-output-data"></a>Pobieranie danych wyjściowych

Po pobraniu utrwalonych danych wyjściowych przy użyciu biblioteki konwencji plików wsadowych platformy Azure, można to zrobić w sposób zorientowany na zadania i zadania. Można zażądać danych wyjściowych dla danego zadania lub zadania bez konieczności znać jego ścieżki w usłudze Azure Storage, a nawet jego nazwę pliku. Zamiast tego można zażądać plików wyjściowych według identyfikatora zadania lub zadania.

Poniższy fragment kodu iteruje za pośrednictwem zadań zadania, drukuje niektóre informacje o plikach wyjściowych dla zadania, a następnie pobiera jego pliki z magazynu.

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

## <a name="view-output-files-in-the-azure-portal"></a>Wyświetlanie plików wyjściowych w witrynie Azure portal

Portal Azure zawiera pliki wyjściowe zadań i dzienniki, które są utrwalone na połączonym koncie usługi Azure Storage przy użyciu [standardu Konwencje plików wsadowych.](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) Te konwencje można zaimplementować samodzielnie w wybranym języku lub biblioteki Konwencje plików w aplikacjach platformy .NET.

Aby włączyć wyświetlanie plików wyjściowych w portalu, należy spełnić następujące wymagania:

1. Połącz konto usługi Azure Storage z kontem usługi Batch.
1. Przestrzegaj wstępnie zdefiniowanych konwencji nazewnictwa kontenerów magazynu i plików podczas utrwalania danych wyjściowych. Definicję tych konwencji można znaleźć w bibliotece konwencji plików [README][github_file_conventions_readme]. Jeśli używasz biblioteki [konwencje plików wsadowych platformy Azure][nuget_package] do utrwalenia danych wyjściowych, pliki są utrwalone zgodnie ze standardem Konwencje plików.

Aby wyświetlić pliki wyjściowe zadań i dzienniki w portalu Azure, przejdź do zadania, którego dane wyjściowe Cię interesują, a następnie kliknij pozycję **Zapisane pliki wyjściowe** lub **Zapisane dzienniki**. Ten obraz przedstawia **zapisane pliki wyjściowe** dla zadania o identyfikatorze "007":

![Blok wyjścia zadań w witrynie Azure portal][2]

## <a name="code-sample"></a>Przykład kodu

Przykładowy projekt [PersistOutputs][github_persistoutputs] jest jednym z [przykładowych przykładów kodu usługi Azure Batch][github_samples] w usłudze GitHub. To rozwiązanie programu Visual Studio pokazuje, jak używać biblioteki konwencje plików wsadowych platformy Azure do utrwalania danych wyjściowych zadań do trwałego magazynu. Aby uruchomić próbkę, wykonaj następujące kroki:

1. Otwórz projekt w **programie Visual Studio 2019**.
2. Dodaj **poświadczenia konta** usługi Batch i Storage do **accountSettings.settings** w projekcie Microsoft.Azure.Batch.Samples.Common.
3. **Kompilacja** (ale nie uruchamiaj) rozwiązania. Przywróć wszystkie pakiety NuGet, jeśli zostanie wyświetlony monit.
4. Użyj witryny Azure portal, aby przekazać [pakiet aplikacji](batch-application-packages.md) dla **PersistOutputsTask**. Dołącz `PersistOutputsTask.exe` i jego zestawy zależne w pakiecie .zip, ustaw identyfikator aplikacji na "PersistOutputsTask", a wersję pakietu aplikacji na "1.0".
5. **Uruchom** (uruchom) **persistoutputs** projektu.
6. Po wyświetleniu monitu o wybranie technologii trwałości do uruchomienia próbki wprowadź **1,** aby uruchomić próbkę przy użyciu biblioteki Konwencje plików, aby utrwalić dane wyjściowe zadania. 

## <a name="next-steps"></a>Następne kroki

### <a name="get-the-batch-file-conventions-library-for-net"></a>Pobierz bibliotekę Konwencje plików wsadowych dla platformy .NET

Biblioteka Konwencje plików wsadowych dla platformy .NET jest dostępna w programie [NuGet][nuget_package]. Biblioteka rozszerza [CloudJob][net_cloudjob] i [CloudTask][net_cloudtask] klasy z nowymi metodami. Zobacz też [dokumentację referencyjną](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files) biblioteki Konwencje plików.

[Kod źródłowy][github_file_conventions] biblioteki Konwencje plików jest dostępny w usłudze GitHub w pakiecie Microsoft Azure SDK dla repozytorium .NET. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Poznaj inne podejścia do utrwalania danych wyjściowych

- Zobacz [Utrwalanie danych wyjściowych zadania i zadań w usłudze Azure Storage, aby](batch-task-output.md) uzyskać omówienie utrwalania danych zadań i zadań.
- Zobacz [Utrwalanie danych zadań do usługi Azure Storage za pomocą interfejsu API usługi wsadowej,](batch-task-output-files.md) aby dowiedzieć się, jak używać interfejsu API usługi wsadowej do utrwalania danych wyjściowych.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage._cloud_storage_account
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
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

[1]: ./media/batch-task-output/task-output-01.png "Zapisane pliki wyjściowe i selektory zapisanych dzienników w portalu"
[2]: ./media/batch-task-output/task-output-02.png "Blok wyjścia zadań w witrynie Azure portal"
