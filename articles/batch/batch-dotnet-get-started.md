---
title: Samouczek — korzystanie z biblioteki klienta usługi Azure Batch dla programu .NET | Microsoft Docs
description: Podstawowe pojęcia dotyczące usługi Azure Batch i tworzenie prostego rozwiązania przy użyciu platformy .NET.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 76cb9807-cbc1-405a-8136-d1e53e66e82b
ms.service: batch
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 06/28/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9968d18f9e27d7a138831394658b40a483b66709
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="get-started-building-solutions-with-the-batch-client-library-for-net"></a>Rozpoczynanie tworzenia rozwiązań za pomocą biblioteki klienta usługi Batch dla platformy .NET

> [!div class="op_single_selector"]
> * [.NET](batch-dotnet-get-started.md)
> * [Python](batch-python-tutorial.md)
> * [Node.js](batch-nodejs-get-started.md)
>
>

W tym artykule omawiamy podstawy usługi [Azure Batch][azure_batch] i biblioteki [Batch .NET][net_api] na podstawie przykładowej aplikacji w języku C#. Wyjaśniamy, jak przykładowa aplikacja korzysta z usługi Batch do przetwarzania równoległego obciążenia w chmurze oraz współpracuje z usługą [Azure Storage](../storage/common/storage-introduction.md) w celu przygotowania i pobrania plików. Przedstawiono tu typowy przepływ pracy w aplikacji usługi Batch oraz wyjaśniono podstawowe zagadnienia dotyczące najważniejszych składników usługi Batch, np. zadań, podzadań, pul i węzłów obliczeniowych.

![Przepływ pracy rozwiązania w usłudze Batch (podstawowy)][11]<br/>

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz praktyczną wiedzę na temat języka C# i programu Visual Studio. Przyjęto również założenie, że jesteś w stanie spełnić wymagania dotyczące tworzenia konta, które zostały wyszczególnione poniżej dla platformy Azure oraz usług Batch i Storage.

### <a name="accounts"></a>Konta
* **Konto platformy Azure**: jeśli nie masz jeszcze subskrypcji platformy Azure, [utwórz bezpłatne konto platformy Azure][azure_free_account].
* **Konto usługi Batch**: po uzyskaniu subskrypcji platformy Azure [utwórz konto usługi Azure Batch](batch-account-create-portal.md).
* **Konto magazynu**: zobacz sekcję [Tworzenie konta magazynu](../storage/common/storage-create-storage-account.md#create-a-storage-account) w temacie [Informacje o kontach magazynu Azure](../storage/common/storage-create-storage-account.md).

> [!IMPORTANT]
> Usługa Batch obsługuje obecnie *tylko* typ konta magazynu **ogólnego przeznaczenia**, zgodnie z opisem w kroku 5 [Tworzenie konta magazynu](../storage/common/storage-create-storage-account.md#create-a-storage-account) w temacie [Informacje o kontach magazynu Azure](../storage/common/storage-create-storage-account.md).
>
>

### <a name="visual-studio"></a>Visual Studio
Do utworzenia przykładowego projektu potrzebny jest program **Visual Studio 2015 lub nowszy**. Bezpłatne i próbne wersje programu Visual Studio można znaleźć w [omówieniu produktów Visual Studio][visual_studio].

### <a name="dotnettutorial-code-sample"></a>Przykład kodu *DotNetTutorial*
[DotNetTutorial][github_dotnettutorial] to jeden z wielu przykładów kodu usługi Batch w repozytorium [azure-batch-samples][github_samples] w witrynie GitHub. Możesz pobrać wszystkie przykłady, klikając przycisk **Clone or download > Download ZIP** (Sklonuj lub pobierz > Pobierz plik ZIP) na stronie głównej repozytorium lub klikając bezpośredni link pobierania pliku [azure-batch-samples-master.zip][github_samples_zip]. Po wyodrębnieniu zawartości pliku ZIP rozwiązanie można znaleźć w następującym folderze:

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### <a name="batchlabs-optional"></a>BatchLabs (opcjonalnie)
[BatchLabs][github_batchlabs] to bezpłatne, bogate w funkcje, autonomiczne narzędzie klienta pomagające tworzyć, debugować i monitorować aplikacje usługi Azure Batch. Chociaż nie jest wymagane do ukończenia tego samouczka, może przydać się podczas tworzenia i debugowania rozwiązań w usłudze Batch.

## <a name="dotnettutorial-sample-project-overview"></a>Omówienie przykładowego projektu DotNetTutorial
Przykładowy kod *DotNetTutorial* jest rozwiązaniem programu Visual Studio, które obejmuje dwa projekty: **DotNetTutorial** i **TaskApplication**.

* **DotNetTutorial** jest aplikacją kliencką, która współdziała z usługami Batch i Storage w celu wykonania równoległego obciążenia w węzłach obliczeniowych (maszyny wirtualne). Aplikacja DotNetTutorial jest uruchamiana na lokalnej stacji roboczej.
* **TaskApplication** to program uruchamiany w węzłach obliczeniowych na platformie Azure, który wykonuje faktyczną pracę. W naszym przykładzie `TaskApplication.exe` analizuje tekst w pliku pobranym z usługi Azure Storage (plik wejściowy). Następnie tworzy plik tekstowy (plik wyjściowy) zawierający listę trzech słów najczęściej występujących w pliku wejściowym. Po utworzeniu pliku wyjściowego program TaskApplication przekazuje plik do usługi Azure Storage. Dzięki temu jest on dostępny do pobrania przez aplikację kliencką. Program TaskApplication jest uruchamiany równolegle w wielu węzłach obliczeniowych w usłudze Batch.

Na poniższym diagramie przedstawiono podstawowe operacje wykonywane przez aplikację kliencką,*DotNetTutorial*, oraz aplikację, która jest wykonywana przez podzadania, *TaskApplication*. Ten podstawowy przepływ pracy jest typowy dla wielu rozwiązań obliczeniowych utworzonych za pomocą usługi Batch. Chociaż nie przedstawiono tu wszystkich funkcji dostępnych w usłudze Batch, prawie każdy scenariusz usługi Batch obejmuje elementy tego przepływu pracy.

![Przykładowy przepływ pracy w usłudze Batch][8]<br/>

[**Krok 1.**](#step-1-create-storage-containers) Utwórz **kontenery** w usłudze Azure Blob Storage.<br/>
[**Krok 2.**](#step-2-upload-task-application-and-data-files) Przekaż pliki aplikacji podzadań i pliki wejściowe do kontenerów.<br/>
[**Krok 3.**](#step-3-create-batch-pool) Utwórz **pulę** w usłudze Batch.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** Pula funkcji **StartTask** pobiera pliki binarne podzadań (TaskApplication), gdy zostają dołączone do puli.<br/>
[**Krok 4.**](#step-4-create-batch-job) Utwórz **zadanie** w usłudze Batch.<br/>
[**Krok 5.**](#step-5-add-tasks-to-job) Dodaj **podzadania** do zadania.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Zadania są zaplanowane do wykonania w węzłach.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Każde zadanie pobiera dane wejściowe z usługi Azure Storage, a następnie rozpoczyna się wykonywanie.<br/>
[**Krok 6.**](#step-6-monitor-tasks) Monitoruj podzadania.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Gdy zadania zostaną ukończone, przekazują dane wyjściowe do usługi Azure Storage.<br/>
[**Krok 7.**](#step-7-download-task-output) Pobierz dane wyjściowe podzadań z usługi Storage.

Jak wspomniano wcześniej, nie wszystkie rozwiązania usługi Batch obejmują dokładnie te kroki i mogą obejmować wiele innych. Aplikacja przykładowa *DotNetTutorial* pokazuje jednak typowe procesy występujące w rozwiązaniu usługi Batch.

## <a name="build-the-dotnettutorial-sample-project"></a>Kompilowanie przykładowego projektu *DotNetTutorial*
Aby pomyślnie uruchomić próbkę, należy najpierw określić poświadczenia konta Storage i usługi Batch w pliku `Program.cs` projektu *DotNetTutorial*. Jeśli jeszcze tego nie zrobiono, otwórz rozwiązanie w programie Visual Studio, klikając dwukrotnie plik rozwiązania `DotNetTutorial.sln`. Lub otwórz je w programie Visual Studio przy użyciu opcji menu **Plik > Otwórz > Projekt/Rozwiązanie**.

Otwórz `Program.cs` w projekcie *DotNetTutorial*. Następnie dodaj poświadczenia określone w górnej części pliku:

```csharp
// Update the Batch and Storage account credential strings below with the values
// unique to your accounts. These are used when constructing connection strings
// for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

> [!IMPORTANT]
> Jak wspomniano powyżej, musisz określić poświadczenia dla konta magazynu **ogólnego przeznaczenia** w usłudze Azure Storage. Aplikacje usługi Batch korzystają z magazynu obiektów blob w ramach konta magazynu **ogólnego przeznaczenia**. Nie określaj poświadczeń dla konta usługi Storage, które zostało utworzone przez wybranie typu konta *Magazyn obiektów Blob*.
>
>

Poświadczenia konta usług Batch i Storage znajdziesz w blokach konta poszczególnych usług w witrynie [Azure Portal][azure_portal]:

![Poświadczenia usługi Batch w portalu][9]
![Poświadczenia usługi Storage w portalu][10]<br/>

Po zaktualizowaniu projektu przy użyciu poświadczeń kliknij prawym przyciskiem myszy rozwiązanie w Eksploratorze rozwiązań i kliknij przycisk **Kompiluj rozwiązanie**. Jeśli zostanie wyświetlony monit, potwierdź przywrócenie pakietów NuGet.

> [!TIP]
> Jeśli pakiety nie zostaną automatycznie przywrócone lub zostaną wyświetlone błędy z informacją o nieudanej próbie przywrócenia pakietów, sprawdź, czy jest zainstalowany [Menedżer pakietów NuGet][nuget_packagemgr]. Następnie włącz pobieranie brakujących pakietów. Zobacz sekcję [Enabling Package Restore During Build][nuget_restore] (Włączanie przywracania pakietów podczas kompilacji), która zawiera instrukcje dotyczące włączania pobierania pakietów.
>
>

W poniższych sekcjach przykładowa aplikacja została podzielona na kroki wykonywane w celu przetworzenia obciążenia w usłudze Batch. Kroki te zostały szczegółowo opisane. Podczas wykonywania instrukcji w dalszej części tego artykułu warto korzystać z informacji z otwartego rozwiązania w programie Visual Studio, ponieważ nie omówiono tu wszystkich wierszy kodu z próbki.

Przejdź do góry metody `MainAsync` w pliku `Program.cs` projektu *DotNetTutorial*, aby rozpocząć od kroku 1. Wszystkie kroki poniżej odpowiadają mniej więcej postępowi wywołań metody w elemencie `MainAsync`.

## <a name="step-1-create-storage-containers"></a>Krok 1: tworzenie kontenerów w usłudze Storage
![Tworzenie kontenerów w usłudze Azure Storage][1]
<br/>

Usługa Batch ma wbudowaną funkcję obsługi interakcji z usługą Azure Storage. Kontenery na koncie usługi Storage będą udostępniać pliki potrzebne zadaniom, które będą uruchamiane na koncie usługi Batch. Kontenery zapewniają również miejsce do przechowywania danych wyjściowych wytworzonych przez zadania. Najpierw aplikacja kliencka *DotNetTutorial* tworzy trzy kontenery w usłudze [Azure Blob Storage](../storage/common/storage-introduction.md):

* **aplikacja**: ten kontener będzie przechowywać aplikację uruchomioną przez podzadania oraz wszelkie ich zależności, np. biblioteki dll.
* **dane wejściowe**: podzadania będą pobierać pliki danych do przetwarzania z kontenera *wejściowego*.
* **dane wyjściowe**: gdy podzadania ukończą przetwarzanie plików wejściowych, przekażą wyniki do kontenera *wyjściowego*.

Do interakcji z kontem usługi Storage i tworzenia kontenerów należy użyć [biblioteki Azure Storage Client Library dla platformy .NET][net_api_storage]. Należy utworzyć odwołanie do konta za pomocą elementu [CloudStorageAccount][net_cloudstorageaccount] i przy jego użyciu utworzyć element [CloudBlobClient][net_cloudblobclient]:

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format(
    "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
    StorageAccountName,
    StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to
// blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Należy używać odwołania `blobClient` w całej aplikacji i przekazać go jako parametr do kilku metod. Przykładem może być blok kodu, który następuje od razu po powyższym, w ramach którego wywołuje się element `CreateContainerIfNotExistAsync` na potrzeby tworzenia kontenerów.

```csharp
// Use the blob client to create the containers in Azure Storage if they don't
// yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```csharp
private static async Task CreateContainerIfNotExistAsync(
    CloudBlobClient blobClient,
    string containerName)
{
        CloudBlobContainer container =
            blobClient.GetContainerReference(containerName);

        if (await container.CreateIfNotExistsAsync())
        {
                Console.WriteLine("Container [{0}] created.", containerName);
        }
        else
        {
                Console.WriteLine("Container [{0}] exists, skipping creation.",
                    containerName);
        }
}
```

Po utworzeniu kontenerów aplikacja może teraz przekazać pliki, które będą używane przez podzadania.

> [!TIP]
> W temacie [How to use Blob Storage from .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) (Jak korzystać z usługi Blob Storage w ramach platformy .NET) znajduje się szczegółowe omówienie korzystania z kontenerów i obiektów blob w usłudze Azure Storage. Powinna to być jedna z najważniejszych pozycji do przeczytania po rozpoczęciu pracy z usługą Batch.
>
>

## <a name="step-2-upload-task-application-and-data-files"></a>Krok 2: przekazanie aplikacji podzadań i plików danych
![Przekazywanie aplikacji podzadań i plików danych wejściowych do kontenerów][2]
<br/>

Podczas przekazywania plików aplikacja *DotNetTutorial* najpierw definiuje kolekcje **aplikacji** i ścieżki plików **wejściowych** na komputerze lokalnym. Następnie przekazuje te pliki do kontenerów, które zostały utworzone w poprzednim kroku.

```csharp
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication,
    // allowing us to determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the
// application that will process the data files, and will be executed by each
// of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(
    blobClient,
    appContainerName,
    applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of
// the tasks that are executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

W pliku `Program.cs` istnieją dwie metody używane w procesie przekazywania:

* `UploadFilesToContainerAsync`: ta metoda zwraca kolekcję obiektów [ResourceFile][net_resourcefile] (omówionych poniżej) i wywołuje wewnętrznie element `UploadFileToContainerAsync` w celu przekazania wszystkich plików przekazywanych w parametrze *filePaths*.
* `UploadFileToContainerAsync`: ta metoda faktycznie wykonuje przekazanie pliku i tworzy obiekty [ResourceFile][net_resourcefile]. Po przekazaniu pliku uzyskuje sygnaturę dostępu współdzielonego (SAS) i zwraca obiekt ResourceFile, który go reprezentuje. Sygnatury dostępu współdzielonego zostały również omówione poniżej.

```csharp
private static async Task<ResourceFile> UploadFileToContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string filePath)
{
        Console.WriteLine(
            "Uploading file {0} to container [{1}]...", filePath, containerName);

        string blobName = Path.GetFileName(filePath);

        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
        await blobData.UploadFromFileAsync(filePath);

        // Set the expiry time and permissions for the blob shared access signature.
        // In this case, no start time is specified, so the shared access signature
        // becomes valid immediately
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
        {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
                Permissions = SharedAccessBlobPermissions.Read
        };

        // Construct the SAS URL for blob
        string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
        string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

        return new ResourceFile(blobSasUri, blobName);
}
```

### <a name="resourcefiles"></a>ResourceFiles
Klasa [ResourceFile][net_resourcefile] zawiera podzadania w usłudze Batch z adresem URL do pliku w usłudze Azure Storage, które są pobierane do węzła obliczeniowego przed uruchomieniem tego podzadania. Właściwość [ResourceFile.BlobSource][net_resourcefile_blobsource] określa pełny adres URL pliku przechowywanego w usłudze Azure Storage. Adres URL może także zawierać sygnaturę dostępu współdzielonego (SAS), która zapewnia bezpieczny dostęp do pliku. Większość typów podzadań w usłudze Batch dla platformy .NET obejmuje właściwość *ResourceFiles*, w tym następujące elementy:

* [CloudTask][net_task]
* [StartTask][net_pool_starttask]
* [JobPreparationTask][net_jobpreptask]
* [JobReleaseTask][net_jobreltask]

Przykładowa aplikacja DotNetTutorial nie używa typów zadań JobPreparationTask ani JobReleaseTask, ale więcej informacji o nich można znaleźć w temacie [Run job preparation and completion tasks on Azure Batch compute nodes](batch-job-prep-release.md) (Uruchamianie podzadań przygotowania i ukończenia zadania w węzłach obliczeniowych w usłudze Azure Batch).

### <a name="shared-access-signature-sas"></a>Sygnatura dostępu współdzielonego (SAS)
Sygnatury dostępu współdzielonego to ciągi, które — w przypadku dołączenia ich do adresu URL — zapewniają bezpieczny dostęp do kontenerów i obiektów blob w usłudze Azure Storage. Aplikacja DotNetTutorial używa adresów URL sygnatury dostępu współdzielonego kontenera i obiektów blob i służy do pokazania, jak uzyskać te ciągi sygnatur dostępu współdzielonego za pomocą usługi Storage.

* **Sygnatury dostępu współdzielonego obiektów Blob**: funkcja StartTask puli w aplikacji DotNetTutorial używa sygnatur dostępu współdzielonego obiektów blob podczas pobierania danych binarnych aplikacji i plików danych wejściowych za pomocą usługi Storage (zobacz krok 3 poniżej). Metoda `UploadFileToContainerAsync` w elemencie `Program.cs` aplikacji DotNetTutorial obejmuje kod, który uzyskuje dostęp do sygnatur dostępu współdzielonego poszczególnych obiektów blob. Odbywa się to przez wywołanie metody [CloudBlob.GetSharedAccessSignature][net_sas_blob].
* **Sygnatury dostępu współdzielonego kontenera**: gdy poszczególne podzadania zakończą pracę w węźle obliczeniowym, przekażą pliki wyjściowe do kontenera *wyjściowego* w usłudze Azure Storage. W tym celu funkcja TaskApplication używa sygnatury dostępu współdzielonego kontenera, która zapewnia prawo do zapisu w kontenerze w ramach ścieżki, gdy przekazuje plik. Uzyskiwanie sygnatury dostępu współdzielonego kontenera odbywa się w podobny sposób, jak w przypadku uzyskiwania sygnatury dostępu współdzielonego obiektów blob. W aplikacji DotNetTutorial będzie widać, że metoda pomocnika `GetContainerSasUrl` wywołuje w tym celu metodę [CloudBlobContainer.GetSharedAccessSignature][net_sas_container]. Więcej informacji na temat sposobu, w jaki aplikacja TaskApplication korzysta z sygnatury dostępu współdzielonego kontenera znajduje się w sekcji „Krok 6: monitorowanie podzadań.”

> [!TIP]
> Aby dowiedzieć się więcej o zapewnieniu bezpiecznego dostępu do danych na koncie usługi Storage, zapoznaj się z dwuczęściową serią dotyczącą sygnatur dostępu współdzielonego: [Part 1: Understanding the shared access signature (SAS) model](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (Część 1: opis modelu sygnatury dostępu współdzielonego [SAS]) i [Part 2: Create and use a shared access signature (SAS) with the Blob storage](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md) (Część 2: tworzenie i korzystanie z sygnatury dostępu współdzielonego [SAS] w magazynie Blob Storage).
>
>

## <a name="step-3-create-batch-pool"></a>Krok 3: tworzenie puli usługi Batch
![Tworzenie puli usługi Batch][3]
<br/>

**Pula** usługi Batch jest kolekcją węzłów obliczeniowych (maszyn wirtualnych), w których usługa Batch wykonuje podzadania danego zadania.

Po przekazaniu plików aplikacji i danych do konta usługi Storage przy użyciu interfejsów API usługi Azure Storage element *DotNetTutorial* rozpoczyna wykonywanie wywołań do usługi Batch przy użyciu interfejsów API udostępnionych przez bibliotekę technologii .NET usługi Batch. Kod najpierw tworzy element [BatchClient][net_batchclient]:

```csharp
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(
    BatchAccountUrl,
    BatchAccountName,
    BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
{
    ...
```

Następnie w przykładzie tworzona jest pula węzłów obliczeniowych na koncie usługi Batch z wywołaniem funkcji `CreatePoolIfNotExistsAsync`. Funkcja `CreatePoolIfNotExistsAsync` używa metody [BatchClient.PoolOperations.CreatePool][net_pool_create], aby utworzyć nową pulę w usłudze Batch:

```csharp
private static async Task CreatePoolIfNotExistAsync(BatchClient batchClient, string poolId, IList<ResourceFile> resourceFiles)
{
    CloudPool pool = null;
    try
    {
        Console.WriteLine("Creating pool [{0}]...", poolId);

        // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(), no pool is actually created in the
        // Batch service. This CloudPool instance is therefore considered "unbound," and we can modify its properties.
        pool = batchClient.PoolOperations.CreatePool(
            poolId: poolId,
            targetDedicatedComputeNodes: 3,                                             // 3 compute nodes
            virtualMachineSize: "small",                                                // single-vCPU, 1.75 GB memory, 225 GB disk
            cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));   // Windows Server 2012 R2

        // Create and assign the StartTask that will be executed when compute nodes join the pool.
        // In this case, we copy the StartTask's resource files (that will be automatically downloaded
        // to the node by the StartTask) into the shared directory that all tasks will have access to.
        pool.StartTask = new StartTask
        {
            // Specify a command line for the StartTask that copies the task application files to the
            // node's shared directory. Every compute node in a Batch pool is configured with a number
            // of pre-defined environment variables that can be referenced by commands or applications
            // run by tasks.

            // Since a successful execution of robocopy can return a non-zero exit code (e.g. 1 when one or
            // more files were successfully copied) we need to manually exit with a 0 for Batch to recognize
            // StartTask execution success.
            CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
            ResourceFiles = resourceFiles,
            WaitForSuccess = true
        };

        await pool.CommitAsync();
    }
    catch (BatchException be)
    {
        // Swallow the specific error code PoolExists since that is expected if the pool already exists
        if (be.RequestInformation?.BatchError != null && be.RequestInformation.BatchError.Code == BatchErrorCodeStrings.PoolExists)
        {
            Console.WriteLine("The pool {0} already existed when we tried to create it", poolId);
        }
        else
        {
            throw; // Any other exception is unexpected
        }
    }
}
```

Podczas tworzenia puli za pomocą funkcji [CreatePool][net_pool_create] należy określić liczbę parametrów, np. liczbę węzłów obliczeniowych, [rozmiar węzłów](../cloud-services/cloud-services-sizes-specs.md) oraz system operacyjny węzłów. W aplikacji *DotNetTutorial* używamy funkcji [CloudServiceConfiguration][net_cloudserviceconfiguration] do określenia systemu Windows Server 2012 R2 z [usług Cloud Services](../cloud-services/cloud-services-guestos-update-matrix.md). 

Można również tworzyć pule węzłów obliczeniowych, które są maszynami wirtualnymi platformy Azure, wybierając element [VirtualMachineConfiguration][net_virtualmachineconfiguration] dla puli. Pulę węzłów obliczeniowych maszyn wirtualnych można utworzyć na podstawie [obrazów systemu Linux](batch-linux-nodes.md) lub Windows. Źródłem obrazów maszyny wirtualnej może być:

- Witryna [Marketplace usługi Azure Virtual Machines][vm_marketplace], która udostępnia gotowe do użycia obrazy systemów Windows i Linux. 
- Obraz niestandardowy przygotowywany i udostępniany przez użytkownika. Aby uzyskać szczegółowe informacje o obrazach niestandardowych, zobacz [Tworzenie rozbudowanych rozwiązań przetwarzania równoległego przy użyciu usługi Batch](batch-api-basics.md#pool).

> [!IMPORTANT]
> W usłudze Batch opłaty są naliczane za zasoby obliczeniowe. Aby zminimalizować koszty przed uruchomieniem próbki, można zmniejszyć parametr `targetDedicatedComputeNodes` do 1.
>
>

Wraz z fizycznymi właściwościami węzłów można też określić funkcję [StartTask][net_pool_starttask] dla puli. Funkcja StartTask jest wykonywana w każdym węźle, gdy tylko ten węzeł zostanie dołączony do puli, oraz za każdym razem, gdy węzeł będzie uruchamiany ponownie. Funkcja StartTask jest szczególnie przydatna podczas instalowania aplikacji w węzłach obliczeniowych przed wykonaniem podzadań. Na przykład jeśli podzadania przetwarzają dane za pomocą skryptów języka Python, można użyć funkcji StartTask do zainstalowania języka Python w węzłach obliczeniowych.

W tej przykładowej aplikacji podzadanie StartTask kopiuje pliki, które pobiera z usługi Storage (określone za pomocą właściwości [StartTask][net_starttask].[ResourceFiles][net_starttask_resourcefiles]) z katalogu roboczego podzadania StartTask do współdzielonego katalogu, do którego mają dostęp *wszystkie* podzadania uruchomione w węźle. Powoduje to skopiowanie pliku `TaskApplication.exe` i jego zależności do udostępnionego katalogu w każdym węźle, gdy węzeł dołącza do puli, aby dostęp do niego miały wszystkie podzadania uruchomione w węźle.

> [!TIP]
> Funkcja **pakietów aplikacji** w usłudze Azure Batch udostępnia inny sposób pobrania aplikacji do węzła obliczeniowego w puli. Szczegółowe informacje można znaleźć w artykule [Deploy applications to compute nodes with Batch application packages (Wdrażanie aplikacji w węzłach obliczeniowych za pomocą pakietów aplikacji usługi Batch)](batch-application-packages.md).
>
>

W powyższym fragmencie kodu warto również zwrócić uwagę na użycie dwóch zmiennych środowiskowych we właściwości *CommandLine* funkcji StartTask: `%AZ_BATCH_TASK_WORKING_DIR%` i `%AZ_BATCH_NODE_SHARED_DIR%`. Każdy węzeł obliczeniowy w puli usługi Batch jest automatycznie konfigurowany za pomocą kilku zmiennych środowiskowych właściwych dla usługi Batch. Wszystkie procesy wykonywane przez zadanie mają dostęp do tych zmiennych środowiskowych.

> [!TIP]
> Informacje na temat zmiennych środowiskowych dostępnych w węzłach obliczeniowych w puli usługi Batch oraz katalogów roboczych podzadań znajdują się w sekcjach [Ustawienia środowiska dla podzadań](batch-api-basics.md#environment-settings-for-tasks) oraz [Pliki i katalogi](batch-api-basics.md#files-and-directories) w artykule [Batch feature overview for developers](batch-api-basics.md) (Omówienie funkcji usługi Batch dla deweloperów).
>
>

## <a name="step-4-create-batch-job"></a>Krok 4: tworzenie zadania w usłudze Batch
![Tworzenie zadania w usłudze Batch][4]<br/>

**Zadanie** usługi Batch jest kolekcją podzadań i jest skojarzone z pulą węzłów obliczeniowych. Podzadania tego zadania są wykonywane w węzłach obliczeniowych skojarzonej puli.

Zadań można użyć nie tylko do organizowania i śledzenia podzadań w powiązanych obciążeniach, ale także do nakładania pewnych ograniczeń — takich jak maksymalny czas wykonywania zadania (a co za tym idzie podzadań, które ono obejmuje), a także priorytet zadania w odniesieniu do innych zadań w ramach konta usługi Batch. Jednak w tym przykładzie zadanie jest skojarzone tylko z pulą, która została utworzona w kroku 3. Żadne dodatkowe właściwości nie są konfigurowane.

Wszystkie zadania usługi Batch są skojarzone z określoną pulą. To skojarzenie wskazuje, w których węzłach będą wykonywane podzadania wchodzące w skład zadania. Należy to określić za pomocą właściwości [CloudJob.PoolInformation][net_job_poolinfo], jak pokazano we fragmencie kodu poniżej.

```csharp
private static async Task CreateJobAsync(
    BatchClient batchClient,
    string jobId,
    string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

Teraz, kiedy zadanie zostało utworzone, są dodawane podzadania, aby wykonać pracę.

## <a name="step-5-add-tasks-to-job"></a>Krok 5: dodawanie podzadań do zadania
![Dodawanie podzadań do zadania][5]<br/>
*(1) Podzadania są dodawane do zadania, (2) podzadania są planowane do uruchomienia w węzłach i (3) podzadania pobierają pliki danych do przetwarzania*

**Podzadania** usługi Batch to pojedyncze jednostki robocze, które są wykonywane w węzłach obliczeniowych. Podzadanie ma wiersz polecenia i uruchamia skrypty lub pliki wykonywalne określone w tym wierszu polecenia.

Aby praca została rzeczywiście wykonana, należy dodać podzadania do zadania. Każde podzadanie typu [CloudTask][net_task] jest konfigurowane za pomocą właściwości wiersza polecenia i właściwości [ResourceFiles][net_task_resourcefiles] (tak jak w przypadku podzadania StartTask w puli), które podzadanie pobiera do węzła, zanim zostanie automatycznie wykonany jego wiersz polecenia. W przykładowym projekcie aplikacji *DotNetTutorial* każde podzadanie przetwarza tylko jeden plik. W związku z tym jego kolekcja ResourceFiles zawiera jeden element.

```csharp
private static async Task<List<CloudTask>> AddTasksAsync(
    BatchClient batchClient,
    string jobId,
    List<ResourceFile> inputFiles,
    string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format(
            "cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 \"{1}\"",
            inputFile.FilePath,
            outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to issuing a separate AddTask call
    // for each. Bulk task submission helps to ensure efficient underlying API calls
    // to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [!IMPORTANT]
> Podczas uzyskiwania dostępu do zmiennych środowiskowych (takich jak `%AZ_BATCH_NODE_SHARED_DIR%`) lub wykonywania aplikacji nieznajdującej się w parametrze `PATH` węzła wiersze poleceń podzadań muszą rozpoczynać się od `cmd /c`. Spowoduje to jawne wykonanie interpretera poleceń i przesłanie do niego instrukcji zakończenia po wypełnieniu polecenia. Nie jest to konieczne, jeśli podzadania wykonują aplikację w parametrze `PATH` węzła (np. *robocopy.exe* lub *powershell.exe*) i żadne zmienne środowiskowe nie są używane.
>
>

W pętli `foreach` w powyższym fragmencie kodu widać, że wiersz polecenia dla podzadania jest zbudowany w taki sposób, że trzy argumenty wiersza polecenia są przekazywane do pliku *TaskApplication.exe*:

1. **Pierwszy argument** jest ścieżką pliku do przetworzenia. Jest to ścieżka lokalna do pliku zgodnie z jego lokalizacją w węźle. Gdy obiekt ResourceFile w `UploadFileToContainerAsync` został utworzony po raz pierwszy, jak powyżej, nazwa pliku została użyta dla tej właściwości (jako parametr do konstruktora ResourceFile). Oznacza to, że plik znajduje się w tym samym katalogu co plik *TaskApplication.exe*.
2. **Drugi argument** określa, że *N* wyrazów na samej górze będzie zapisywanych do pliku wyjściowego. W przykładzie zostało to zakodowane na stałe, aby pierwsze trzy słowa zostały zapisane do pliku wyjściowego.
3. **Trzeci argument** jest sygnaturą dostępu współdzielonego (SAS), która zapewnia prawo do zapisu kontenera **wyjściowego** w usłudze Azure Storage. Plik *TaskApplication.exe* korzysta z tego adresu URL sygnatury dostępu współdzielonego, gdy przekazuje plik wyjściowy do usługi Azure Storage. Kod do tej czynności można znaleźć w metodzie `UploadFileToContainer` w pliku `Program.cs` projektu funkcji TaskApplication:

```csharp
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
        string blobName = Path.GetFileName(filePath);

        // Obtain a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

        // Upload the file (as a new blob) to the container
        try
        {
                CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
                blob.UploadFromFile(filePath);

                Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
                Console.WriteLine();
        }
        catch (StorageException e)
        {

                Console.WriteLine("Write operation failed for SAS URL " + containerSas);
                Console.WriteLine("Additional error information: " + e.Message);
                Console.WriteLine();

                // Indicate that a failure has occurred so that when the Batch service
                // sets the CloudTask.ExecutionInformation.ExitCode for the task that
                // executed this application, it properly indicates that there was a
                // problem with the task.
                Environment.ExitCode = -1;
        }
}
```

## <a name="step-6-monitor-tasks"></a>Krok 6: monitorowanie podzadań
![Monitorowanie podzadań][6]<br/>
*Aplikacja kliencka (1) monitoruje podzadania pod kątem stanu ukończenia i powodzenia, a (2) podzadania przekazują dane wynikowe do usługi Azure Storage*

Gdy podzadania są dodawane do zadania, zostają automatycznie dodane do kolejki i zaplanowane do wykonania w węzłach obliczeniowych w puli skojarzonej z zadaniem. Na podstawie określonych przez użytkownika ustawień usługa Batch samodzielnie obsługuje dodawanie podzadań do kolejki, ich planowanie, ponawianie prób ich wykonania oraz inne czynności administracyjne.

Istnieje wiele sposobów, w jakie można monitorować wykonanie podzadań. Aplikacja DotNetTutorial stanowi prosty przykład zgłaszania tylko w przypadku ukończenia oraz stanów powodzenia lub niepowodzenia. W metodzie `MonitorTasks` w parametrze `Program.cs` programu DotNetTutorial istnieją trzy pojęcia w usłudze Batch dla platformy .NET, które wymagają omówienia. Są one wymienione poniżej w kolejności ich występowania:

1. **ODATADetailLevel**: określenie funkcji [ODATADetailLevel][net_odatadetaillevel] na liście operacji (takich jak uzyskanie listy podzadań zadania) jest niezbędne do zapewnienia wydajności aplikacji usługi Batch. Zaplanuj przeczytanie tematu [Query the Azure Batch service efficiently](batch-efficient-list-queries.md) (Wydajne wysyłanie zapytań do usługi Azure Batch), jeśli planujesz wykonać dowolny rodzaj monitorowania stanu w aplikacjach usługi Batch.
2. **TaskStateMonitor**: funkcja [TaskStateMonitor][net_taskstatemonitor] zapewnia aplikacjom usługi Batch dla platformy .NET narzędzia pomocy do monitorowania stanów podzadań. W przypadku parametru `MonitorTasks` *DotNetTutorial* oczekuje, aż wszystkie podzadania zostaną w określonym czasie objęte zasięgiem funkcji [TaskState.Completed][net_taskstate]. Następnie kończy zadanie.
3. **TerminateJobAsync**: zakończenie zadania za pomocą funkcji [JobOperations.TerminateJobAsync][net_joboperations_terminatejob] (lub blokującej funkcji JobOperations.TerminateJob) spowoduje, że zadanie zostanie oznaczone jako zakończone. Należy tak koniecznie zrobić, jeśli rozwiązanie usługi Batch korzysta z funkcji [JobReleaseTask][net_jobreltask]. Jest to szczególny typ podzadania, który opisano w temacie [Job preparation and completion tasks](batch-job-prep-release.md) (Przygotowanie zadania i podzadania związane z ukończeniem).

Metoda `MonitorTasks` z pliku aplikacji *DotNetTutorial* `Program.cs` jest wyświetlana poniżej:

```csharp
private static async Task<bool> MonitorTasks(
    BatchClient batchClient,
    string jobId,
    TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use
    // a detail level to  specify that only the "id" property of each task should be
    // populated. Using a detail level for all list operations helps to lower
    // response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks =
        await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...",
        timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we
    // will wait for all tasks to reach the Completed state.
    TaskStateMonitor taskStateMonitor
        = batchClient.Utilities.CreateTaskStateMonitor();

    try
    {
        await taskStateMonitor.WhenAll(tasks, TaskState.Completed, timeout);
    }
    catch (TimeoutException)
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);
        Console.WriteLine(failureMessage);
        return false;
    }

    await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

    // All tasks have reached the "Completed" state, however, this does not
    // guarantee all tasks completed successfully. Here we further check each task's
    // ExecutionInfo property to ensure that it did not encounter a failure
    // or return a non-zero exit code.

    // Update the detail level to populate only the task id and executionInfo
    // properties. We refresh the tasks below, and need only this information for
    // each task.
    detail.SelectClause = "id, executionInfo";

    foreach (CloudTask task in tasks)
    {
        // Populate the task's properties with the latest info from the Batch service
        await task.RefreshAsync(detail);

        if (task.ExecutionInformation.Result == TaskExecutionResult.Failure)
        {
            // A task with failure information set indicates there was a problem with the task. It is important to note that
            // the task's state can be "Completed," yet still have encountered a failure.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] encountered a failure: {1}", task.Id, task.ExecutionInformation.FailureInformation.Message);
            if (task.ExecutionInformation.ExitCode != 0)
            {
                // A non-zero exit code may indicate that the application executed by the task encountered an error
                // during execution. As not every application returns non-zero on failure by default (e.g. robocopy),
                // your implementation of error checking may differ from this example.

                Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
            }
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## <a name="step-7-download-task-output"></a>Krok 7: pobranie danych wyjściowych podzadań
![Pobieranie danych wyjściowych zadań podrzędnych z usługi Storage][7]<br/>

Po ukończeniu zadania można pobrać dane wyjściowe podzadań przy użyciu usługi Azure Storage. Odbywa się to przy użyciu wywołania do funkcji `DownloadBlobsFromContainerAsync` w pliku `Program.cs` *DotNetTutorial*:

```csharp
private static async Task DownloadBlobsFromContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string directoryPath)
{
        Console.WriteLine("Downloading all files from container [{0}]...", containerName);

        // Retrieve a reference to a previously created container
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        // Get a flat listing of all the block blobs in the specified container
        foreach (IListBlobItem item in container.ListBlobs(
                    prefix: null,
                    useFlatBlobListing: true))
        {
                // Retrieve reference to the current blob
                CloudBlob blob = (CloudBlob)item;

                // Save blob contents to a file in the specified folder
                string localOutputFile = Path.Combine(directoryPath, blob.Name);
                await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
        }

        Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [!NOTE]
> Wywołanie do funkcji `DownloadBlobsFromContainerAsync` w aplikacji *DotNetTutorial* określa, że pliki powinny zostać pobrane do folderu `%TEMP%`. Można także swobodnie modyfikować tę lokalizację danych wyjściowych.
>
>

## <a name="step-8-delete-containers"></a>Krok 8: usuwanie kontenerów
Ponieważ użytkownik jest rozliczany za dane przechowywane w usłudze Azure Storage, zawsze dobrym rozwiązaniem jest usunięcie obiektów blob, które nie są już potrzebne do zadań w ramach usługi Batch. W pliku `Program.cs` programu DotNetTutorial odbywa się to przy użyciu trzech wywołań do metody pomocy `DeleteContainerAsync`:

```csharp
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

Sama metoda jedynie uzyskuje odwołanie do kontenera, a następnie wywołuje funkcję [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete]:

```csharp
private static async Task DeleteContainerAsync(
    CloudBlobClient blobClient,
    string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.",
            containerName);
    }
}
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Krok 9: usuwanie zadania i puli
W ostatnim kroku zostaje wyświetlony monit o usunięcie zadania i puli, które zostały utworzone przez aplikację DotNetTutorial. Mimo że nie są naliczane opłaty za same zadania i podzadania, *są* naliczane opłaty za węzły obliczeniowe. W związku z tym zaleca się przydzielanie węzłów tylko zależnie do potrzeb. Usuwanie nieużywanych pul może odbywać się podczas konserwacji.

Dla obu obiektów [JobOperations][net_joboperations] i [PoolOperations][net_pooloperations] klienta BatchClient istnieją odpowiednie metody usuwania, które są wywoływane, jeśli użytkownik potwierdzi usunięcie:

```csharp
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [!IMPORTANT]
> Należy pamiętać, że opłaty są naliczane za zasoby obliczeniowe — usunięcie nieużywanych pul zminimalizuje koszty. Ponadto należy pamiętać, że usunięcie puli spowoduje usunięcie wszystkich węzłów obliczeniowych w tej puli i że po usunięciu puli danych z węzłów nie da się odzyskać.
>
>

## <a name="run-the-dotnettutorial-sample"></a>Uruchamianie aplikacji przykładowej *DotNetTutorial*
Po uruchomieniu aplikacji przykładowej dane wyjściowe w konsoli będą wyglądać mniej więcej w taki sposób. W czasie wykonywania nastąpi wstrzymanie operacji w momencie wyświetlenia paska zadań `Awaiting task completion, timeout in 00:30:00...` podczas uruchamiania węzłów obliczeniowych puli. Użyj witryny [Azure Portal][azure_portal] do monitorowania puli, węzłów obliczeniowych, zadania i podzadań w trakcie wykonywania i po nim. Użyj witryny [Azure Portal][azure_portal] lub programu [Azure Storage Explorer][storage_explorers] do wyświetlania zasobów usługi Storage (kontenerów i obiektów blob) tworzonych przez aplikację.

Typowy czas wykonywania wynosi **mniej więcej 5 minut** w przypadku uruchomienia aplikacji w konfiguracji domyślnej.

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Następne kroki
Możesz swobodnie wprowadzać zmiany w funkcjach *DotNetTutorial* i *TaskApplication*, aby eksperymentować z różnymi scenariuszami obliczeniowymi. Spróbuj na przykład dodać opóźnienie wykonywania do funkcji *TaskApplication*, tak jak w przypadku funkcji [Thread.Sleep][net_thread_sleep], w celu symulowania podzadań długotrwałych i monitorowania ich w portalu. Spróbuj dodać więcej podzadań lub dostosować liczbę węzłów obliczeniowych. Dodaj logikę, pod kątem której będzie odbywać się sprawdzanie, i zezwól na użycie istniejącej puli, aby przyspieszyć czas wykonywania (*wskazówka*: zobacz plik `ArticleHelpers.cs` w projekcie [Microsoft.Azure.Batch.Samples.Common][github_samples_common] w repozytorium [azure-batch-samples][github_samples]).

Po zapoznaniu się z podstawowym przepływem pracy rozwiązania usługi Batch nadszedł czas, by poszerzyć wiedzę na temat dodatkowych funkcji w usłudze Batch.

* Przejrzyj artykuł [Overview of Azure Batch features](batch-api-basics.md) (Omówienie funkcji w usłudze Azure Batch), który zalecamy użytkownikom rozpoczynającym korzystanie z tej usługi.
* Przeczytaj inne artykuły dotyczące programowania w usłudze Batch w części **Development in-depth** (Szczegółowy opis programowania) w [ścieżce szkoleniowej dotyczącej usługi Batch][batch_learning_path].
* Zapoznaj się z inną implementacją przetwarzania obciążenia „N najczęściej występujących słów” za pomocą usługi Batch w przykładzie [TopNWords][github_topnwords].
* Przejrzyj [informacje o wersji](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/Batch/DataPlane/changelog.md#azurebatch-release-notes) platformy .NET dla usługi Batch, aby poznać najnowsze zmiany w bibliotece.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchlabs]: https://azure.github.io/BatchLabs/
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudserviceconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudserviceconfiguration.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.resourcefiles.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_virtualmachineconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.virtualmachineconfiguration.aspx
[nuget_packagemgr]: https://docs.nuget.org/consume/installing-nuget
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/vs/
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Tworzenie kontenerów w usłudze Azure Storage"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Przekazywanie aplikacji podzadań i plików danych wejściowych do kontenerów"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Tworzenie puli usługi Batch"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Tworzenie zadania w usłudze Batch"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Dodawanie podzadań do zadania"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Monitorowanie podzadań"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Pobieranie danych wyjściowych podzadań z usługi Storage"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Przepływ pracy w rozwiązaniu usługi Batch (pełny diagram)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "Poświadczenia usługi Batch w portalu"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "Poświadczenia usługi Storage w portalu"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Przepływ pracy rozwiązania w usłudze Batch (diagram minimalny)"
