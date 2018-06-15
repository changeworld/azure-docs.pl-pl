---
title: Kodowanie elementu zawartości z standardu Media Encoder Standard przy użyciu platformy .NET | Dokumentacja firmy Microsoft
description: W tym artykule pokazano, jak używać .NET do zasobów przy użyciu Media Encoder standardowa kodowania.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 2964be45c98350fb1f3c82f25716943493979240
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788683"
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Kodowanie elementu zawartości z standardu Media Encoder Standard przy użyciu platformy .NET
Zadania kodowania to jedna z operacji najczęściej przeprowadzanych przy użyciu usługi Media Services. Zadania kodowania są tworzone w celu konwertowania plików multimediów z jednego formatu kodowania na inny. Podczas kodowania, można użyć wbudowanych Media Encoder Media Services. Można również użyć kodera świadczonych przez partnera usługi Media Services; kodery innych firm są dostępne za pośrednictwem portalu Azure Marketplace. 

W tym artykule pokazano, jak używać .NET do kodowania zasobów z Media Encoder Standard (rynkowej). Media Encoder Standard została skonfigurowana przy użyciu jednego z koderów ustawienia opisane [tutaj](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

Zalecane jest zawsze kodowanie plików źródłowych do adaptacyjnej szybkości bitowej MP4 zestawu, a następnie wykonać konwersję zestaw na żądany format za pomocą [dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md). 

Jeśli dane wyjściowe zawartości jest szyfrowany w magazynie, musisz skonfigurować zasady dostarczania elementu zawartości. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania elementów zawartości](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> Rynkowej tworzy plik wyjściowy z nazwy, która zawiera najpierw 32 znaków nazwy pliku wejściowego. Nazwa jest oparty na nazwie określonej w istniejących plików. Na przykład, "FileName": "{nazwę bazową} _ {Index} {rozszerzenia}". {Nazwę bazową} zostanie zastąpiony przez pierwsze 32 znaków nazwy pliku wejściowego.
> 
> 

### <a name="mes-formats"></a>Formaty rynkowej
[Koderów-dekoderów i formaty](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>Ustawienia wstępne usługi MES
Media Encoder Standard została skonfigurowana przy użyciu jednego z koderów ustawienia opisane [tutaj](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Wejście i wyjście metadanych
Podczas kodowania zasób wejściowy (lub zasoby), za pomocą rynkowej, Pobierz zasób dane wyjściowe po pomyślnym zakończeniu tego kodowania zadań. Elementu zawartości wyjściowej zawiera wideo, audio, miniatur, manifestu, itp., oparte na ustawienie kodowania, które można użyć.

Elementu zawartości wyjściowej zawiera także plik o metadane dotyczące zasobu wejściowego. Nazwa pliku XML metadanych ma następujący format: < asset_id > _metadata.xml (na przykład 41114ad3-eb5e - 4c d 57 8 92-5354e2b7d4a4_metadata.xml), gdzie < asset_id > jest wartość IDśrodkaTrwałego zasobów wejściowych. Schemat wejściowego XML metadanych jest opisany [tutaj](media-services-input-metadata-schema.md).

Elementu zawartości wyjściowej zawiera także plik o metadane dotyczące zawartości wyjściowej. Nazwa pliku XML metadanych ma następujący format: < source_file_name > _manifest.xml (na przykład BigBuckBunny_manifest.xml). Schemat opisano XML metadanych dane wyjściowe [tutaj](media-services-output-metadata-schema.md).

Jeśli chcesz sprawdzić z jednej z dwóch plików metadanych, można utworzyć lokalizatora SAS i Pobierz plik na komputerze lokalnym. Przykład można znaleźć na tworzenie lokalizatora SAS i Pobierz plik przy użyciu rozszerzenia SDK .NET usługi Media Services.

## <a name="download-sample"></a>Pobieranie przykładu
Możesz pobrać i uruchom przykład pokazujący sposób kodowania z rynkowej z [tutaj](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="net-sample-code"></a>.NET przykładowy kod

Poniższy przykład kodu wykorzystuje .NET SDK usługi Media Services do wykonywania następujących zadań:

* Utwórz zadania kodowania.
* Pobierz odwołanie do kodera Media Encoder Standard.
* Określ użycie [adaptacyjne przesyłanie strumieniowe](media-services-autogen-bitrate-ladder-with-mes.md) wstępnie zdefiniowane. 
* Dodaj pojedynczego zadania kodowania do zadania. 
* Określ wejściowych zasobów do zakodowania.
* Tworzenie zasobu wyjściowy, który zawiera zakodowanym elementem zawartości.
* Dodaj program obsługi zdarzeń, aby sprawdzić postęp zadania.
* Przesłać zadanie.

#### <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Przykład 

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderStandardSample
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

            // Create a task with the encoding details, using a string preset.
            // In this case "Adaptive Streaming" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
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

## <a name="advanced-encoding-features-to-explore"></a>Funkcje zaawansowane kodowanie do eksplorowania
* [Sposób generowania miniatur](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Trwa generowanie miniatur podczas kodowania](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Filmy wideo przycięcia podczas kodowania](media-services-crop-video.md)
* [Dostosowywanie ustawień predefiniowanych](media-services-custom-mes-presets-with-dotnet.md)
* [Nakładki lub znak wodny wideo z obrazem](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Kolejne kroki
[Sposób generowania miniatury z platformą .NET przy użyciu standardu Media Encoder Standard](media-services-dotnet-generate-thumbnail-with-mes.md)
[usługi Media kodowanie — omówienie](media-services-encode-asset.md)

