---
title: Indeksowanie plików multimedialnych za pomocą usługi Azure Media Indexer 2 w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Usługa Azure Media Indexer umożliwia Dodaj zawartości multimediów jako możliwej do multimedialnej możliwość wyszukiwania oraz generowanie pełnotekstowej transkrypcji na podpisy kodowane oraz słowa kluczowe. W tym temacie pokazano, jak używać programu Media Indexer 2 (wersja zapoznawcza).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/19/2019
ms.author: adsolank;juliako;
ms.openlocfilehash: 304ecda320e1fdd9573bc961fde74efe03400aa3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64712950"
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Indeksowanie plików multimedialnych za pomocą usługi Azure Media Indexer 2 w wersji zapoznawczej
## <a name="overview"></a>Omówienie
**Azure Media Indexer 2 w wersji zapoznawczej** procesor multimediów (MP) umożliwia Dodaj plików multimedialnych i zawartości multimedialnej możliwość wyszukiwania, a także wygenerować zamknięte śledzi podpisów. W porównaniu do poprzedniej wersji [usługi Azure Media Indexer](media-services-index-content.md), **Azure Media Indexer 2 w wersji zapoznawczej** wykonuje indeksowania szybciej i zapewnia szerszy obsługę języka. Obsługiwane języki angielski, hiszpański, francuski, niemiecki, włoski, chiński (mandaryński, uproszczonym), portugalski, arabskiego, rosyjski i japoński.

**Azure Media Indexer 2 w wersji zapoznawczej** pakiet administracyjny jest obecnie w wersji zapoznawczej.

W tym artykule przedstawiono sposób tworzenia zadania indeksowania za pomocą **Azure Media Indexer 2 w wersji zapoznawczej**.

> [!NOTE]
> Mają zastosowanie następujące kwestie:
> 
> Indeksatora w wersji 2 nie jest obsługiwana w chińskiej wersji platformy Azure i Azure dla instytucji rządowych.
> 
> Podczas indeksowania zawartości, upewnij się użyć plików multimedialnych, które mają jasno mowy (bez muzyki w tle, hałasu, efekty i szumów mikrofon). Niektóre przykłady odpowiedniej zawartości to: rejestrowane spotkań, kursach i prezentacji. Następująca zawartość może nie być odpowiednie dla indeksowania: filmy, programy telewizyjne, wszystko za pomocą mieszanego audio i efekty dźwiękowe źle nagranej zawartości przy użyciu hałas w tle (szumów).
> 
> 

Ten artykuł zawiera szczegółowe informacje o **Azure Media Indexer 2 w wersji zapoznawczej** i pokazuje, jak z niej korzystać z zestawu SDK usługi Media Services dla platformy .NET

## <a name="input-and-output-files"></a>Pliki wejściowe i wyjściowe
### <a name="input-files"></a>Pliki wejściowe
Plików audio i wideo

### <a name="output-files"></a>Pliki wyjściowe
Zadania można wygenerować plików z napisami w następujących formatach:  

* **SAMI**
* **TTML**
* **WebVTT**

Zamknięte podpisu (DW) plików w tych formatach można udostępnić pliki audio i wideo dla osób z wadami słuchu niepełnosprawności.

## <a name="task-configuration-preset"></a>Konfiguracja zadania (ustawienie wstępne)
Podczas tworzenia Indeksowanie zadań za pomocą **Azure Media Indexer 2 w wersji zapoznawczej**, należy określić ustawienie wstępne konfiguracji.

Następujące dane JSON ustawia dostępne parametry.

```json
    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }
```

## <a name="supported-languages"></a>Obsługiwane języki
Wersji zapoznawczej witryny Azure Media Indexer 2 obsługuje mowy na tekst dla następujących języków (jeśli jest określenie nazwy języka w konfiguracji zadania, użyj 4-znakowy kod w nawiasach kwadratowych, jak pokazano poniżej):

* Angielski [EnUs]
* Hiszpański [EsEs]
* Chiński (mandaryński, uproszczone) [ZhCn]
* Francuski [FrFr]
* Niemiecki [DeDe]
* Włoski [ItIt]
* Portugalski [PtBr]
* Arabski (egipska) [ArEg]
* Japoński [JaJp]
* Federacja [RuRu]
* British English [EnGb]
* Hiszpański (Meksyk) [EsMx] 

## <a name="supported-file-types"></a>Obsługiwane typy plików

Aby uzyskać informacji na temat typów plików obsługiwanych, zobacz [obsługiwane kodery-dekodery/formaty](media-services-media-encoder-standard-formats.md#input-containerfile-formats) sekcji.

## <a name="net-sample-code"></a>Przykładowy kod .NET

Poniższy program pokazuje jak:

1. Utworzenie elementu zawartości i przekaż plik multimedialny do niego.
2. Utwórz zadanie w przypadku zadania indeksowania oparte na pliku konfiguracji, który zawiera następujące ustawienie wstępne json:

    ```json
            {
            "version":"1.0",
            "Features":
                [
                {
                "Options": {
                        "Formats":["WebVtt","ttml"],
                        "Language":"enUs",
                        "Type":"RecoOptions"
                },
                "Type":"SpReco"
                }]
            }
    ```
    
3. Pobieranie plików wyjściowych. 
   
#### <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Przykład

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;
using System.Threading.Tasks;

namespace IndexContent
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Run indexing job.
            var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                        @"C:\supportFiles\Indexer\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
        }

        static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Indexing Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Indexing Job");

            // Get a reference to Azure Media Indexer 2 Preview.
            string MediaProcessorName = "Azure Media Indexer 2 Preview";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Indexing Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset to be indexed.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

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
                return null;
            }

            return job.OutputMediaAssets[0];
        }

        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
        {
            IAsset asset = _context.Assets.Create(assetName, options);

            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);

            return asset;
        }

        static void DownloadAsset(IAsset asset, string outputDirectory)
        {
            foreach (IAssetFile file in asset.AssetFiles)
            {
                file.Download(Path.Combine(outputDirectory, file.Name));
            }
        }

        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors
                .Where(p => p.Name == mediaProcessorName)
                .ToList()
                .OrderBy(p => new Version(p.Version))
                .LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor",
                                                           mediaProcessorName));

            return processor;
        }

        static private void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine();
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:
                    // Cast sender as a job.
                    IJob job = (IJob)sender;
                    // Display or log error details as needed.
                    // LogJobStop(job.Id);
                    break;
                default:
                    break;
            }
        }
    }
}
```

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Powiązane linki
[Przegląd Analityki usługi Azure Media Services](media-services-analytics-overview.md)

[Pokazy usługi Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

