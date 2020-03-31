---
title: Wykrywanie twarzy i emocji za pomocą usługi Azure Media Analytics | Dokumenty firmy Microsoft
description: W tym temacie pokazano, jak wykryć twarze i emocje za pomocą usługi Azure Media Analytics.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 5ca4692c-23f1-451d-9d82-cbc8bf0fd707
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: 2d746167f993438e5fce467365844df2078c08a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919315"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Wykrywanie twarzy i emocji za pomocą usługi Azure Media Analytics

> [!NOTE]
> Procesor nośników **Azure Media Face Detector** zostanie wycofany. W przypadku daty wycofania zobacz temat [starszych składników.](legacy-components.md)

## <a name="overview"></a>Omówienie

Procesor **multimediów Azure Media Face Detector** (MP) umożliwia liczenie, śledzenie ruchów, a nawet mierzenie udziału odbiorców i reakcji za pomocą mimiki twarzy. Ta usługa zawiera dwie funkcje: 

* **Wykrywanie twarzy**
  
    Wykrywanie twarzy znajduje i śledzi ludzkie twarze w filmie. Wiele twarzy można wykryć, a następnie być śledzone podczas ich poruszania się, z metadanych czasu i lokalizacji zwracanych w pliku JSON. Podczas śledzenia próbuje nadać spójny identyfikator tej samej twarzy, gdy osoba porusza się po ekranie, nawet jeśli jest zatkana lub na krótko opuszcza ramkę.
  
  > [!NOTE]
  > Ta usługa nie wykonuje rozpoznawania twarzy. Osoba, która opuści ramkę lub zostanie zasłana zbyt długo, otrzyma nowy identyfikator po powrocie.
  > 
  > 
* **Wykrywanie emocji**
  
    Wykrywanie emocji jest opcjonalnym składnikiem procesora mediów wykrywania twarzy, który zwraca analizę wielu atrybutów emocjonalnych z wykrytych twarzy, w tym szczęścia, smutku, strachu, gniewu i innych. 

**Usługa Azure Media Face Detector** MP jest obecnie w wersji zapoznawczej.

W tym artykule przedstawiono szczegółowe informacje na temat **usługi Azure Media Face Detector** i pokazano, jak go używać z zestawem SDK usługi Media Services dla platformy .NET.

## <a name="face-detector-input-files"></a>Pliki wejściowe detektora twarzy
Pliki wideo. Obecnie obsługiwane są następujące formaty: MP4, MOV i WMV.

## <a name="face-detector-output-files"></a>Pliki wyjściowe detektora twarzy
Interfejs API wykrywania i śledzenia twarzy zapewnia precyzyjne wykrywanie i śledzenie lokalizacji twarzy, które może wykryć do 64 ludzkich twarzy w filmie. Ściany czołowe zapewniają najlepsze wyniki, podczas gdy ściany boczne i małe ściany (mniejsze lub równe 24x24 pikselom) mogą nie być tak dokładne.

Wykryte i śledzone ściany są zwracane ze współrzędnymi (po lewej, u góry, szerokością i wysokością) wskazującymi położenie ścian na obrazie w pikselach, a także numerem identyfikatora ściany wskazującym śledzenie tej osoby. Numery identyfikatorów twarzy są podatne na resetowanie w warunkach utraty lub nakładania się czołowej ściany w ramce, co powoduje, że niektóre osoby otrzymują przypisane wiele identyfikatorów.

## <a name="elements-of-the-output-json-file"></a><a id="output_elements"></a>Elementy wyjściowego pliku JSON

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

Face Detector używa technik fragmentacji (gdzie metadane mogą być podzielone na fragmenty oparte na czasie i można pobrać tylko to, czego potrzebujesz) i segmentacji (gdzie zdarzenia są podzielone w przypadku, gdy się zbyt duże). W transformacji danych może pomóc kilka prostych obliczeń. Na przykład, jeśli zdarzenie rozpoczęło się od 6300 (znaczniki), z harmonogramem 2997 (znaczniki/s) i 29,97 (klatki/s), wówczas:

* Pocżątek/skala czasu= 2,1 s
* Sekundy x Liczba klatek na sekundę = 63 klatki

## <a name="face-detection-input-and-output-example"></a>Przykład wprowadzania i wyjścia wykrywania twarzy
### <a name="input-video"></a>Wprowadzanie wideo
[Wejście wideo](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Konfiguracja zadania (ustawienie wstępne)
Podczas tworzenia zadania za pomocą **narzędzia Azure Media Face Detector**należy określić predefiniowane ustawienia konfiguracji. Następujące ustawienie konfiguracji jest przeznaczone tylko do wykrywania twarzy.

```json
    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }
```

#### <a name="attribute-descriptions"></a>Opisy atrybutów
| Nazwa atrybutu | Opis |
| --- | --- |
| Tryb |Szybki - szybka szybkość przetwarzania, ale mniej dokładna (domyślnie).|

### <a name="json-output"></a>Wyjście JSON
Poniższy przykład danych wyjściowych JSON został obcięty.

```json
    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],
```


## <a name="emotion-detection-input-and-output-example"></a>Przykład wprowadzania i wyjścia wykrywania emocji
### <a name="input-video"></a>Wprowadzanie wideo
[Wejście wideo](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Konfiguracja zadania (ustawienie wstępne)
Podczas tworzenia zadania za pomocą **narzędzia Azure Media Face Detector**należy określić predefiniowane ustawienia konfiguracji. Następujące ustawienie konfiguracji określa utworzenie JSON na podstawie wykrywania emocji.

```json
    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }
```


#### <a name="attribute-descriptions"></a>Opisy atrybutów
| Nazwa atrybutu | Opis |
| --- | --- |
| Tryb |Twarze: Tylko wykrywanie twarzy.<br/>PerFaceEmotion: Zwracaj emocje niezależnie dla każdego wykrywania twarzy.<br/>AgregacjaEmocja: Zwraca średnie wartości emocji dla wszystkich ścian w ramce. |
| AggregateEmotionWindowMs |Użyj, jeśli wybrano tryb AggregateEmotion. Określa długość wideo używanego do tworzenia każdego wyniku agregacji w milisekundach. |
| AggregateMotionIntervalMs |Użyj, jeśli wybrano tryb AggregateEmotion. Określa, z jaką częstotliwością, aby uzyskać wyniki agregacji. |

#### <a name="aggregate-defaults"></a>Zagregowane wartości domyślne
Poniżej znajdują się zalecane wartości dla ustawień okna i interwału agregacji. AggregateEmotionWindowMs powinny być dłuższe niż AggregateEmotionIntervalMs.

|| Wartości domyślne | Maks. | Min., min. |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0,5 |2 |0,25|
| AggregateMotionIntervalMs |0,5 |1 |0,25|

### <a name="json-output"></a>Wyjście JSON
Dane wyjściowe JSON dla zagregowanych emocji (obcięty):

```json
    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
```

## <a name="limitations"></a>Ograniczenia
* Obsługiwane formaty wideo wejściowe obejmują MP4, MOV i WMV.
* Wykrywalny zakres rozmiaru twarzy wynosi od 24x24 do 2048x2048 pikseli. Twarze poza tym zakresem nie zostaną wykryte.
* Dla każdego filmu maksymalna liczba zwróconych twarzy wynosi 64.
* Niektóre twarze mogą nie zostać wykryte z powodu wyzwań technicznych; na przykład bardzo duże kąty twarzy (pozy głowy) i duże okluzja. Twarze czołowe i zbliżone do frontu mają najlepsze wyniki.

## <a name="net-sample-code"></a>Przykładowy kod platformy .NET

W poniższym programie pokazano, jak:

1. Utwórz zasób i przekaż plik multimedialny do zasobu.
2. Utwórz zadanie z zadaniem wykrywania twarzy na podstawie pliku konfiguracyjnego zawierającego następujące ustawienia json: 

    ```json
            {
                "version": "1.0"
            }
    ```
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

namespace FaceDetection
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

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Run the FaceDetection job.
            var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\FaceDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
        }

        static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Face Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Detection Job");

            // Get a reference to Azure Media Face Detector.
            string MediaProcessorName = "Azure Media Face Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Detection Output Asset", AssetCreationOptions.None);

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

[Pokazy usługi Azure Media Analytics](https://amslabs.azurewebsites.net/demos/Analytics.html)

