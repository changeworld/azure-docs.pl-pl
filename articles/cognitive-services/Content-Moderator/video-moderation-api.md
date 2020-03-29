---
title: Analizowanie treści wideo pod kątem kontrowersyjnych materiałów w języku C# - Content Moderator
titleSuffix: Azure Cognitive Services
description: Jak analizować zawartość wideo pod kątem różnych kontrowersyjnych materiałów za pomocą SDK Content Moderator dla .NET
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 71858755fe31823d4d7ef8623b915db851530116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72755233"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>Analizowanie treści wideo pod kątem kontrowersyjnych materiałów w C #

Ten artykuł zawiera informacje i przykłady kodu ułatwiające rozpoczęcie korzystania z [SDK moderatora zawartości dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) w celu skanowania zawartości wideo w poszukiwaniu zawartości dla dorosłych lub treści rasistowskich.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem. 

## <a name="prerequisites"></a>Wymagania wstępne
- Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/)

## <a name="set-up-azure-resources"></a>Konfigurowanie zasobów platformy Azure

Funkcja moderowania wideo moderatora zawartości jest dostępna jako bezpłatny **procesor multimediów** w publicznej wersji zapoznawczej w usłudze Azure Media Services (AMS). Usługa Azure Media Services to wyspecjalizowana usługa platformy Azure do przechowywania i przesyłania strumieniowego zawartości wideo. 

### <a name="create-an-azure-media-services-account"></a>Tworzenie konta usługi Azure Media Services

Postępuj zgodnie z instrukcjami w [Tworzenie konta usługi Azure Media Services,](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) aby zasubskrybować usługę AMS i utworzyć skojarzone konto magazynu platformy Azure. Na tym koncie magazynu utwórz nowy kontener magazynu obiektów Blob.

### <a name="create-an-azure-active-directory-application"></a>Tworzenie aplikacji usługi Azure Active Directory

Przejdź do nowej subskrypcji usługi AMS w witrynie Azure portal i wybierz **dostęp do interfejsu API** z menu bocznego. Wybierz **pozycję Połącz z usługą Azure Media Services z jednostką usługi**. Zanotuj wartość w polu **punktu końcowego interfejsu API REST;** będzie to potrzebne później.

W sekcji **aplikacji usługi Azure AD** wybierz pozycję **Utwórz nowy** i nazwij nową rejestrację aplikacji usługi Azure AD (na przykład "VideoModADApp"). Kliknij **przycisk Zapisz** i odczekaj kilka minut, aż aplikacja jest skonfigurowana. Następnie powinna zostać wyświetlona nowa rejestracja aplikacji w sekcji **aplikacji usługi Azure AD** na stronie.

Wybierz rejestrację aplikacji i kliknij przycisk **Zarządzaj aplikacją** pod nią. Zanotuj wartość w polu **Identyfikator aplikacji;** będzie to potrzebne później. Wybierz**pozycję Klawisze** **ustawień** > i wprowadź opis nowego klucza (na przykład "VideoModKey"). Kliknij **przycisk Zapisz**, a następnie zwróć uwagę na nową wartość klucza. Skopiuj ten ciąg i zapisz go w bezpiecznym miejscu.

Aby uzyskać dokładniejsze wskazówki dotyczące powyższego procesu, zobacz [Wprowadzenie do uwierzytelniania usługi Azure AD.](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad)

Po wykonaniu tej tej funkcji można korzystać z procesora multimediów moderowania wideo na dwa różne sposoby.

## <a name="use-azure-media-services-explorer"></a>Korzystanie z Eksploratora usług Multimediów platformy Azure

Eksplorator usług multimediów Azure jest przyjaznym dla użytkownika frontendem dla usługi AMS. Służy do przeglądania konta AMS, przesyłania filmów i skanowania zawartości za pomocą procesora multimediów Content Moderator. Pobierz i zainstaluj go z [usługi GitHub](https://github.com/Azure/Azure-Media-Services-Explorer/releases)lub zobacz [wpis w blogu programu Azure Media Services Explorer, aby](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) uzyskać więcej informacji.

![Eksplorator usług Azure Media Services z moderatorem zawartości](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. W programie Visual Studio utwórz nowy projekt **aplikacji konsoli (.NET Framework)** i nadaj jej nazwę **VideoModeration**. 
1. Jeśli w rozwiązaniu istnieją inne projekty, wybierz ten projekt jako pojedynczy projekt startowy.
1. Pobierz wymagane pakiety NuGet. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz pozycję **Zarządzaj pakietami NuGet**, a następnie znajdź i zainstaluj następujące pakiety:
    - usługi windowsazure.mediaservices
    - windowsazure.mediaservices.extensions

## <a name="add-video-moderation-code"></a>Dodawanie kodu moderacji wideo

Następnie skopiujesz kod z tego przewodnika i wkleisz go do projektu, aby zaimplementować podstawowy scenariusz moderowania zawartości.

### <a name="update-the-programs-using-statements"></a>Aktualizowanie programu za pomocą instrukcji

Dodaj następujące instrukcje `using` na początku pliku _Program.cs_.

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

### <a name="set-up-resource-references"></a>Konfigurowanie odwołań do zasobów

Dodaj następujące pola statyczne do klasy **Program** w _Program.cs_. W tych polach znajdują się informacje niezbędne do nawiązania połączenia z subskrypcją usługi AMS. Wypełnij je wartościami, które otrzymałeś w powyższych krokach. Należy `CLIENT_ID` zauważyć, że jest wartość **identyfikator aplikacji** `CLIENT_SECRET` usługi Azure AD i jest wartością "VideoModKey", który został utworzony dla tej aplikacji.

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

// Azure Media Services authentication. 
private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
private const string CLIENT_ID = "YOUR CLIENT ID";
private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

// REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

// Content Moderator Media Processor Nam
private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

// Input and Output files in the current directory of the executable
private const string INPUT_FILE = "VIDEO FILE NAME";
private const string OUTPUT_FOLDER = "";

// JSON settings file
private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

```

Jeśli chcesz użyć lokalnego pliku wideo (najprostszy przypadek), dodaj go do `INPUT_FILE` projektu i wprowadź jego ścieżkę jako wartość (ścieżki względne są względem katalogu wykonywania).

Należy również utworzyć plik _preset.json_ w bieżącym katalogu i użyć go do określenia numeru wersji. Przykład:

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>Załaduj wejściowe wideo

**Główna** metoda **klasy Program** utworzy kontekst nośnika azure, a następnie kontekst usługi Azure Storage (w przypadku, gdy klipy wideo znajdują się w magazynie obiektów blob). Pozostały kod skanuje wideo z folderu lokalnego, obiektu blob lub wielu obiektów blob w kontenerze magazynu platformy Azure. Możesz wypróbować wszystkie opcje, komentując inne wiersze kodu.

```csharp
// Create Azure Media Context
CreateMediaContext();

// Create Storage Context
CreateStorageContext();

// Use a file as the input.
IAsset asset = CreateAssetfromFile();

// -- OR ---

// Or a blob as the input
// IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

// Then submit the asset to Content Moderator
RunContentModeratorJob(asset);

//-- OR ----

// Just run the content moderator on all blobs in a list (from a Blob Container)
// RunContentModeratorJobOnBlobs();
```

### <a name="create-an-azure-media-context"></a>Tworzenie kontekstu nośnika platformy Azure

Dodaj następującą metodę do klasy **Program**. Spowoduje to użycie poświadczeń usługi AMS w celu umożliwienia komunikacji z usługi AMS.

```csharp
// Creates a media context from azure credentials
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

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Dodawanie kodu w celu utworzenia kontekstu usługi Azure Storage

Dodaj następującą metodę do klasy **Program**. Aby uzyskać dostęp do magazynu obiektów blob, należy użyć kontekstu magazynu utworzonego na podstawie poświadczeń magazynu magazynu.

```csharp
// Creates a storage context from the AMS associated storage name and key
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

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Dodaj kod do tworzenia zasobów multimediów platformy Azure z lokalnego pliku i obiektu blob

Procesor multimediów moderatora zawartości uruchamia zadania w **zasobach** na platformie Azure Media Services.
Te metody utworzyć zasoby z pliku lokalnego lub skojarzonego obiektu blob.

```csharp
// Creates an Azure Media Services Asset from the video file
static IAsset CreateAssetfromFile()
{
    return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
}

// Creates an Azure Media Services asset from your blog storage
static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
{
    // Create asset from the FIRST blob in the list and return it
    return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
}
```

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Dodawanie kodu do skanowania kolekcji klipów wideo (jako obiektów blob) w kontenerze

```csharp
// Runs the Content Moderator Job on all Blobs in a given container name
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

// Get all blobs in your container
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

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Dodaj metodę uruchamiania zadania moderatora zawartości

```csharp
// Run the Content Moderator job on the designated Asset from local file or blob storage
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

Te metody pobrać plik wyjściowy moderatora zawartości (JSON) z zasobu usługi Azure Media Services i pomóc śledzić stan zadania moderowania, dzięki czemu program może rejestrować stan uruchomiony do konsoli.

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

Po zakończeniu zadania moderowania zawartości przeanalizuj odpowiedź JSON. Składa się z tych elementów:

- Podsumowanie informacji wideo
- **Strzały** jako "**fragmenty**"
- **Klatki kluczowe** jako "**wydarzenia**" z **recenzjąRekomunażoną flagą "= prawda lub fałsz)"** na podstawie wyników **adult** i **racy**
- **start**, **czas trwania,** **totalDuration**, a **sygnatura czasowa** są w "kleszcze". Podziel według **skali czasu,** aby uzyskać liczbę w sekundach.
 
> [!NOTE]
> - `adultScore`reprezentuje potencjalną obecność i wynik przewidywania treści, które w pewnych sytuacjach mogą być uważane za osoby o charakterze jednoznacznie seksualnym lub dorosłych.
> - `racyScore`reprezentuje potencjalną obecność i wynik przewidywania treści, które mogą być uznane za sugestywne seksualnie lub dojrzałe w pewnych sytuacjach.
> - `adultScore`i `racyScore` są od 0 do 1. Im wyższy wynik, tym wyższy model przewiduje, że kategoria może mieć zastosowanie. Ta wersja zapoznawcza opiera się na modelu statystycznym, a nie ręcznie kodowane wyniki. Zalecamy przetestowanie z własną zawartością, aby określić, jak każda kategoria jest zgodna z Twoimi wymaganiami.
> - `reviewRecommended`jest true lub false w zależności od wewnętrznych progów wynik. Klienci powinni ocenić, czy użyć tej wartości lub zdecydować o progach niestandardowych na podstawie ich zasad zawartości.

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

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak generować [recenzje wideo](video-reviews-quickstart-dotnet.md) z danych wyjściowych moderowania.

Dodaj [moderowanie transkrypcji](video-transcript-moderation-review-tutorial-dotnet.md) do recenzji wideo.

Zapoznaj się ze szczegółowym samouczkiem, jak zbudować [kompletne rozwiązanie moderacji wideo i transkrypcji](video-transcript-moderation-review-tutorial-dotnet.md).

[Pobierz rozwiązanie programu Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) dla tego i innych przewodników przewodnika content moderatora dla platformy .NET.
