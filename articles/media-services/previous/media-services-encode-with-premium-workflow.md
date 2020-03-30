---
title: Zaawansowane kodowanie za pomocą przepływu pracy Media Encoder Premium | Dokumenty firmy Microsoft
description: Dowiedz się, jak zakodować za pomocą przepływu pracy media encoder Premium. Przykłady kodu są zapisywane w języku C# i używają zestawu SDK usług multimedialnych dla platformy .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: ca5de657ad45f53cff0cb01d5fe9cc412baf4533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792298"
---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Zaawansowane kodowanie za pomocą usługi Media Encoder Premium Workflow
> [!NOTE]
> Procesor nośników Media Encoder Premium Workflow omówiony w tym artykule nie jest dostępny w Chinach.
>
>

## <a name="overview"></a>Omówienie
Usługa Microsoft Azure Media Services wprowadza procesor nośników **Media Encoder Premium Workflow.** Ten procesor oferuje zaawansowane możliwości kodowania dla przepływów pracy premium na żądanie.

Następujące tematy przedstawiają szczegóły dotyczące **przepływu pracy usługi Media Encoder Premium:**

* [Formaty obsługiwane przez przepływ pracy Premium Kodera multimediów](media-services-premium-workflow-encoder-formats.md) — omówiono formaty plików i kodeków obsługiwanych przez **media encoder Premium Workflow**.
* [Przegląd i porównanie koderów multimediów platformy Azure na żądanie](media-services-encode-asset.md) porównuje możliwości kodowania w programie Media **Encoder Premium Workflow** i **Media Encoder Standard.**

W tym artykule pokazano, jak zakodować za pomocą **przepływu pracy usługi Media Encoder Premium** przy użyciu platformy .NET.

Zadania kodowania przepływu **pracy usługi Media Encoder Premium** wymagają oddzielnego pliku konfiguracyjnego, zwanego plikiem przepływu pracy. Te pliki mają rozszerzenie przepływu pracy i są tworzone za pomocą narzędzia [Projektant przepływu pracy.](media-services-workflow-designer.md)

W [tym](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)miejscu można również uzyskać domyślne pliki przepływu pracy . Folder zawiera również opis tych plików.

Pliki przepływu pracy muszą zostać przekazane na konto usługi Media Services jako zasób, a ten zasób powinien zostać przekazany do zadania kodowania.

## <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md). 

## <a name="encoding-example"></a>Przykład kodowania

W poniższym przykładzie pokazano, jak zakodować za pomocą **przepływu pracy usługi Media Encoder Premium**.

Wykonywane są następujące kroki:

1. Utwórz zasób i przekaż plik przepływu pracy.
2. Utwórz zasób i przekaż źródłowy plik multimedialny.
3. Pobierz procesor multimediów "Media Encoder Premium Workflow".
4. Tworzenie zadania i zadania.

    W większości przypadków ciąg konfiguracji zadania jest pusty (jak w poniższym przykładzie). Istnieje kilka zaawansowanych scenariuszy (które wymagają dynamicznego ustawiania właściwości środowiska uruchomieniowego), w którym to przypadku należy podać ciąg XML do zadania kodowania. Przykładami takich scenariuszy są: tworzenie nakładki, równoległe lub sekwencyjne szwy nośników, napisy.
5. Dodaj dwa zasoby wejściowe do zadania.

   1. 1. miejsce – zasób przepływu pracy.
   2. 2. – zasób wideo.

      >[!NOTE]
      >Zasób przepływu pracy musi zostać dodany do zadania przed zasóbem multimedialnym.
      Ciąg konfiguracji dla tego zadania powinien być pusty.
   
6. Prześlij zadanie kodowania.

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderPremiumWorkflowSample
{
    class Program
    {
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

        private static readonly string _supportFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _workflowFilePath =
            Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");

        private static readonly string _singleMP4InputFilePath =
            Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");

        static void Main(string[] args)
        {

            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
            var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
            IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset);

        }

        static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
        {
            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
            var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return asset;
        }

        static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Premium Workflow encoding job");
            // Get a media processor reference, and pass to it the name of the
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                processor,
                "",
                TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(workflow);
            task.InputAssets.Add(video); // we add one asset
                                         // Add an output asset to contain the results of the job.
                                         // This output is specified as AssetCreationOptions.None, which
                                         // means the output asset is not encrypted.
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new
                    EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
            progressJobTask.Wait();

            // If job state is Error the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                throw new Exception("\nExiting method due to job error.");
            }

            return job.OutputMediaAssets[0];
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
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

## <a name="need-help"></a>Potrzebujesz pomocy?

Możesz otworzyć bilet pomocy technicznej, przechodząc do [nowego żądania pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
