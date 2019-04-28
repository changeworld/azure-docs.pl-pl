---
title: Kodowanie elementu zawartości za pomocą usługi Media Encoder Standard za pomocą platformy .NET | Dokumentacja firmy Microsoft
description: W tym artykule pokazano, jak kodowanie elementu zawartości przy użyciu usługi Media Encoder Standard za pomocą platformy .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako;anilmur
ms.openlocfilehash: d3eb2affe76374eb35ac724dff0204f43b567e09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61225732"
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Kodowanie elementu zawartości za pomocą usługi Media Encoder Standard za pomocą platformy .NET  

Zadania kodowania to jedna z operacji najczęściej przeprowadzanych przy użyciu usługi Media Services. Zadania kodowania są tworzone w celu konwertowania plików multimediów z jednego formatu kodowania na inny. Podczas kodowania, możesz użyć wbudowanych Media Encoder usługi Media Services. Można również użyć koder świadczonych przez partnera usługi Media Services; kodery innych firm są dostępne za pośrednictwem portalu Azure Marketplace. 

W tym artykule przedstawiono sposób kodowania elementów zawartości przy użyciu Media Encoder Standard (MES) za pomocą platformy .NET. Usługi Media Encoder Standard jest skonfigurowany przy użyciu jednego z ustawień wstępnych koderów opisem [tutaj](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

Zalecane jest zawsze kodowanie plików źródłowych do adaptacyjną szybkością transmisji bitów zestawu w formacie MP4, a następnie wykonać konwersję zestawu za pomocą żądany format [funkcję dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md). 

Jeśli element zawartości danych wyjściowych jest szyfrowany w magazynie, należy skonfigurować zasady dostarczania elementu zawartości. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania elementów zawartości](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> Usługi MES tworzy plik wyjściowy o nazwie, która zawiera pierwszych 32 znaków nazwy pliku wejściowego. Nazwa jest oparty na został określony w pliku wstępnie zdefiniowane. Na przykład, "FileName": "{Basename} _ {Index} {Extension}". {Basename} jest zastępowany przez pierwszych 32 znaków nazwy pliku wejściowego.
> 
> 

### <a name="mes-formats"></a>Formaty usługi MES
[Formaty i kodery-dekodery](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>Ustawienia wstępne usługi MES
Usługi Media Encoder Standard jest skonfigurowany przy użyciu jednego z ustawień wstępnych koderów opisem [tutaj](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Metadane wejściowe i wyjściowe
Podczas kodowania wejściowego elementu (lub zasoby), za pomocą usługi MES, pobieranie elementu zawartości wyjściowej po pomyślnym zakończeniu tego kodowania zadań. Elementu zawartości wyjściowej zawiera film wideo, audio, miniatury, manifest, itp., w oparciu o ustawienia wstępne kodowania, którego używasz.

Elementu zawartości wyjściowej zawiera także plik o metadane dotyczące zasobu danych wejściowych. Nazwa pliku XML metadanych ma następujący format: < asset_id > _metadata.xml (na przykład, d 57 8 41114ad3-eb5e — 4 rdzeni 92-5354e2b7d4a4_metadata.xml), gdzie < asset_id > jest wartość AssetId trwałego danych wejściowych. Schemat XML metadanych danych wejściowych jest opisany [tutaj](media-services-input-metadata-schema.md).

Elementu zawartości wyjściowej zawiera także plik o metadane dotyczące elementu zawartości wyjściowej. Nazwa pliku XML metadanych ma następujący format: < source_file_name > _manifest.xml (na przykład BigBuckBunny_manifest.xml). Schemat metadanych danych wyjściowych XML jest opisany [tutaj](media-services-output-metadata-schema.md).

Jeśli chcesz sprawdzić z jednej z dwóch plików metadanych, możesz utworzyć lokalizatora SAS następuje i pobrać plik na komputer lokalny. Przykład można znaleźć na temat sposobu tworzenia lokalizatora SAS następuje i Pobierz plik przy użyciu rozszerzenia SDK .NET usługi Media Services.

## <a name="download-sample"></a>Pobieranie przykładu
Możesz pobrać i uruchomić przykład, który pokazuje, jak kodowanie za pomocą usługi MES z [tutaj](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="net-sample-code"></a>Przykładowy kod .NET

Poniższy przykład kodu używa Media Services .NET SDK do wykonywania następujących zadań:

* Utwórz zadania kodowania.
* Pobierz odwołanie do usługi Media Encoder Standard kodera.
* Określić, czy użyć [adaptacyjnego przesyłania strumieniowego](media-services-autogen-bitrate-ladder-with-mes.md) wstępnie zdefiniowane. 
* Dodaj pojedynczego zadania kodowania do zadania. 
* Określ wejściowego elementu do zakodowania.
* Tworzenie zasobu danych wyjściowych, który zawiera zakodowanym elementem zawartości.
* Dodaj program obsługi zdarzeń, aby sprawdzić postęp zadania.
* Przesyłanie zadania.

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

## <a name="advanced-encoding-features-to-explore"></a>Zaawansowane funkcje kodowania, aby zapoznać się z
* [Generowanie miniatur](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Generowanie miniatur podczas kodowania](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Przycinanie wideo podczas kodowania](media-services-crop-video.md)
* [Dostosowywanie ustawienia wstępne kodowania](media-services-custom-mes-presets-with-dotnet.md)
* [Nakładki lub znaku wodnego wideo z obrazem](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Kolejne kroki
[Sposób generowania miniatur za pomocą usługi Media Encoder Standard za pomocą platformy .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
[omówienie kodowanie usług Media](media-services-encode-asset.md)

