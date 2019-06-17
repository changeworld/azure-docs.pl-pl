---
title: Digitize tekstu za pomocą usługi Azure Media Analytics optyczne rozpoznawanie znaków | Dokumentacja firmy Microsoft
description: Azure Media Analytics OCR (optyczne rozpoznawanie znaków) umożliwia konwertowanie zawartości tekstowej w plikach wideo do edycji, wyszukiwanie tekstu cyfrowych.  Dzięki temu można zautomatyzować ekstrakcji istotne metadane z multimediów sygnału wideo.
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
ms.openlocfilehash: 91fad34073d7505c596bedfb6c93946ee7393dd7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60825612"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Użyj analizy multimediów Azure, aby przekonwertować na tekst cyfrowych, zawartości tekstowej w plikach wideo  
## <a name="overview"></a>Omówienie
Jeśli zachodzi potrzeba wyodrębnić zawartość tekstu z plików wideo i wygenerować go edytować, którą można przeszukiwać tekst cyfrowych, należy użyć usługi Azure Media Analytics OCR (optyczne rozpoznawanie znaków). To procesor multimediów Azure wykrywa zawartość tekstową plików wideo i generuje pliki tekstowe do użycia. Optyczne rozpoznawanie znaków umożliwia automatyzację ekstrakcji istotne metadane z multimediów sygnału wideo.

W przypadku użycia w połączeniu z wyszukiwarką, możesz łatwo indeksowanie multimediów według tekstu i Zwiększ możliwości odnajdywania Twojej zawartości. Jest to bardzo przydatne w wysoce tekstową wideo, takich jak nagranie wideo lub Przechwytywanie ekranu prezentacji pokaz slajdów. Procesor multimediów Azure optyczne rozpoznawanie znaków jest zoptymalizowany pod kątem cyfrowego tekstu.

**OCR multimediów Azure** procesor multimediów jest obecnie w wersji zapoznawczej.

Ten artykuł zawiera szczegółowe informacje o **OCR multimediów Azure** i pokazuje, jak z niej korzystać z zestawu SDK usługi Media Services dla platformy .NET. Aby uzyskać więcej informacji i przykładów, zobacz [ten blog](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>Optyczne rozpoznawanie znaków plików wejściowych
Pliki wideo. Obecnie obsługiwane są następujące formaty: MP4, MOV i WMV.

## <a name="task-configuration"></a>Konfiguracja zadania
Konfiguracja zadania (ustawienie wstępne). Podczas tworzenia zadania za pomocą **OCR multimediów Azure**, należy określić konfigurację ustawienia wstępnego za pomocą JSON lub XML. 

>[!NOTE]
>Aparatu OCR trwa tylko obszar obrazu z minimalną pikseli 40 maksymalna pikseli 32000 jako prawidłowe dane wejściowe w obu Wysokość/szerokość.
>

### <a name="attribute-descriptions"></a>Opisy atrybutów
| Nazwa atrybutu | Opis |
| --- | --- |
|AdvancedOutput| Jeśli AdvancedOutput jest ustawiona na wartość true, dane wyjściowe JSON będzie zawierać dane pozycyjne dla każdego pojedynczego słowa (oprócz fraz i regiony). Jeśli chcesz wyświetlić te informacje, należy ustawić flagę na wartość false. Wartość domyślna to false. Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/).|
| Język |(opcjonalnie) opis język tekstu do wyszukania. Jeden z następujących czynności: Autowykrywanie (ustawienie domyślne), arabskiego, ChineseSimplified, ChineseTraditional, czeski, duński, holenderski, angielski, fiński, francuski, niemiecki, grecki, węgierski, włoski, japoński, koreański, norweski, Polski, portugalski, rumuński, rosyjski, SerbianCyrillic, SerbianLatin , Słowacki, hiszpański, szwedzki, turecki. |
| TextOrientation |(opcjonalnie) opis orientacji tekstu do wyszukania.  "Left" oznacza, że w górnej części wszystkich liter skierowane po lewej stronie.  Domyślny tekst (na przykład tych, które można znaleźć w książce) może być wywoływana "Up", aby poznać podstawy.  Jeden z następujących czynności: Autowykrywanie (ustawienie domyślne), maksymalnie, prawo, dół, lewo. |
| TimeInterval |(opcjonalnie) opis częstotliwość próbkowania.  Domyślnie jest co sekundę 1/2.<br/>Format JSON —: mm: ss. SSS (00:00:00.500 domyślny)<br/>Format XML — pierwotny czas trwania W3C XSD (ustawienie domyślne PT0.5) |
| DetectRegions |(opcjonalnie) Tablica obiektów DetectRegion, określając regionach w obrębie klatki wideo, w której ma zostać wykrywaj tekst.<br/>Obiekt DetectRegion składa się z następujących wartości cztery liczby całkowitej:<br/>Po lewej stronie — pikseli od lewy margines<br/>TOP — pikseli od górnej marginesu<br/>Width — szerokość obszaru w pikselach<br/>Wysokość — wysokość regionu, w pikselach |

#### <a name="json-preset-example"></a>Przykład kodu JSON z wstępnie zdefiniowane

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

## <a name="ocr-output-files"></a>Pliki wyjściowe optyczne rozpoznawanie znaków
Dane wyjściowe procesora media optyczne rozpoznawanie znaków jest plik w formacie JSON.

### <a name="elements-of-the-output-json-file"></a>Elementy danych wyjściowych pliku JSON
Dane wyjściowe optyczne rozpoznawanie znaków w wideo zawiera czas segmentów danych znaków w wideo.  Atrybuty, takie jak język lub orientacja służy hone w dokładnie słowa, które zostały wybrane w analizie. 

Dane wyjściowe zawierają następujące atrybuty:

| Element | Opis |
| --- | --- |
| Skala czasu |"impulsów", na sekundę filmu wideo |
| Offset |Czas przesunięcia sygnatur czasowych. W wersji 1.0 interfejsów API Video ta będzie zawsze równa 0. |
| Framerate |Liczba klatek na sekundę filmu wideo |
| Szerokość |szerokość obrazu wideo w pikselach |
| Wysokość |wysokość w pikselach |
| Fragments |Tablica oparte na czasie fragmenty filmu wideo, do którego jest podzielony metadanych |
| start |uruchomienie fragmentu w "taktach" |
| Czas trwania |Długość fragmentu w "taktach" |
| interval |Interwał każde zdarzenie w ramach danego fragmentu |
| zdarzenia |Tablica zawierająca regionów |
| region |Obiekt reprezentujący wykryto słów i fraz |
| language |język tekstu wykryte w obrębie regionu |
| Orientacja |Orientacja tekstu wykryte w obrębie regionu |
| wiersze |Tablica wierszy tekstu wykryte w obrębie regionu |
| tekst |aktualny tekst |

### <a name="json-output-example"></a>Przykład danych wyjściowych JSON
W poniższym przykładzie danych wyjściowych zawiera ogólne informacje wideo oraz kilka fragmentów wideo. W każdym fragmencie wideo zawiera on każdy region, który zostanie wykryty przez pakiet administracyjny optyczne rozpoznawanie znaków za pomocą języka i jego orientacja tekstu. Region zawiera również wszystkich wierszy programu word, w tym regionie przy użyciu wiersza tekstu, położenie wiersza i co informacje programu word (zawartości programu word, położenie i zaufania) w tym wierszu. Oto przykład i umieścić niektóre wbudowane komentarzy.

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

## <a name="net-sample-code"></a>Przykładowy kod .NET

Poniższy program pokazuje jak:

1. Utworzenie elementu zawartości i przekaż plik multimedialny do niego.
2. Utwórz zadanie przy użyciu pliku konfiguracji/ustawienie wstępne optyczne rozpoznawanie znaków.
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
[Przegląd Analityki usługi Azure Media Services](media-services-analytics-overview.md)

