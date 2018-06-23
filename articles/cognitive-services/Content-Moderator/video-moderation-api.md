---
title: Azure moderatora zawartości - wideo łagodzenia | Dokumentacja firmy Microsoft
description: Umożliwia skanowanie w poszukiwaniu możliwych zawartość dla dorosłych i luksusowych łagodzenia wideo.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/02/2018
ms.author: sajagtap
ms.openlocfilehash: ef58f5990d4a0a19ab2b8c61b42ab2a0754dc6fa
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349349"
---
# <a name="video-moderation"></a>Moderowanie filmów wideo

Obecnie online przeglądarki Generowanie miliardów widoków wideo w popularnych i regionalnych mediów społecznościowych witryn sieci web i zwiększając wydajność. Stosując uczenie maszynowe oparte na usług do prognozowania potencjalnych zawartość dla dorosłych i luksusowych, możesz obniżyć koszty wysiłków łagodzenia.

## <a name="sign-up-for-the-content-moderator-media-processor-public-preview"></a>Załóż procesor multimediów zawartości moderatora (publicznej wersji zapoznawczej)

### <a name="create-a-free-azure-account"></a>Utwórz bezpłatne konto platformy Azure

[Zacznij tutaj](https://azure.microsoft.com/free/) utworzyć bezpłatne konto platformy Azure, jeśli nie masz już.

### <a name="create-an-azure-media-services-account"></a>Tworzenie konta usługi Azure Media Services

Moderatora zawartości wideo funkcja jest dostępna jako publicznej wersji zapoznawczej **procesor multimediów** w usłudze Azure Media Services (AMS) bez dodatkowych opłat.

[Tworzenie konta usługi Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) w Twojej subskrypcji platformy Azure.

### <a name="get-azure-active-directory-credentials"></a>Pobieranie poświadczeń usługi Azure Active Directory

   1. Odczyt [artykułu portalu usługi Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad) Aby dowiedzieć się, jak używać portalu Azure można pobrać poświadczeń uwierzytelniania usługi Azure AD.
   1. Odczyt [artykułu Azure Media Services na platformie .NET](https://docs.microsoft.com/azure/media-services/media-services-dotnet-get-started-with-aad) informacje na temat Użyj swoich poświadczeń usługi Azure Active Directory przy użyciu zestawu .NET SDK.

   > [!NOTE]
   > Przykładowy kod tego przewodnika Szybki Start używa **główne uwierzytelnianie usługi** metody opisanej w obu tych artykułów.

Po pobraniu poświadczeń AMS istnieją dwa sposoby spróbuj procesor multimediów moderatora zawartości.

## <a name="use-azure-media-services-explorer"></a>Użyj usługi Azure Media Services Explorer

Użyj interakcyjnego [explorer Azure Media Services (AMS)](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) do przeglądania kontem AMS, przekazywania plików wideo, a Skanuj za pomocą procesor multimediów moderatora zawartości. [Pobierz i zainstaluj go](https://github.com/Azure/Azure-Media-Services-Explorer/releases) z serwisu GitHub, i [przeglądanie kodu źródłowego](http://github.com/Azure/Azure-Media-Services-Explorer) do Poznaj przy użyciu zestawu SDK usługi AMS.

![Azure Media Services explorer z moderatora zawartości](images/ams-explorer-content-moderator.PNG)

## <a name="net-quickstart-with-visual-studio-and-c"></a>.NET — Szybki Start o Visual Studio i C#

1. Dodaj nową **aplikacji konsoli (.NET Framework)** projektu do rozwiązania.

   W przykładowym kodzie nazwij projekt **VideoModeration**.

1. Wybierz ten projekt jako projekt startowy pojedynczego dla rozwiązania.

### <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

Zainstaluj następujące pakiety NuGet dostępne na [NuGet](https://www.nuget.org/).

- windowsazure.mediaservices
- windowsazure.mediaservices.Extensions

### <a name="update-the-programs-using-statements"></a>Aktualizacja programu użytkownika za pomocą instrukcji

Zmodyfikuj program użytkownika przy użyciu instrukcji.

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


### <a name="initialize-application-specific-settings"></a>Inicjowanie ustawienia specyficzne dla aplikacji

Dodaj następujące pola statycznego do **Program** klasy w pliku Program.cs.

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

### <a name="create-a-preset-file-json"></a>Utwórz plik wstępnie zdefiniowane (json)

Utwórz plik JSON w bieżącym katalogu z numerem wersji.

    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";
    //Example file content:
    //        {
    //             "version": "2.0"
    //        }
    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

### <a name="add-the-following-code-to-the-main-method"></a>Dodaj następujący kod do metody main

Metoda główna najpierw tworzy kontekst multimediów Azure, a następnie kontekst magazynu Azure w przypadku plików wideo jest w magazynie obiektów blob.
Pozostały kod skanuje wideo z folderu lokalnego, obiektów blob lub wielu obiektów blob w kontenerze magazynu Azure.
Możesz wypróbować wszystkie opcje, przez dodawanie komentarza do innych wierszy kodu.

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

### <a name="add-the-code-to-create-an-azure-media-context"></a>Dodaj kod, aby utworzyć kontekstu multimediów Azure

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

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Dodaj kod, aby utworzyć kontekst magazynu Azure
Kontekst magazynu utworzone na podstawie poświadczeń magazynu umożliwia dostęp do usługi magazynu obiektów blob.

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

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Dodaj kod, aby utworzyć Azure Media zasoby z lokalnego pliku i obiektów blob
Procesor multimediów zawartości moderatora uruchamia zadania na **zasoby** w ramach platformy Azure Media Services.
Te metody tworzenia zasoby z pliku lokalnego lub skojarzony obiekt blob.

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

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Dodaj kod, aby skanować kolekcji filmów wideo (jak obiekty BLOB) w kontenerze

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

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Dodaj metodę, aby uruchomić zadanie moderatora zawartości

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

### <a name="add-a-couple-of-helper-functions"></a>Dodaj kilka funkcji pomocnika

Te metody Pobierz moderatora zawartości pliku wyjściowego (JSON) z zasobów usługi Azure Media Services, a śledzić stan zadania łagodzenia tak, aby program można rejestrować stan działania do konsoli.

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

### <a name="run-the-program-and-review-the-output"></a>Uruchom program i przejrzyj dane wyjściowe

Po zakończeniu zadania zawartości łagodzenia analizować odpowiedź w formacie JSON. Składa się z następujących elementów:

- Podsumowanie informacji wideo
- **Zrzuty** jako "**fragmenty**"
- **Klucz ramki** jako "**zdarzenia**" z **reviewRecommended "(= true lub false)"** na podstawie flagi **dla dorosłych** i **Racy** wyników
- **Uruchom**, **czas trwania**, **totalDuration**, i **sygnatury czasowej** w parametrem "ticks". Dzielenie przez **skali czasu** można pobrać liczby w sekundach.
 
> [!NOTE]

> - `adultScore` reprezentuje wynik obecności i prognozowanie potencjalnych zawartości, którą można uznać za płciowo jawne lub dla dorosłych w niektórych sytuacjach.
> - `racyScore` reprezentuje wynik obecności i prognozowanie potencjalnych zawartości, którą można uznać za płciowo sugerujących lub dojrzałe w niektórych sytuacjach.
> - `adultScore` i `racyScore` należą do zakresu od 0 do 1. Im wyższa jest ocena wyższa modelu jest prognozowanie kategorii można stosować. Ta wersja zapoznawcza zależy od statystyczne modelu zamiast wyników kodowane ręcznie. Zaleca się testowanie na własną zawartość, aby określić, jak każda kategoria wyrównuje do własnych potrzeb.
> - `reviewRecommended` jest wartość PRAWDA lub FAŁSZ w zależności od wewnętrznego wynik progów. Klienci powinna ocenić, czy ta wartość lub zdecydować o progami niestandardowymi na podstawie ich zawartości zasad.
>

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

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak Generowanie [przegląda wideo](video-reviews-quickstart-dotnet.md) z danych wyjściowych łagodzenia.

Dodaj [łagodzenia wykaz](video-transcript-moderation-review-tutorial-dotnet.md) do swoje recenzje wideo.

Zapoznaj się z szczegółowy samouczek na temat tworzenia [ukończyć wideo i zapis rozwiązania łagodzenia](video-transcript-moderation-review-tutorial-dotnet.md).

[Pobierz rozwiązania Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) to i inne elementy zawartości moderatora szybkiego startu dla platformy .NET.
