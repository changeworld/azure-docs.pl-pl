---
title: Digitalizacja tekstu za pomocą usługi Azure Media Analytics OCR | Dokumenty firmy Microsoft
description: Usługa Azure Media Analytics OCR (optyczne rozpoznawanie znaków) umożliwia konwertowanie zawartości tekstowej w plikach wideo na edytowalny, z możliwością przeszukiwania tekstu cyfrowego.  Pozwala to zautomatyzować wyodrębnianie znaczących metadanych z sygnału wideo multimediów.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 307c196e-3a50-4f4b-b982-51585448ffc6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 11889bd6df0bcc9564c17fdaacc333df1d418660
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918340"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Konwertowanie zawartości tekstowej w plikach wideo na tekst cyfrowy za pomocą usługi Azure Media Analytics  

> [!NOTE]
> Procesor multimediów **OCR usługi Azure Media** zostanie wycofany. W przypadku daty wycofania zobacz temat [starszych składników.](legacy-components.md)

## <a name="overview"></a>Omówienie
Jeśli chcesz wyodrębnić zawartość tekstową z plików wideo i wygenerować edytowalny, przeszukiwalny tekst cyfrowy, należy użyć usługi Azure Media Analytics OCR (optyczne rozpoznawanie znaków). Ten procesor multimediów azure wykrywa zawartość tekstową w plikach wideo i generuje pliki tekstowe do użytku. OCR umożliwia automatyzację wyodrębniania znaczących metadanych z sygnału wideo multimediów.

W połączeniu z wyszukiwarką można łatwo indeksować multimedia za pomocą tekstu i zwiększać wykrywalność treści. Jest to niezwykle przydatne w wysoce tekstowych wideo, takich jak nagrywanie wideo lub przechwytywanie ekranu prezentacji pokazu slajdów. Procesor multimediów OCR platformy Azure jest zoptymalizowany pod kątem tekstu cyfrowego.

Procesor nośników **OCR azure** jest obecnie w wersji zapoznawczej.

Ten artykuł zawiera szczegółowe informacje na temat **usługi Azure Media OCR** i pokazuje, jak go używać z zestawem SDK usługi Media Services dla platformy .NET. Aby uzyskać więcej informacji i przykładów, zobacz [ten blog](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>Pliki wejściowe OCR
Pliki wideo. Obecnie obsługiwane są następujące formaty: MP4, MOV i WMV.

## <a name="task-configuration"></a>Konfiguracja zadania
Konfiguracja zadania (ustawienia wstępne). Podczas tworzenia zadania za pomocą **usługi Azure Media OCR**należy określić predefiniowane ustawienia konfiguracji przy użyciu języka JSON lub XML. 

>[!NOTE]
>Aparat OCR przyjmuje tylko obszar obrazu z co najmniej 40 pikseli do maksymalnie 32000 pikseli jako prawidłowe dane wejściowe w obu wysokości /szerokości.
>

### <a name="attribute-descriptions"></a>Opisy atrybutów
| Nazwa atrybutu | Opis |
| --- | --- |
|Zaawansowanaoutput| Jeśli ustawisz AdvancedOutput do true, dane wyjściowe JSON będzie zawierać dane pozycyjne dla każdego wyrazu (oprócz fraz i regionów). Jeśli nie chcesz widzieć tych szczegółów, ustaw flagę na false. Wartość domyślna to false. Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/).|
| Język |(opcjonalnie) opisuje język tekstu, którego należy szukać. Jedna z następujących opcji: AutoDetect (domyślnie), arabski, chiński uproszczony, chińskiTradicyjny, czeski duński, holenderski, angielski, fiński, francuski, niemiecki, grecki, węgierski, włoski, japoński, koreański, norweski, polski, portugalski, rumuński, rosyjski, Serbskicyrilliczny, serbskiLatin, słowacki, hiszpański, szwedzki, turecki. |
| Textorientation |(opcjonalnie) opisuje orientację tekstu, dla którego należy szukać.  "Lewy" oznacza, że górna część wszystkich liter jest skierowana w lewo.  Domyślny tekst (podobny do tego, który można znaleźć w książce) można nazwać "W górę".  Jedna z następujących opcji: AutoDetect (domyślnie), W górę, W prawo, W dół, W lewo. |
| CzasInterwal |(opcjonalnie) opisuje częstotliwość pobierania próbek.  Wartość domyślna to co 1/2 sekundy.<br/>JSON format – HH:mm:ss. SSS (domyślnie 00:00:00.500)<br/>Format XML — pierwotny czas trwania W3C XSD (domyślny PT0.5) |
| Wykrytoregiony |(opcjonalnie) Tablica detectregion obiektów określających regiony w ramce wideo, w którym do wykrywania tekstu.<br/>Obiekt DetectRegion składa się z następujących czterech wartości całkowitych:<br/>Po lewej – piksele z lewego marginesu<br/>Góra – piksele z górnego marginesu<br/>Szerokość – szerokość regionu w pikselach<br/>Wysokość – wysokość regionu w pikselach |

#### <a name="json-preset-example"></a>Przykład ustawień predefiniowanych JSON

```json
    {
        "Version":1.0, 
        "Options": 
        {
            "AdvancedOutput":"true",
            "Language":"English", 
            "TimeInterval":"00:00:01.5",
            "TextOrientation":"Up",
            "DetectRegions": [
                    {
                       "Left": 10,
                       "Top": 10,
                       "Width": 100,
                       "Height": 50
                    }
             ]
        }
    }
```

#### <a name="xml-preset-example"></a>Przykład ustawień predefiniowanych XML

```xml
    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""https://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""https://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""https://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <AdvancedOutput>true</AdvancedOutput>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>10</Left>
                   <Top>10</Top>
                   <Width>100</Width>
                   <Height>50</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>
```

## <a name="ocr-output-files"></a>Pliki wyjściowe OCR
Dane wyjściowe procesora nośników OCR to plik JSON.

### <a name="elements-of-the-output-json-file"></a>Elementy wyjściowego pliku JSON
Wyjście OCR wideo zapewnia dane segmentowe na znakach znalezionych w filmie.  Można użyć atrybutów, takich jak język lub orientacja, aby doskonalić dokładnie słowa, które są zainteresowane analizą. 

Dane wyjściowe zawierają następujące atrybuty:

| Element | Opis |
| --- | --- |
| Skala czasu |"kleszcze" na sekundę filmu |
| Przesunięcie |przesunięcie czasu dla znaczników czasu. W wersji 1.0 interfejsów API wideo zawsze będzie to 0. |
| Framerate |Klatki na sekundę filmu |
| szerokość |szerokość wideo w pikselach |
| height |wysokość wideo w pikselach |
| Fragments |tablica fragmentów wideo opartych na czasie, do których są fragmentowane metadane |
| rozpoczynanie |godzina rozpoczęcia fragmentu w "kleszczach" |
| czas trwania |długość fragmentu w "kleszczach" |
| interval |odstęp czasu każdego zdarzenia w obrębie danego fragmentu |
| zdarzenia |tablica zawierająca regiony |
| region |obiekt reprezentujący wykryte słowa lub frazy |
| language |język tekstu wykrytego w obrębie regionu |
| Orientacji |orientacja tekstu wykrytego w obrębie regionu |
| linie |tablica wierszy tekstu wykrytych w obrębie regionu |
| tekst |rzeczywisty tekst |

### <a name="json-output-example"></a>Przykład danych wyjściowych JSON
Poniższy przykład wyjściowy zawiera ogólne informacje wideo i kilka fragmentów wideo. W każdym fragmencie wideo zawiera każdy region, który jest wykrywany przez OCR MP z językiem i jego orientacją tekstową. Region zawiera również każdy wiersz wyrazu w tym regionie z tekstem wiersza, położeniem wiersza i każdą informacją o słowie (zawartość wyrazu, pozycję i zaufanie) w tym wierszu. Oto przykład, i umieścić kilka komentarzy inline.

```json
    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }
```

## <a name="net-sample-code"></a>Przykładowy kod platformy .NET

W poniższym programie pokazano, jak:

1. Utwórz zasób i przekaż plik multimedialny do zasobu.
2. Utwórz zadanie z plikiem konfiguracji/ustawień predefiniowanych OCR.
3. Pobierz wyjściowe pliki JSON. 
   
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

namespace OCR
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

            // Run the OCR job.
            var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                        @"C:\supportFiles\OCR\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
        }

        static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My OCR Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My OCR Job");

            // Get a reference to Azure Media OCR.
            string MediaProcessorName = "Azure Media OCR";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My OCR Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);

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

