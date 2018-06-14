---
title: Umożliwia tworzenie podsumowań wideo miniatur wideo multimediów Azure | Dokumentacja firmy Microsoft
description: Podsumowanie wideo mogą pomóc tworzyć podsumowania długich filmów wideo, wybierając automatycznie interesujące fragmenty kodu ze źródła wideo. Jest to przydatne, gdy chcesz zapewnić szybki przegląd czego można oczekiwać w długich wideo.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: milanga;juliako;
ms.openlocfilehash: 4f40c9364d02929fe5bb193b4e8eb0a0157d34d2
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2018
ms.locfileid: "29737271"
---
# <a name="use-azure-media-video-thumbnails-to-create-a-video-summarization"></a>Umożliwia tworzenie podsumowań wideo miniatur wideo multimediów Azure
## <a name="overview"></a>Przegląd
**Miniatur wideo multimediów Azure** procesor multimediów (MP) umożliwia tworzenie podsumowanie wideo, które są przydatne do klientów, którzy po prostu chcesz przeglądać podsumowanie wideo długo. Na przykład klienci mogą być wyświetlane krótki "Podsumowanie wideo" gdy umieść kursor nad miniatury. Przez dostosowywanie parametry **miniatur wideo multimediów Azure** za pomocą ustawienia domyślne konfiguracji, można użyć MP zaawansowanego zrzut wykrywania i łączenia rozwiązania algorithmically wygenerować subclip opisowy.  

**Miniatur wideo multimediów Azure** pakiet administracyjny jest obecnie w przeglądzie.

Ten artykuł zawiera szczegółowe informacje o **miniatur wideo multimediów Azure** i pokazuje, jak z niego korzystać z zestawu SDK usługi Media Services dla platformy .NET.

## <a name="limitations"></a>Ograniczenia

W niektórych przypadkach Jeśli wideo nie składa się z różnych scen dane wyjściowe będą tylko jednego zrzut.

## <a name="video-summary-example"></a>Przykład podsumowanie wideo
Oto kilka przykładów czynności procesor multimediów miniatur wideo multimediów Azure:

### <a name="original-video"></a>Oryginalnego wideo
[Oryginalnego wideo](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

### <a name="video-thumbnail-result"></a>Wynik miniatur wideo
[Wynik miniatur wideo](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="task-configuration-preset"></a>Konfiguracja zadania (ustawienia domyślne)
Podczas tworzenia zadania miniatur wideo z **miniatur wideo multimediów Azure**, należy określić ustawienia domyślne konfiguracji. Powyższym przykładzie miniatur został utworzony przy użyciu następującej konfiguracji JSON podstawowe:

```json
    {
        "version":"1.0"
    }
```

Obecnie można zmienić następujące parametry:

| Param | Opis |
| --- | --- |
| outputAudio |Określa, czy Wynikowy klip wideo zawiera żadnego dźwięku. <br/>Dozwolone wartości to: True lub False. Domyślna wartość to True. |
| fadeInFadeOut |Określa, czy przejścia zanikania służą między miniatur oddzielne ruchu.  <br/>Dozwolone wartości to: True lub False.  Domyślna wartość to True. |
| maxMotionThumbnailDurationInSecs |Liczba całkowita, która określa, jak długo są całe wideo wynikowe.  Domyślna zależy od oryginalnego wideo czasu trwania. |

W poniższej tabeli opisano domyślny czas trwania, gdy **maxMotionThumbnailInSecs** nie jest używany.

|  |  |  |
| --- | --- | --- | --- | --- |
| Czas trwania wideo |d < 3 min |3 min < d < 15 min |
| Czas trwania miniatur |s 15 (sceny 2 – 3) |30 sekund (3 – 5 sceny) |

Następujące JSON ustawia dostępne parametry.

```json
    {
        "version": "1.0",
        "options": {
            "outputAudio": "true",
            "maxMotionThumbnailDurationInSecs": "10",
            "fadeInFadeOut": "true"
        }
    }
```

## <a name="net-sample-code"></a>.NET przykładowy kod

Następujących programów przedstawiono sposób:

1. Utworzenie elementu zawartości i przesyłanie pliku multimediów do elementu zawartości.
2. Tworzy zadanie z zadaniem miniatur wideo oparty na pliku konfiguracji, który zawiera następujące ustawienie json: 
    
    ```json
            {                
                "version": "1.0",
                "options": {
                    "outputAudio": "true",
                    "maxMotionThumbnailDurationInSecs": "30",
                    "fadeInFadeOut": "false"
                }
            }
    ```

3. Pobiera pliki danych wyjściowych. 

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

    namespace VideoSummarization
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


                // Run the thumbnail job.
                var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
                                            @"C:\supportFiles\VideoThumbnail\config.json");

                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
            }

            static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Video Thumbnail Input Asset",
                    AssetCreationOptions.None);

                // Declare a new job.
                IJob job = _context.Jobs.Create("My Video Thumbnail Job");

                // Get a reference to Azure Media Video Thumbnails.
                string MediaProcessorName = "Azure Media Video Thumbnails";

                var processor = GetLatestMediaProcessorByName(MediaProcessorName);

                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);

                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
                    processor,
                    configuration,
                    TaskOptions.None);

                // Specify the input asset.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);

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

### <a name="video-thumbnail-output"></a>Wyjście miniatur wideo
[Wyjście miniatur wideo](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Powiązane linki
[Przegląd analiz usługi Azure Media Services](media-services-analytics-overview.md)

[W trakcie analizy multimediów Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

