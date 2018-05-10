---
title: Wykrywanie twarzy na obrazie i emocji z analizy multimediów Azure | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono sposób wykrywania kroje i emocji z analizy multimediów Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 5ca4692c-23f1-451d-9d82-cbc8bf0fd707
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: milanga;juliako;
ms.openlocfilehash: 859e75819f96edd527fceb143faf8357738ce80e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Wykrywanie twarzy na obrazie i emocji z analizy multimediów Azure
## <a name="overview"></a>Przegląd
**Azure Media krój detektora** procesor multimediów (MP) umożliwia count, śledzić przeniesień i nawet określić udział odbiorców i reakcji za pośrednictwem twarzy. Ta usługa zawiera dwie funkcje: 

* **wykrywanie twarzy na obrazie**
  
    Wykrywanie twarzy na obrazie znajduje i śledzi człowieka kroje w pliku wideo. Wiele powierzchni mogą być wykrywane i następnie śledzenia przechodzą wokół, czas i lokalizację metadanymi zwrócił w pliku JSON. Podczas śledzenia podejmie ma zostać przypisany do tej samej kroju spójny identyfikator, gdy osoba jest przenoszenia na ekranie, nawet jeśli są zablokowane lub pozostaw krótko ramki.
  
  > [!NOTE]
  > Ta usługa nie przeprowadza rozpoznawanie twarzy. Osoba, która pozostawia ramki lub staje się blokować dla zbyt długo będzie mógł skorzystać z nowym Identyfikatorem gdy zwracają.
  > 
  > 
* **Wykrywanie emocji**
  
    Wykrywanie emocji jest opcjonalnym składnikiem procesor multimediów wykrywania twarzy na obrazie, które zwraca analizy na wiele atrybutów emocjonalne kroje wykryte, w tym szczęście, sadness, obawy i gniew. 

**Azure Media krój detektora** pakiet administracyjny jest obecnie w przeglądzie.

Ten artykuł zawiera szczegółowe informacje o **Azure Media krój detektora** i pokazuje, jak z niego korzystać z zestawu SDK usługi Media Services dla platformy .NET.

## <a name="face-detector-input-files"></a>Stają przed detektora plików wejściowych
Pliki wideo. Obecnie obsługiwane są następujące formaty: MP4, MOV i WMV.

## <a name="face-detector-output-files"></a>Stają przed detektora pliki wyjściowe
Interfejsu API śledzenia i wykrywania twarzy na obrazie zapewnia wysokiej precyzji krój lokalizacji wykrywania i śledzenia wykrywające maksymalnie 64 kroje człowieka wideo. Kroje czołowego Podaj najlepsze wyniki, podczas boczne i w małych kroje (mniejsze niż lub równe 24 x 24 piksele) może nie być tak dokładne.

Kroje wykryte i śledzonych są zwracane z współrzędne (po lewej, top, szerokość i wysokość) wskazujący lokalizację kroje obrazu w pikselach, a także numer identyfikacyjny krój, wskazując śledzenia tej osoby. Numery identyfikatorów krój są podatne na zresetować w sytuacji, gdy czołowego kroju zostanie utracony lub pokrywający się w ramce spowodować, że niektóre osoby pobierania przypisanych wiele identyfikatorów.

## <a id="output_elements"></a>Elementy pliku wyjściowego w formacie JSON

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

Wykrywanie twarzy na obrazie używa technik fragmentacji (gdzie metadanych można podzielić w oparte na czasie fragmentów i można pobrać tylko potrzebnych) i segmentacji (gdzie zdarzenia są podzielony na wypadek, gdyby otrzymują zbyt duży). Niektórych prostych obliczeń może pomóc przekształcania danych. Na przykład, jeśli zdarzenie rozpoczęty o godzinie 6300 (znaczniki) z skali czasu 2997 (Takty/s) i szybkość klatek z 29,97 (ramek na sekundę), następnie:

* Start/skali czasu = sekund 2.1
* Sekundy x Framerate = 63 ramki

## <a name="face-detection-input-and-output-example"></a>Dostęp do danych wejściowych wykrywania i przykład danych wyjściowych
### <a name="input-video"></a>Wejściowy plik wideo
[Wejściowy plik wideo](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Konfiguracja zadania (ustawienia domyślne)
Podczas tworzenia zadania z **Azure Media krój detektora**, należy określić ustawienia domyślne konfiguracji. Następujące ustawienie konfiguracji jest tylko do wykrywania twarzy na obrazie.

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
| Tryb |Fast — szybkiego przetwarzania szybkości, ale mniej dokładne (ustawienie domyślne).|

### <a name="json-output"></a>Dane wyjściowe JSON
Poniższy przykład danych wyjściowych JSON została obcięta.

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


## <a name="emotion-detection-input-and-output-example"></a>Wykrywanie emocji wejściowa i wyjściowa przykład
### <a name="input-video"></a>Wejściowy plik wideo
[Wejściowy plik wideo](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Konfiguracja zadania (ustawienia domyślne)
Podczas tworzenia zadania z **Azure Media krój detektora**, należy określić ustawienia domyślne konfiguracji. Określa następujące ustawienie konfiguracji można utworzyć oparty na wykrywaniu emocji JSON.

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
| Tryb |Kroje: Tylko stają przed wykrywania.<br/>PerFaceEmotion: Zwraca emocji osobno dla każdej wykrywania twarzy na obrazie.<br/>AggregateEmotion: Wartości zwracane emocji średni wszystkie powierzchnie w ramce. |
| AggregateEmotionWindowMs |Użyj, jeśli wybrany tryb AggregateEmotion. Określa długość wideo użyta do wyprodukowania każdy łączny wynik, w milisekundach. |
| AggregateEmotionIntervalMs |Użyj, jeśli wybrany tryb AggregateEmotion. Określa częstotliwość dostarczyło wyników agregacji. |

#### <a name="aggregate-defaults"></a>Łączny wartości domyślnych
Poniżej są zalecane wartości ustawień okna i interwał agregacji. AggregateEmotionWindowMs powinien być dłuższy niż AggregateEmotionIntervalMs.

|| Ustawienia domyślne (s) | MAX(s) | Min(s) |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0.5 |2 |0.25|
| AggregateEmotionIntervalMs |0.5 |1 |0.25|

### <a name="json-output"></a>Dane wyjściowe JSON
Dane wyjściowe dla agregacji emocji (obcięty) JSON:

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
* Obsługiwane formaty wideo wejściowych obejmują MP4, MOV i WMV.
* Zakres rozmiaru wykrywalny krój jest 24 x 24 do 2048 x 2048 pikseli. Nie są wykrywane kroje poza tym zakresem.
* Dla każdego wideo maksymalna liczba kroje zwrócił wynosi 64.
* Niektóre kroje mogą nie być wykrywane z powodu problemów technicznych; na przykład, kąty przedniej bardzo duże (head ułożenia) i duże zamknięcia. Kroje czołowego i niemal czołowego mieć najlepsze wyniki.

## <a name="net-sample-code"></a>.NET przykładowy kod

Następujących programów przedstawiono sposób:

1. Utworzenie elementu zawartości i przesyłanie pliku multimediów do elementu zawartości.
2. Utwórz zadanie z zadania wykrywania twarzy na obrazie w zależności od pliku konfiguracji, który zawiera następujące ustawienie json: 

    ```json
            {
                "version": "1.0"
            }
    ```
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
            task.OutputAssets.AddNew("My Face Detectoion Output Asset", AssetCreationOptions.None);

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

[W trakcie analizy multimediów Azure](http://amslabs.azurewebsites.net/demos/Analytics.html)

