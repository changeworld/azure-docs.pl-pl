---
title: Indeksowanie plików multimedialnych za pomocą programu Azure Media Indexer 2 Preview | Dokumenty firmy Microsoft
description: Usługa Azure Media Indexer umożliwia wyszukiwanie zawartości plików multimedialnych i generowanie transkrypcji pełnotekstowej dla podpisów kodowych i słów kluczowych. W tym temacie pokazano, jak używać programu Media Indexer 2 Preview.
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
ms.date: 09/22/2019
ms.author: juliako
ms.reviewer: adsolank
ms.openlocfilehash: c24218dc116803ca0e0a1f166b7b54b24fc4d5ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163798"
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Indeksowanie plików multimedialnych za pomocą programu Azure Media Indexer 2 Preview

> [!NOTE]
> Procesor nośników **Usługi Azure Media Indexer 2** zostanie wycofany. Aby uzyskać daty wycofania, zobacz ten temat [starszych składników.](legacy-components.md) [Indeksator wideo usługi Azure Media Services](https://docs.microsoft.com/azure/media-services/video-indexer/) zastępuje ten starszy procesor multimediów. Aby uzyskać więcej informacji, zobacz [Migrowanie z indeksatora multimediów azure i indeksatora multimediów azure 2 do indeksatora wideo usługi Azure Media Services.](migrate-indexer-v1-v2.md)

Procesor multimediów **w wersji zapoznawczej azure media indexer 2** (MP) umożliwia przeszukiwanie plików multimedialnych i zawartości, a także generowanie utworów z napisami kodowymi. W porównaniu do poprzedniej wersji [programu Azure Media Indexer,](media-services-index-content.md) **usługa Azure Media Indexer 2 Preview** wykonuje szybsze indeksowanie i oferuje szerszą obsługę języka. Obsługiwane języki to angielski, hiszpański, francuski, niemiecki, włoski, chiński (mandaryński, uproszczony), portugalski, arabski, rosyjski i japoński.

**Usługa Azure Media Indexer 2 Preview** MP jest obecnie w wersji zapoznawczej.

W tym artykule pokazano, jak utworzyć zadania indeksowania za pomocą **usługi Azure Media Indexer 2 Preview**.

## <a name="considerations"></a>Zagadnienia do rozważenia

Obowiązują następujące zastrzeżenia:
 
* Indeksator 2 nie jest obsługiwany w usłudze Azure China 21Vianet i azure government.
* Podczas indeksowania zawartości należy używać plików multimedialnych, które mają bardzo wyraźną mowę (bez muzyki w tle, szumów, efektów lub syków mikrofonowych). Oto kilka przykładów odpowiedniej zawartości: nagrane spotkania, wykłady lub prezentacje. Następująca zawartość może nie być odpowiednia do indeksowania: filmy, programy telewizyjne, wszystko z mieszanym dźwiękiem i efektami dźwiękowymi, źle nagrana zawartość z szumem tła (syk).
 
## <a name="input-and-output-files"></a>Pliki wejściowe i wyjściowe
### <a name="input-files"></a>Pliki wejściowe
Pliki audio lub wideo

### <a name="output-files"></a>Pliki wyjściowe
Zadanie indeksowania może generować pliki podpisów kodowych w następujących formatach:  

* **TTML (ttml)**
* **WebVTT (webvtt)**

Pliki z podpisami kodowanymi (CC) w tych formatach mogą być używane do udostępnienia plików audio i wideo osobom z niepełnosprawnością słuchu.

## <a name="task-configuration-preset"></a>Konfiguracja zadania (ustawienie wstępne)
Podczas tworzenia zadania indeksowania za pomocą **usługi Azure Media Indexer 2 Preview,** należy określić predefiniowane ustawienia konfiguracji.

Następujący JSON ustawia dostępne parametry.

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
Usługa Azure Media Indexer 2 Preview obsługuje zamiany mowy na tekst dla następujących języków (podczas określania nazwy języka w konfiguracji zadania należy użyć kodu 4-znakowego w nawiasach, jak pokazano poniżej):

* angielski [EnUs]
* Hiszpański [Es]
* Chiński (mandaryński, uproszczony) [ZhCn]
* Francuski [FrFr]
* Niemiecki [DeDe]
* Włoski [ItIt]
* Portugalski [PtBr]
* arabski (egipski) [ArEg]
* Japoński [JaJp]
* Rosyjski [RuRu]
* angielski brytyjski [EnGb]
* Hiszpański (Meksyk) [EsMx] 

## <a name="supported-file-types"></a>Obsługiwane typy plików

Aby uzyskać informacje na temat obsługiwanych typów plików, zobacz sekcję [obsługiwane kodeki/formaty.](media-services-media-encoder-standard-formats.md#input-containerfile-formats)

## <a name="net-sample-code"></a>Przykładowy kod platformy .NET

W poniższym programie pokazano, jak:

1. Utwórz zasób i przekaż plik multimedialny do zasobu.
2. Utwórz zadanie z zadaniem indeksowania na podstawie pliku konfiguracyjnego zawierającego następujące ustawienia json:

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
    
3. Pobierz pliki wyjściowe. 
   
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
[Omówienie usługi Azure Media Services Analytics](media-services-analytics-overview.md)

[Pokazy usługi Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

