---
title: 'Szybki Start: Sprawdź zawartość wideo w języku C# - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Jak sprawdzić zawartość wideo do treści dla dorosłych lub erotycznej materiału przy użyciu zestawu SDK Moderator zawartości dla języka C#
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: sajagtap
ms.openlocfilehash: cb97eebcf398137653988ab3b6ef663f987fb57a
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310508"
---
# <a name="quickstart-check-video-content-in-c"></a>Szybki Start: Sprawdź zawartość wideo w języku C#

Obecnie online przeglądarki Generowanie ogromną liczbę wyświetleń wideo w popularnych i regionalne mediów społecznościowych witryn sieci Web i aplikacjach. Przez stosowanie machine learning na podstawie usług, które wykrywa potencjalnie dorosłych i zawartości erotycznej możesz obniżyć koszt wysiłków moderowania.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="sign-up-for-the-content-moderator-media-processor-preview"></a>Zarejestruj się w pakiecie Content Moderator procesor multimediów (wersja zapoznawcza)

### <a name="create-an-azure-media-services-account"></a>Tworzenie konta usługi Azure Media Services

Pakiet Content Moderator wideo funkcja jest dostępna w publicznej wersji zapoznawczej **procesor multimediów** w usłudze Azure Media Services (AMS) bez dodatkowych opłat. [Tworzenie konta usługi Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) w subskrypcji platformy Azure.

### <a name="get-azure-active-directory-credentials"></a>Pobieranie poświadczeń usługi Azure Active Directory

   1. Odczyt [artykułu portalu usługi Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad) Aby dowiedzieć się, jak uzyskać poświadczenia uwierzytelniania usługi Azure Active Directory za pomocą witryny Azure portal.
   1. Odczyt [artykułu platformy .NET dla usługi Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-dotnet-get-started-with-aad) dowiesz się, jak używać poświadczeń usługi Azure Active Directory przy użyciu zestawu .NET SDK.

   > [!NOTE]
   > Przykładowy kod w tym przewodniku Szybki Start używa **uwierzytelnianie jednostki usługi** metody opisanej w obu powyższych artykułach.

Po pobraniu poświadczeń usługi AMS, istnieją dwa sposoby spróbuj procesor multimediów pakietu Content Moderator.

## <a name="use-azure-media-services-explorer"></a>Użyj Eksploratora usługi Azure Media Services

Użyj interakcyjnego [Eksploratora usługi Azure Media Services (AMS)](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) Aby przeglądać swoje konto AMS, przekazywania plików wideo, a skanowanie za pomocą usługi Content Moderator procesor multimediów. [Pobierz i zainstaluj go](https://github.com/Azure/Azure-Media-Services-Explorer/releases) z serwisu GitHub, a [przeglądanie kodu źródłowego](http://github.com/Azure/Azure-Media-Services-Explorer) udzielenie przy użyciu zestawu SDK usługi AMS.

![Eksplorator usługi Azure Media Services za pomocą pakietu Content Moderator](images/ams-explorer-content-moderator.PNG)

## <a name="quickstart-with-visual-studio-and-c"></a>Szybki Start za pomocą programu Visual Studio i języka C#

1. Dodaj nowy projekt **Aplikacja konsoli (.NET Framework)** do rozwiązania.

   W przykładowym kodzie, nadaj projektowi nazwę **VideoModeration**.

1. Wybierz ten projekt jako pojedynczy projekt startowy rozwiązania.

### <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

Zainstaluj następujące pakiety NuGet, dostępne na [NuGet](https://www.nuget.org/).

- windowsazure.mediaservices
- windowsazure.mediaservices.Extensions

### <a name="update-the-programs-using-statements"></a>Aktualizowanie programu za pomocą instrukcji

Dodaj następujący kod `using` instrukcji.

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using System.Collections.Generic;
```

### <a name="initialize-application-specific-settings"></a>Inicjowanie ustawień specyficznych dla aplikacji

Dodaj następujące pola statyczne do **Program** klasy w _Program.cs_.

```csharp
// declare constants and globals
private static CloudMediaContext _context = null;
private static CloudStorageAccount _StorageAccount = null;

// Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
// a list of Blobs to be processed.
static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

private static StorageCredentials _StorageCredentials = null;

// Azure Media Services authentication. See the quickstart for how to get these.
private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
private const string CLIENT_ID = "YOUR CLIENT ID"
private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

// REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

// Content Moderator Media Processor Nam
private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

// Input and Output files in the current directory of the executable
private const string INPUT_FILE = "VIDEO FILE NAME";
private const string OUTPUT_FOLDER = "";
```

### <a name="create-a-preset-file-json"></a>Utwórz plik wstępnie zdefiniowane (json)

Utwórz plik JSON w bieżącym katalogu z numerem wersji.

```csharp
//Example file content:
//        {
//             "version": "2.0"
//        }
private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";
```

### <a name="add-the-following-code-to-the-main-method"></a>Dodaj następujący kod do metody głównego

Metoda główna najpierw tworzy kontekst multimediów platformy Azure, a następnie kontekst magazynu platformy Azure, w przypadku, gdy pliki wideo znajdują się w magazynie obiektów blob. Pozostały kod skanuje wideo z folderu lokalnego, obiektów blob lub wielu obiektów blob w kontenerze usługi Azure storage. Możesz wypróbować wszystkie opcje, zakomentowując wierszy kodu.

```csharp
// Create Azure Media Context
CreateMediaContext();

// Create Storage Context
CreateStorageContext();

// Use a file as the input.
// IAsset asset = CreateAssetfromFile();

// -- OR ---

// Or a blob as the input
// IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

// Then submit the asset to Content Moderator
// RunContentModeratorJob(asset);

//-- OR ----

// Just run the content moderator on all blobs in a list (from a Blob Container)
RunContentModeratorJobOnBlobs();
```

### <a name="add-the-code-to-create-an-azure-media-context"></a>Dodaj kod, aby utworzyć kontekst multimediów platformy Azure

```csharp
/// <summary>
/// Creates a media context from azure credentials
/// </summary>
static void CreateMediaContext()
{
    // Get Azure AD credentials
    var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
        new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
        AzureEnvironments.AzureCloudEnvironment);

    // Initialize an Azure AD token
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

    // Create a media context
    _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
}
```

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Dodaj kod, aby utworzyć kontekst magazynu platformy Azure
Kontekst magazynu tworzone na podstawie poświadczeń magazynu umożliwia dostęp do usługi blob storage.

```csharp
/// <summary>
/// Creates a storage context from the AMS associated storage name and key
/// </summary>
static void CreateStorageContext()
{
    // Get a reference to the storage account associated with a Media Services account. 
    if (_StorageCredentials == null)
    {
        _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
    }
    _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
}
```

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Dodaj kod, aby utworzyć zasoby multimedialne platformy Azure z lokalnego pliku i obiektów blob
Procesor multimediów pakietu Content Moderator uruchamia zadania **zasoby** na platformie Azure Media Services.
Te metody tworzenia zasobów z pliku lokalnego lub skojarzony obiekt blob.

```csharp
/// <summary>
/// Creates an Azure Media Services Asset from the video file
/// </summary>
/// <returns>Asset</returns>
static IAsset CreateAssetfromFile()
{
    return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
}

/// <summary>
/// Creates an Azure Media Services asset from your blog storage
/// </summary>
/// <param name="Blob"></param>
/// <returns>Asset</returns>
static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
{
    // Create asset from the FIRST blob in the list and return it
    return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
}
```

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Dodaj kod do skanowania kolekcji filmów wideo (jako obiekty BLOB) w kontenerze

```csharp
/// <summary>
/// Runs the Content Moderator Job on all Blobs in a given container name
/// </summary>
static void RunContentModeratorJobOnBlobs()
{
    // Get the reference to the list of Blobs. See the following method.
    var blobList = GetBlobsList();

    // Iterate over the Blob list items or work on specific ones as needed
    foreach (var sourceBlob in blobList)
    {
        // Create an Asset
        IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                            _StorageCredentials, AssetCreationOptions.None);
        asset.Update();

        // Submit to Content Moderator
        RunContentModeratorJob(asset);
    }
}

/// <summary>
/// Get all blobs in your container
/// </summary>
/// <returns></returns>
static IEnumerable<IListBlobItem> GetBlobsList()
{
    // Get a reference to the Container within the Storage Account
    // that has the files (blobs) for moderation
    CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
    CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

    // Get the reference to the list of Blobs 
    var blobList = MediaBlobContainer.ListBlobs();
    return blobList;
}
```

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Dodaj metodę, aby uruchomić zadanie Content Moderator

```csharp
/// <summary>
/// Run the Content Moderator job on the designated Asset from local file or blob storage
/// </summary>
/// <param name="asset"></param>
static void RunContentModeratorJob(IAsset asset)
{
    // Grab the presets
    string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

    // grab instance of Azure Media Content Moderator MP
    IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

    // create Job with Content Moderator task
    IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
            asset.AssetFiles.First() + "_" + Guid.NewGuid()));

    ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
            mp, configuration,
            TaskOptions.None);
    contentModeratorTask.InputAssets.Add(asset);
    contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
        AssetCreationOptions.None);

    job.Submit();


    // Create progress printing and querying tasks
    Task progressPrintTask = new Task(() =>
    {
        IJob jobQuery = null;
        do
        {
            var progressContext = _context;
            jobQuery = progressContext.Jobs
            .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}",
                DateTime.Now,
                jobQuery.State));
                Thread.Sleep(10000);
            }
            while (jobQuery.State != JobState.Finished &&
            jobQuery.State != JobState.Error &&
            jobQuery.State != JobState.Canceled);
    });
    progressPrintTask.Start();

    Task progressJobTask = job.GetExecutionProgressTask(
    CancellationToken.None);
    progressJobTask.Wait();

    // If job state is Error, the event handling 
    // method for job progress should log errors.  Here we check 
    // for error state and exit if needed.
    if (job.State == JobState.Error)
    {
        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
        Console.WriteLine(string.Format("Error: {0}. {1}",
        error.Code,
        error.Message));
    }

    DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
}
```

### <a name="add-helper-functions"></a>Dodawanie funkcji pomocnika

Te metody Pobierz plik wyjściowy pakietu Content Moderator (JSON) od zasobów usługi Azure Media Services i śledzić stan zadania Moderowanie tak, aby program mogą logować się stanu działania na konsoli.

```csharp
static void DownloadAsset(IAsset asset, string outputDirectory)
{
    foreach (IAssetFile file in asset.AssetFiles)
    {
        file.Download(Path.Combine(outputDirectory, file.Name));
    }
}

// event handler for Job State
static void StateChanged(object sender, JobStateChangedEventArgs e)
{
    Console.WriteLine("Job state changed event:");
    Console.WriteLine("  Previous state: " + e.PreviousState);
    Console.WriteLine("  Current state: " + e.CurrentState);
    switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("Job finished.");
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
            Console.WriteLine("Job is canceled.\n");
            break;
        case JobState.Error:
            Console.WriteLine("Job failed.\n");
            break;
        default:
            break;
    }
}
```

### <a name="run-the-program-and-review-the-output"></a>Uruchamianie programu i przeglądanie danych wyjściowych

Po zakończeniu zadania moderowanie zawartości analizować odpowiedź w formacie JSON. Składa się z następujących elementów:

- Informacje o wideo podsumowania
- **Zrzuty** jako "**fragmenty**"
- **Klatki kluczowe** jako "**zdarzenia**" za pomocą **reviewRecommended "(= true lub false)"** na podstawie flagi **treści dla dorosłych** i **Racy** wyniki
- **Rozpocznij**, **czas trwania**, **totalDuration**, i **sygnatura czasowa** znajdują się w "znaczników". Dzielenie przez **skali czasu** Aby uzyskać numer w ciągu kilku sekund.
 
> [!NOTE]
> - `adultScore` reprezentuje potencjalnych obecności i prognozowania oceny zawartość, która może być uznane za przekleństwa jawne lub treści dla dorosłych w niektórych sytuacjach.
> - `racyScore` reprezentuje potencjalnych obecności i prognozowania oceny zawartość, która może być uznane za przekleństwa dwuznaczne lub dla dorosłych w niektórych sytuacjach.
> - `adultScore` i `racyScore` należą do zakresu od 0 do 1. Wyższą ocenę, tym większe modelu jest prognozowanie mogą dotyczyć kategorii. Ta wersja zapoznawcza opiera się na modelu statystycznych, a nie ręcznie zakodowane wyników. Zaleca się testowanie za pomocą własnej zawartości, aby określić, jak wyrównuje każdej kategorii wymagań.
> - `reviewRecommended` jest wartość PRAWDA lub FAŁSZ, w zależności od wewnętrznego wynik progów. Klientów należy ocenić, czy Użyj tej wartości lub wybrać progami niestandardowymi na podstawie ich zawartości zasad.

```json
{
"version": 2,
"timescale": 90000,
"offset": 0,
"framerate": 50,
"width": 1280,
"height": 720,
"totalDuration": 18696321,
"fragments": [
{
    "start": 0,
    "duration": 18000
},
{
    "start": 18000,
    "duration": 3600,
    "interval": 3600,
    "events": [
    [
        {
        "reviewRecommended": false,
        "adultScore": 0.00001,
        "racyScore": 0.03077,
        "index": 5,
        "timestamp": 18000,
        "shotIndex": 0
        }
    ]
    ]
},
{
    "start": 18386372,
    "duration": 119149,
    "interval": 119149,
    "events": [
    [
        {
        "reviewRecommended": true,
        "adultScore": 0.00000,
        "racyScore": 0.91902,
        "index": 5085,
        "timestamp": 18386372,
        "shotIndex": 62
        }
    ]
    ]
}
]
}
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak wygenerować [przeglądy wideo](video-reviews-quickstart-dotnet.md) z danych wyjściowych moderowania.

Dodaj [Moderowanie transkrypcji](video-transcript-moderation-review-tutorial-dotnet.md) do Twojego wideo przeglądów.

Zapoznaj się z szczegółowy samouczek dotyczący sposobu tworzenia [kompletne rozwiązanie Moderowanie filmów wideo i transkrypcji](video-transcript-moderation-review-tutorial-dotnet.md).

[Pobierz rozwiązanie programu Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) dla tego programu oraz inne Przewodniki Szybki Start pakietu Content Moderator dla platformy .NET.
