---
title: Kodowanie zasobu za pomocą standardu media encoder przy użyciu platformy .NET | Dokumenty firmy Microsoft
description: W tym artykule pokazano, jak używać platformy .NET do kodowania zasobu przy użyciu standardu media encoder.
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
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 259e32d55f25c4a146b7ff358eb503763dd5fab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69016589"
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Kodowanie zasobu za pomocą standardu media encoder przy użyciu platformy .NET  

Zadania kodowania są jedną z najczęstszych operacji przetwarzania w umiań media. Tworzenie zadań kodowania do konwersji plików multimedialnych z jednego kodowania do drugiego. Podczas kodowania można użyć wbudowanego kodera multimediów usługi Media Services. Można również użyć kodera dostarczonego przez partnera usługi Media Services; kodery innych firm są dostępne za pośrednictwem portalu Azure Marketplace. 

W tym artykule pokazano, jak używać platformy .NET do kodowania zasobów za pomocą usługi Media Encoder Standard (MES). Media Encoder Standard jest skonfigurowany przy użyciu jednego z predefiniowanych koderów opisanych [tutaj.](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)

Zaleca się, aby zawsze kodować pliki źródłowe do adaptacyjnego zestawu MP4 o szybkości transmisji bitów, a następnie konwertować zestaw do żądanego formatu za pomocą [dynamicznego pakowania](media-services-dynamic-packaging-overview.md). 

Jeśli zasób wyjściowy jest zaszyfrowany magazynem, należy skonfigurować zasady dostarczania zasobów. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania zasobów](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> Mes tworzy plik wyjściowy o nazwie, która zawiera pierwsze 32 znaki nazwy pliku wejściowego. Nazwa jest oparta na tym, co jest określone w pliku predefiniowanych. Na przykład "Nazwa pliku": "{Basename}_{Index}{Extension}". {Basename} zastępuje pierwsze 32 znaki nazwy pliku wejściowego.
> 
> 

### <a name="mes-formats"></a>Formaty MES
[Formaty i kodeki](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>Ustawienia wstępne usługi MES
Media Encoder Standard jest skonfigurowany przy użyciu jednego z predefiniowanych koderów opisanych [tutaj.](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)

### <a name="input-and-output-metadata"></a>Metadane wejściowe i wyjściowe
Podczas kodowania zasobu wejściowego (lub zasobów) przy użyciu mes, otrzymasz zasób wyjściowy po pomyślnym zakończeniu tego zadania kodowania. Zasób wyjściowy zawiera wideo, audio, miniatury, manifesty itp., na podstawie używanego ustawienia kodowania.

Zasób wyjściowy zawiera również plik z metadanymi dotyczącymi zasobu wejściowego. Nazwa pliku XML metadanych ma następujący format: <asset_id>_metadata.xml (na przykład 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), gdzie <asset_id> jest wartością AssetId zasobu wejściowego. Schemat tego pliku XML metadanych wejściowych jest opisany [w tym miejscu](media-services-input-metadata-schema.md).

Zasób wyjściowy zawiera również plik z metadanymi dotyczącymi zasobu wyjściowego. Nazwa pliku XML metadanych ma następujący format: <source_file_name>_manifest.xml (na przykład BigBuckBunny_manifest.xml). Schemat tego wyjściowego metadanego XML jest opisany [w tym miejscu](media-services-output-metadata-schema.md).

Jeśli chcesz sprawdzić jeden z dwóch plików metadanych, możesz utworzyć lokalizator sygnatury dostępu Współdzielonego i pobrać plik na komputer lokalny. Można znaleźć przykład tworzenia lokalizatora sygnatury dostępu Współdzielonego i pobierania pliku przy użyciu rozszerzeń SDK usługi Media Services .NET.

## <a name="download-sample"></a>Pobieranie próbki
Możesz pobrać i uruchomić próbkę, która pokazuje, jak zakodować mes [stąd](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="net-sample-code"></a>Przykładowy kod platformy .NET

Poniższy przykład kodu używa media services .NET SDK do wykonywania następujących zadań:

* Utwórz zadanie kodowania.
* Uzyskaj odwołanie do kodera Media Encoder Standard.
* Określ, aby użyć predefiniowanych ustawień [przesyłania strumieniowego adaptacyjnego.](media-services-autogen-bitrate-ladder-with-mes.md) 
* Dodaj do zadania pojedyncze zadanie kodowania. 
* Określ zasób wejściowy, który ma być zakodowany.
* Utwórz zasób wyjściowy zawierający zakodowany zasób.
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

## <a name="advanced-encoding-features-to-explore"></a>Zaawansowane funkcje kodowania do zbadania
* [Jak generować miniatury](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Generowanie miniatur podczas kodowania](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Przycinanie klipów wideo podczas kodowania](media-services-crop-video.md)
* [Dostosowywanie ustawień predefiniowanych kodowania](media-services-custom-mes-presets-with-dotnet.md)
* [Nakładanie lub znak wodny wideo z obrazem](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Następne kroki
[Jak wygenerować miniaturę przy użyciu standardu media encoder z](media-services-dotnet-generate-thumbnail-with-mes.md)
[omówieniem kodowania programu](media-services-encode-asset.md) .NET Media Services

