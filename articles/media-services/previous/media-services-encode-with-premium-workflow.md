---
title: Zaawansowane kodowania w przepływie pracy Premium kodera multimediów | Dokumentacja firmy Microsoft
description: Dowiedz się, jak do zakodowania w przepływie pracy Premium kodera multimediów. Przykłady kodu są napisane w języku C# i używają SDK usługi Media Services dla platformy .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: 9b341b244d53993699dfc9096a86305def82cad7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788704"
---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Zaawansowane encoding w usłudze Media Encoder Premium w przepływie pracy
> [!NOTE]
> Procesor multimediów Media Encoder Premium w przepływie pracy omówione w tym artykule nie jest dostępna w Chinach.
>
>

Odpowiedzi na pytania koder premium, poczty e-mail mepd@microsoft.com.

## <a name="overview"></a>Przegląd
Wprowadzenie do usługi Microsoft Azure Media Services **Media Encoder Premium w przepływie pracy** procesor multimediów. To kodowanie możliwości przepływy pracy na żądanie premium wcześniejszym oferty procesora.

W poniższych tematach przedstawiono szczegółowe informacje dotyczące **Media Encoder Premium w przepływie pracy**:

* [Formatuje obsługiwane przez przepływ pracy Media Encoder Premium](media-services-premium-workflow-encoder-formats.md) — Discusses pliku formatuje i kodery-dekodery obsługiwane przez **Media Encoder Premium w przepływie pracy**.
* [Omówienie i porównanie koderów Azure multimediów na żądanie](media-services-encode-asset.md) porównuje kodowania możliwości **Media Encoder Premium w przepływie pracy** i **Media Encoder Standard**.

W tym artykule przedstawiono sposób kodowania z **Media Encoder Premium w przepływie pracy** przy użyciu platformy .NET.

Kodowanie zadania **Media Encoder Premium w przepływie pracy** wymagają osobną konfiguracją pliku o nazwie pliku przepływu pracy. Te pliki mają rozszerzenie .workflow i są tworzone przy użyciu [projektanta przepływów pracy](media-services-workflow-designer.md) narzędzia.

Można także uzyskać domyślne pliki przepływu pracy [tutaj](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Folder zawiera również opis tych plików.

Pliki przepływu pracy muszą zostać przekazana do konta usługi Media Services jako zasób, a ten zasób należy przekazać do kodowania zadań.

## <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md). 

## <a name="encoding-example"></a>Przykład kodowania

W poniższym przykładzie pokazano sposób kodowania z **Media Encoder Premium w przepływie pracy**.

Są wykonywane następujące czynności:

1. Utworzenie elementu zawartości i przekazywanie pliku przepływu pracy.
2. Utworzenie elementu zawartości i przekazywanie pliku nośnika źródłowego.
3. Pobierz nośnika procesor "Media Encoder Premium przepływu pracy".
4. Utwórz zadania i zadania.

    W większości przypadków ciągu konfiguracji zadania jest pusty (takich jak w poniższym przykładzie). Niektórych zaawansowanych scenariuszy (które wymagają dynamicznie ustawienia właściwości środowisko uruchomieniowe) w takim przypadku zapewni ciągu XML do kodowania zadań. Przykłady takich scenariuszy: tworzenie nakładki, równoległe i sekwencyjne nośnika łączenia, napisy.
5. Dodaj dwa zasoby wejściowych do zadania.

    1. 1 — zasobów przepływu pracy.
    2. 2 — zawartości wideo.

    >[!NOTE]
    >Zasobów przepływu pracy musi zostać dodany do zadań przed zasobu multimediów.
   Ciąg konfiguracji tego zadania powinna być pusta.
   
6. Przesyłania zadania kodowania.

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

Odpowiedzi na pytania koder premium, poczty e-mail mepd@microsoft.com.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
