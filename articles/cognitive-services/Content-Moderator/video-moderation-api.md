---
title: Analizowanie zawartości wideo dla niepożądanego materiału w C# -Content Moderator
titlesuffix: Azure Cognitive Services
description: Jak analizować zawartości wideo na różnych materiał Kodeksem przy użyciu zestawu SDK Moderator zawartości dla platformy .NET
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 7e987c1249360b14fddf8af57c61fdd1a46ee6c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60605318"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>Analizowanie zawartości wideo dla niepożądanego materiału wC#

Ten artykuł zawiera informacje i przykłady kodu, które ułatwią Ci rozpoczęcie korzystania z [zawartości Moderator SDK dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) do skanowania zawartości wideo do zawartości dla dorosłych lub erotycznej.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="prerequisites"></a>Wymagania wstępne
- Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/)

## <a name="set-up-azure-resources"></a>Konfigurowanie zasobów platformy Azure

Pakiet Content Moderator Moderowanie filmów wideo funkcja jest dostępna jako bezpłatny publicznej wersji zapoznawczej **procesor multimediów** w usłudze Azure Media Services (AMS). Usługa Azure Media Services to wyspecjalizowane usługi platformy Azure do przechowywania i przesyłania strumieniowego zawartości wideo. 

### <a name="create-an-azure-media-services-account"></a>Tworzenie konta usługi Azure Media Services

Postępuj zgodnie z instrukcjami w [Tworzenie konta usługi Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) do subskrypcji usługi AMS i utworzyć konto magazynu platformy Azure skojarzone. W ramach tego konta magazynu należy utworzyć nowy kontener magazynu obiektów Blob.

### <a name="create-an-azure-active-directory-application"></a>Tworzenie aplikacji usługi Azure Active Directory

Przejdź do swojej nowej subskrypcji usługi AMS w witrynie Azure portal i wybierz pozycję **dostęp do interfejsu API** menu boczne. Wybierz **nawiązywanie połączenia z usługi Azure Media Services przy użyciu jednostki usługi**. Zanotuj wartość w **punkt końcowy interfejsu API REST** pola; będzie on potrzebny później.

W **aplikacji usługi Azure AD** zaznacz **Utwórz nowy** i nadaj nazwę nowej rejestracji aplikacji usługi Azure AD (na przykład "VideoModADApp"). Kliknij przycisk **Zapisz** i poczekaj kilka minut, podczas gdy aplikacja jest skonfigurowana. Następnie powinien zostać wyświetlony nowy rejestrację aplikacji w obszarze **aplikacji usługi Azure AD** części strony.

Wybierz rejestrację aplikacji, a następnie kliknij przycisk **Zarządzanie aplikacją** przycisk poniżej. Zanotuj wartość w **identyfikator aplikacji** pola; będzie on potrzebny później. Wybierz **ustawienia** > **klucze**, a następnie wprowadź opis nowego klucza (na przykład "VideoModKey"). Kliknij przycisk **Zapisz**, a następnie zanotuj wartość klucza. Skopiuj następujący ciąg i zapisz go w bezpiecznym miejscu.

Aby uzyskać bardziej szczegółowe wskazówki procesu powyżej, zobacz [wprowadzenie do uwierzytelniania usługi Azure AD](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad).

Po wykonaniu, można użyć procesor multimediów Moderowanie filmów wideo na dwa różne sposoby.

## <a name="use-azure-media-services-explorer"></a>Użyj Eksploratora usługi Azure Media Services

Azure Media Services Explorer jest przyjazny dla użytkownika frontonu dla usługi AMS. Umożliwia przeglądanie swoje konto AMS, przekazywania plików wideo i skanowania zawartości przy użyciu procesora media Content Moderator. Pobierz i zainstaluj go z [GitHub](https://github.com/Azure/Azure-Media-Services-Explorer/releases), lub zobacz [wpis w blogu Azure Media Services Explorer](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) Aby uzyskać więcej informacji.

![Eksplorator usługi Azure Media Services za pomocą pakietu Content Moderator](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. W programie Visual Studio Utwórz nowy **Aplikacja konsoli (.NET Framework)** projektu i nadaj mu nazwę **VideoModeration**. 
1. Jeśli w rozwiązaniu istnieją inne projekty, wybierz ten projekt jako pojedynczy projekt startowy.
1. Pobierz wymagane pakiety NuGet. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz pozycję **Zarządzaj pakietami NuGet**, a następnie znajdź i zainstaluj następujące pakiety:
    - windowsazure.mediaservices
    - windowsazure.mediaservices.extensions

## <a name="add-video-moderation-code"></a>Dodaj kod, Moderowanie filmów wideo

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

### <a name="set-up-resource-references"></a>Konfigurowanie odwołania do zasobów

Dodaj następujące pola statyczne do klasy **Program** w pliku _Program.cs_. Te pola przechowują informacje niezbędne do nawiązywania połączenia z subskrypcją usługi AMS. Wprowadź je przy użyciu wartości, które masz w powyższych krokach. Należy pamiętać, że `CLIENT_ID` jest **identyfikator aplikacji** wartość aplikacji usługi Azure AD i `CLIENT_SECRET` jest wartością "VideoModKey", który został utworzony dla danej aplikacji.

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

Jeśli chcesz użyć lokalnego pliku wideo (najprostszym przypadku), dodaj go do projektu i wprowadź ścieżkę jako `INPUT_FILE` wartość (ścieżki względne są względne wobec katalogu wykonywania).

Należy również utworzyć _preset.json_ plików w bieżącym katalogu i użyj go, aby określić numer wersji. Na przykład:

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>Ładowanie danych wejściowych wideo

**Main** metody **Program** klasy utworzy kontekst multimediów platformy Azure, a następnie kontekst magazynu platformy Azure (w przypadku, gdy są filmów wideo w usłudze blob storage). Pozostały kod skanuje wideo z folderu lokalnego, obiektów blob lub wielu obiektów blob w kontenerze usługi Azure storage. Możesz wypróbować wszystkie opcje, zakomentowując wierszy kodu.

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

### <a name="create-an-azure-media-context"></a>Utwórz kontekst multimediów platformy Azure

Dodaj następującą metodę do klasy **Program**. Poświadczenia usługi AMS używane w celu zezwalania na komunikację przy użyciu usługi AMS.

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

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Dodaj kod, aby utworzyć kontekst magazynu platformy Azure

Dodaj następującą metodę do klasy **Program**. Kontekst magazynu tworzone na podstawie poświadczeń magazynu umożliwia dostęp do usługi blob storage.

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

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Dodaj kod, aby utworzyć zasoby multimedialne platformy Azure z lokalnego pliku i obiektów blob

Procesor multimediów pakietu Content Moderator uruchamia zadania **zasoby** na platformie Azure Media Services.
Te metody tworzenia zasobów z pliku lokalnego lub skojarzony obiekt blob.

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

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Dodaj kod do skanowania kolekcji filmów wideo (jako obiekty BLOB) w kontenerze

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

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Dodaj metodę, aby uruchomić zadanie Content Moderator

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
