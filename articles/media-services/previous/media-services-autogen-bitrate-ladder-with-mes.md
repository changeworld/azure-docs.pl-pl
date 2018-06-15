---
title: Użyj usługi Azure Media Encoder Standard, aby automatycznie wygenerować drabinę szybkości transmisji bitów | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono sposób Media Encoder Standard (rynkowej) umożliwia automatyczne generowanie drabinę szybkości transmisji bitów, na podstawie rozdzielczość i szybkość transmisji bitów. Rozdzielczość i szybkość transmisji bitów nigdy nie zostanie przekroczony. Na przykład jeśli dane wejściowe są 720p w 3 MB/s, dane wyjściowe będą pozostawać w najlepszym 720p i rozpocznie stawkami niższa niż 3 MB/s.
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
ms.date: 12/10/2017
ms.author: juliako
ms.openlocfilehash: 80f76f413ec2c267ba8fb93514480e168563f470
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788627"
---
#  <a name="use-azure-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Użyj usługi Azure Media Encoder Standard, aby automatycznie wygenerować drabinę szybkości transmisji bitów

## <a name="overview"></a>Przegląd

W tym artykule przedstawiono sposób Media Encoder Standard (rynkowej) umożliwia automatyczne generowanie drabinę szybkości transmisji bitów (pary rozpoznawania szybkości transmisji bitów) na podstawie rozdzielczość i szybkość transmisji bitów. Nigdy nie przekroczy ustawienie generowane automatycznie, rozdzielczość i szybkość transmisji bitów. Na przykład jeśli dane wejściowe są 720p 3 MB/s, dane wyjściowe w najlepszym pozostaje 720p i rozpocznie stawkami niższa niż 3 MB/s.

### <a name="encoding-for-streaming-only"></a>Kodowanie tylko przesyłania strumieniowego

Jeśli Twoje zamierzeniu zakodować źródłowy plik wideo tylko do przesyłania strumieniowego, następnie należy użyć "Adaptacyjne przesyłanie strumieniowe" ustawienia wstępnego podczas tworzenia zadania kodowania. Korzystając z **adaptacyjne przesyłanie strumieniowe** zdefiniowane, koder rynkowej sposób inteligentny będzie cap drabinę szybkości transmisji bitów. Jednak nie będzie mogła kontroli kodowanie koszty, ponieważ usługa określa, jak wiele warstw do użycia i jakie rozdzielczości. Zawiera przykłady warstw wyjściowego utworzonego przez rynkowej wyniku z kodowaniem **adaptacyjne przesyłanie strumieniowe** ustawień wstępnych. na końcu tego artykułu. Dane wyjściowe zawartości zawiera pliki MP4, gdzie audio i wideo nie ma przeplotu.

### <a name="encoding-for-streaming-and-progressive-download"></a>Kodowanie używane do przesyłania strumieniowego i pobierania progresywnego

Z celem jest, aby zakodować źródłowy plik wideo do przesyłania strumieniowego, a także do tworzenia plików MP4 do pobierania progresywnego, należy użyć "zawartości adaptacyjną wielu szybkości transmisji bitów MP4" ustawienia wstępnego podczas tworzenia zadania kodowania. Korzystając z **zawartości adaptacyjną wielu MP4 szybkości transmisji bitów** zdefiniowane, koder rynkowej stosuje tej samej logiki kodowania, powyżej, ale teraz elementu zawartości wyjściowej będzie zawierać pliki MP4, gdzie audio i wideo jest przeplatana. Można użyć jednego z tych plików MP4 (na przykład najwyższa wersja szybkości transmisji bitów) jako plik pobierania progresywnego.

## <a id="encoding_with_dotnet"></a>Kodowanie w usłudze Media Services zestawu .NET SDK

Poniższy przykład kodu wykorzystuje .NET SDK usługi Media Services do wykonywania następujących zadań:

- Utwórz zadania kodowania.
- Pobierz odwołanie do kodera Media Encoder Standard.
- Dodaj zadanie kodowania zadania i określanie użycia **adaptacyjne przesyłanie strumieniowe** wstępnie zdefiniowane. 
- Tworzenie zasobu wyjściowy, który zawiera zakodowanym elementem zawartości.
- Dodaj program obsługi zdarzeń, aby sprawdzić postęp zadania.
- Przesłać zadanie.

#### <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Przykład

```
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace AdaptiveStreamingMESPresest
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

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using the "Adaptive Streaming" preset.
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

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            "Adaptive Streaming",
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

## <a id="output"></a>Dane wyjściowe

W tej sekcji przedstawiono trzy przykłady warstw wyjściowego utworzonego przez rynkowej wyniku z kodowaniem **adaptacyjne przesyłanie strumieniowe** wstępnie zdefiniowane. 

### <a name="example-1"></a>Przykład 1
Źródło o wysokość "1080" i "29.970" szybkość klatek tworzy 6 warstwy wideo:

|Warstwy|Wysokość|Szerokość|Bitrate(Kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Przykład 2
Źródło o wysokość "720" i "23.970" szybkość klatek tworzy 5 warstwy wideo:

|Warstwy|Wysokość|Szerokość|Bitrate(Kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Przykład 3
Źródło o wysokość "360" i "29.970" szybkość klatek tworzy 3 warstwy wideo:

|Warstwy|Wysokość|Szerokość|Bitrate(Kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Usługi multimediów kodowania — omówienie](media-services-encode-asset.md)

