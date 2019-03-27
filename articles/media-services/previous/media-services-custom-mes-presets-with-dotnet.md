---
title: Dostosowywanie ustawień wstępnych usługi Media Encoder Standard | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak przeprowadzić Zaawansowane kodowanie za pomocą usługi Media Encoder Standard zadań wstępne dostosowania. Temat pokazuje, jak używać zestawu SDK .NET usługi Media Services do tworzenia, kodowania zadań i zadań. Pokazano również, jak do dostarczania niestandardowych ustawień wstępnych do kodowania zadania.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ec95392f-d34a-4c22-a6df-5274eaac445b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2019
ms.author: juliako
ms.openlocfilehash: eaea0719aa8ca981da68841dae365decdd3c860e
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499866"
---
# <a name="customizing-media-encoder-standard-presets"></a>Ustawienia wstępne Dostosowywanie Media Encoder Standard  

## <a name="overview"></a>Przegląd

Ten artykuł pokazuje, jak wykonywać zaawansowane kodowanie za pomocą Media Encoder Standard (MES) przy użyciu niestandardowego ustawienia wstępnego. Artykuł będzie używał programu .NET do tworzenia zadania kodowania i zadanie, które wykonuje to zadanie.  

W tym artykule pokazano, jak dostosować ustawienia domyślne, wykonując [wielu szybkość transmisji bitów H264 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) ustawienie wstępne i zmniejsza liczbę warstw. [Dostosowywanie Media Encoder Standard ustawienia wstępne](media-services-advanced-encoding-with-mes.md) artykuł przedstawia niestandardowych ustawień wstępnych, które mogą służyć do wykonywania zaawansowanych zadań kodowania.

> [!NOTE]
> Nie można użyć niestandardowych ustawień wstępnych, opisane w tym artykule w [Media Services V3](https://docs.microsoft.com/azure/media-services/latest/) przekształcenia lub poleceń interfejsu wiersza polecenia. Zobacz [wskazówek dotyczących migracji od v2 do v3](../latest/migrate-from-v2-to-v3.md) Aby uzyskać więcej informacji.

## <a id="customizing_presets"></a> Dostosowywanie ustawienie wstępne usługi MES

### <a name="original-preset"></a>Oryginalnego ustawienia wstępnego

Zapisz dane JSON, zdefiniowana w [wielu szybkość transmisji bitów H264 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) artykułu w niektóre pliki z rozszerzeniem .json. Na przykład **CustomPreset_JSON.json**.

### <a name="customized-preset"></a>Niestandardowe ustawienie wstępne

Otwórz **CustomPreset_JSON.json** pliku i usuń pierwsze trzy warstwy z **H264Layers** więc plik wygląda następująco.

```json 
    {  
      "Version": 1.0,  
      "Codecs": [  
        {  
          "KeyFrameInterval": "00:00:02",  
          "H264Layers": [  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 1000,  
              "MaxBitrate": 1000,  
              "BufferWindow": "00:00:05",  
              "Width": 640,  
              "Height": 360,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            },  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 650,  
              "MaxBitrate": 650,  
              "BufferWindow": "00:00:05",  
              "Width": 640,  
              "Height": 360,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            },  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 400,  
              "MaxBitrate": 400,  
              "BufferWindow": "00:00:05",  
              "Width": 320,  
              "Height": 180,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            }  
          ],  
          "Type": "H264Video"  
        },  
        {  
          "Profile": "AACLC",  
          "Channels": 2,  
          "SamplingRate": 48000,  
          "Bitrate": 128,  
          "Type": "AACAudio"  
        }  
      ],  
      "Outputs": [  
        {  
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
          "Format": {  
            "Type": "MP4Format"  
          }  
        }  
      ]  
    }  
```

## <a id="encoding_with_dotnet"></a>Kodowanie za pomocą usługi Media Services .NET SDK

Poniższy przykład kodu używa Media Services .NET SDK do wykonywania następujących zadań:

- Utwórz zadania kodowania.
- Pobierz odwołanie do usługi Media Encoder Standard kodera.
- Załaduj niestandardowego ustawienia wstępnego JSON, który został utworzony w poprzedniej sekcji. 
  
        // Load the JSON from the local file.
        string configuration = File.ReadAllText(fileName);  

- Dodaj zadanie kodowania do zadania. 
- Określ wejściowego elementu do zakodowania.
- Tworzenie zasobu danych wyjściowych, który zawiera zakodowanym elementem zawartości.
- Dodaj program obsługi zdarzeń, aby sprawdzić postęp zadania.
- Przesyłanie zadania.
   
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

namespace CustomizeMESPresests
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

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using custom presets.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("CustomPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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

## <a name="see-also"></a>Zobacz także

- [Jak kodować z niestandardowe przekształcenia przy użyciu interfejsu wiersza polecenia](../latest/custom-preset-cli-howto.md)
- [Kodowania z Media Services v3](../latest/encoding-concept.md)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Omówienie kodowania usługi Media Services](media-services-encode-asset.md)

