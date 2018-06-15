---
title: Digitize tekst z Rozpoznawania analizy multimediów Azure | Dokumentacja firmy Microsoft
description: Rozpoznawania analizy w usłudze Azure Media (OCR) można przekonwertować na tekst cyfrowy można edytować, wyszukiwanie, zawartość tekstu w plikach wideo.  Dzięki temu można zautomatyzować wyodrębniania łatwy do rozpoznania metadanych z sygnału wideo multimediów.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 307c196e-3a50-4f4b-b982-51585448ffc6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: 4a7a31b4e0069d2c94a4f109248d7b02c0b03faa
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790398"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Umożliwia konwertowanie zawartości tekstowej w plikach wideo na cyfrowe tekst analizy multimediów Azure
## <a name="overview"></a>Przegląd
Jeśli potrzebujesz do wyodrębniania zawartości tekstu z plików wideo oraz do generowania edytowalny, wyszukiwanie tekstu cyfrowych, należy użyć Rozpoznawania analizy multimediów Azure (OCR). Ten procesor multimediów Azure wykrywa zawartości tekstowej w plikach wideo i generuje pliki tekstowe do użycia. Rozpoznawania pozwala zautomatyzować wyodrębniania łatwy do rozpoznania metadanych z sygnału wideo multimediów.

Gdy jest używany w połączeniu z aparatu wyszukiwania, możesz łatwo indeksu multimediów tekst i zwiększyć wykrywalność zawartości. Jest to bardzo przydatne w dużej tekstową wideo, takich jak nagrywanie wideo lub Przechwytywanie ekranu prezentacji pokazu slajdów. Procesor multimediów Azure Rozpoznawania jest zoptymalizowana pod kątem cyfrowe tekstu.

**Azure Media Rozpoznawania** procesor multimediów jest obecnie w przeglądzie.

Ten artykuł zawiera szczegółowe informacje o **Azure Media Rozpoznawania** i pokazuje, jak z niego korzystać z zestawu SDK usługi Media Services dla platformy .NET. Aby uzyskać dodatkowe informacje i przykłady, zobacz [ten blog](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>Rozpoznawania plików wejściowych
Pliki wideo. Obecnie obsługiwane są następujące formaty: MP4, MOV i WMV.

## <a name="task-configuration"></a>Konfiguracja zadania
Konfiguracja zadania (ustawienia domyślne). Podczas tworzenia zadania z **Azure Media Rozpoznawania**, należy określić ustawienia wstępnego za pomocą formatu JSON i XML konfiguracji. 

>[!NOTE]
>Aparat Rozpoznawania przyjmuje tylko obszar obrazu z minimalną pikseli 40 do maksymalnej pikseli 32000 jako prawidłową wartość wejściową w obu wysokość i szerokość.
>

### <a name="attribute-descriptions"></a>Opisy atrybutów
| Nazwa atrybutu | Opis |
| --- | --- |
|AdvancedOutput| Jeśli AdvancedOutput jest ustawiona na wartość true, dane wyjściowe JSON zawierają dane pozycyjnych dla każdego pojedynczego wyrazu (oprócz fraz i regiony). Jeśli nie chcesz wyświetlić szczegółowe informacje, należy ustawić flagę na wartość false. Wartość domyślna to false. Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/).|
| Język |(opcjonalnie) opisano język tekstu do wyszukania. Jedną z następujących: AutoDetect (ustawienie domyślne), arabskiego, ChineseSimplified, ChineseTraditional, czeski, duński, holenderski, angielski, fiński, francuski, niemiecki, grecki, węgierski, włoski, japoński, koreański, norweski, Polski, portugalski, rumuński, rosyjski, SerbianCyrillic, SerbianLatin, słowacki, hiszpański, szwedzki, turecki. |
| TextOrientation |(opcjonalnie) opisano orientacji tekstu do wyszukania.  "Lewo" oznacza, że na początku wszystkie litery są skierowane po lewej stronie.  Domyślny tekst (na przykład tych, które można znaleźć w księdze) można wywołać "W górę" orientacji.  Jedną z następujących: AutoDetect (ustawienie domyślne), maksymalnie, prawo, w dół, lewo. |
| TimeInterval |(opcjonalnie) opisano próbkowania.  Domyślna to co 1/2 sekundy.<br/>Format JSON — hh: mm:. SSS (00:00:00.500 domyślne)<br/>Format XML — podstawowy czas trwania W3C XSD (domyślnie PT0.5) |
| DetectRegions |(opcjonalnie) Tablica obiektów DetectRegion określenie obszarów w ramce wideo, w którym można wykryć tekstu.<br/>Obiekt DetectRegion składa się z następujących wartości całkowitych cztery:<br/>Po lewej — pikseli z lewym marginesie<br/>TOP — pikseli z margines górny<br/>Szerokość — Szerokość regionu w pikselach<br/>Wysokość — wysokość regionu w pikselach |

#### <a name="json-preset-example"></a>Przykład predefiniowanych JSON

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

#### <a name="xml-preset-example"></a>Przykład wstępnie zdefiniowane w pliku XML

```xml
    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""http://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""http://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""http://www.windowsazure.com/media/encoding/Preset/2014/03"">
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

## <a name="ocr-output-files"></a>Pliki wyjściowe Rozpoznawania
Dane wyjściowe procesor multimediów Rozpoznawania jest to plik JSON.

### <a name="elements-of-the-output-json-file"></a>Elementy pliku wyjściowego w formacie JSON
Wyjście wideo Rozpoznawania udostępnia dane segmentem czas na znaki w pliku wideo.  Atrybuty, takie jak języka lub orientacji służy do rozmów telefonicznych w dokładnie słowa wybrane analizowanie. 

Dane wyjściowe zawierają następujące atrybuty:

| Element | Opis |
| --- | --- |
| skali czasu |parametrem "ticks" na sekundę wideo |
| Przesunięcie |Czas przesunięcia znaczników czasu. W wersji 1.0 interfejsów API, wideo ta będzie zawsze równa 0. |
| szybkość klatek |Klatek na sekundę wideo |
| Szerokość |Szerokość wideo w pikselach |
| Wysokość |wysokość w pikselach |
| fragmenty |Tablica oparte na czasie fragmentów, do którego jest fragmentaryczne metadane wideo |
| rozpoczynanie |uruchomienie fragmentu w parametrem "ticks" |
| Czas trwania |Długość fragmentu w parametrem "ticks" |
| interval |Interwał każdego zdarzenia w ciągu danego fragmentu |
| zdarzenia |Tablica zawierająca regionów |
| region |Obiekt reprezentujący wykryto słów ani fraz |
| Język |język tekstu wykryte w obrębie regionu |
| Orientacja |orientację tekstu w regionie |
| wiersze |Tablica wierszy tekstu w regionie |
| Tekst |tekst |

### <a name="json-output-example"></a>Przykład danych wyjściowych JSON
W poniższym przykładzie danych wyjściowych zawiera informacje ogólne wideo oraz kilka fragmentów wideo. W każdym wideo fragmentu zawiera każdego regionu, który jest wykrywany przez pakiet administracyjny Rozpoznawania język oraz orientacji tekstu. Region zawiera także każdy wiersz programu word w tym regionie tekst wiersza, pozycja wiersza i informacje co word (zawartości programu word, pozycji i zaufania) w tym wierszu. Poniżej przedstawiono przykład i umieścić niektóre wbudowane komentarze.

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

## <a name="net-sample-code"></a>.NET przykładowy kod

Następujących programów przedstawiono sposób:

1. Utworzenie elementu zawartości i przesyłanie pliku multimediów do elementu zawartości.
2. Utwórz zadanie przy użyciu pliku konfiguracji/ustawienie wstępne Rozpoznawania.
3. Pobierz pliki danych wyjściowych w formacie JSON. 
   
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
[Przegląd analiz usługi Azure Media Services](media-services-analytics-overview.md)

