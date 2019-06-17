---
title: Wykrywanie twarzy i emocji za pomocą usługi Azure Media Analytics | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak wykrywanie twarzy i emocji z usługą Azure Media Analytics.
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
ms.author: milanga;juliako;
ms.openlocfilehash: 46e60583da79006c133c8d9fac63e27f28bd699f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61217268"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Wykrywanie twarzy i emocji za pomocą usługi Azure Media Analytics
## <a name="overview"></a>Omówienie
**Wykrywanie twarzy multimediów Azure** procesor multimediów (MP) pozwala na count, śledzenie przepływu i nawet miernika uczestnictwa odbiorców i reagowanie na nie przy użyciu twarzy. Ta usługa zawiera dwie funkcje: 

* **Wykrywanie twarzy**
  
    Wykrywanie twarzy umożliwia znalezienie i śledzenie ludzkich twarzy w filmie wideo. Wiele powierzchni może zostać wykryte i następnie być śledzone przechodzące wokół, za pomocą metadanych czasu i bieżącej lokalizacji, zwracane w pliku JSON. Podczas śledzenia, podejmowana jest próba oferowanie spójny identyfikator tego samego twarzy podczas, gdy osoba jest poruszanie się w na ekranie, nawet jeśli są zablokowane lub krótko pozostaw ramki.
  
  > [!NOTE]
  > Ta usługa nie wykonuje rozpoznawanie twarzy. Osoba, która pozostawia ramki lub staje się blokować dla zbyt długo otrzyma nowy identyfikator przypadku zwracają.
  > 
  > 
* **Wykrywanie emocji**
  
    Wykrywanie emocji jest opcjonalnym składnikiem procesor multimediów wykrywanie twarzy, które zwraca analizy na wiele atrybutów emocjonalnej twarzy wykryć, w tym szczęście, smutek, strach i gniew. 

**Wykrywanie twarzy multimediów Azure** pakiet administracyjny jest obecnie w wersji zapoznawczej.

Ten artykuł zawiera szczegółowe informacje o **wykrywanie twarzy multimediów Azure** i pokazuje, jak z niej korzystać z zestawu SDK usługi Media Services dla platformy .NET.

## <a name="face-detector-input-files"></a>Pliki wejściowe wykrywanie twarzy
Pliki wideo. Obecnie obsługiwane są następujące formaty: MP4, MOV i WMV.

## <a name="face-detector-output-files"></a>Pliki wyjściowe wykrywanie twarzy
Interfejs API śledzenia i wykrywania twarzy zawiera śledzenia, który można wykrywanie do 64 ludzkich twarzy w filmach wideo i wykrywania położenia twarzy dużej dokładności. Czołowa twarzy zapewniają najlepsze wyniki, podczas boczne i małym twarzy (mniejsze niż lub równe 24 x 24 piksele) może nie być dokładny.

Wykryte i śledzone twarzy są zwracane z współrzędnych (po lewej stronie, top, szerokość i wysokość) wskazująca lokalizację twarzy na obrazie w pikselach, a także numer identyfikacyjny rozpoznawania twarzy, wskazując śledzenie tej osoby. Numery identyfikatorów twarzy są podatne na zresetować w sytuacjach, gdy czołowego twarzy zostanie utracony lub nakładających się w ramce, co w niektórych osób wprowadzenie przypisanych wiele identyfikatorów.

## <a id="output_elements"></a>Elementy danych wyjściowych pliku JSON

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

Wykrywanie twarzy używane są także techniki fragmentacji (gdzie metadane mogą być dzielone we fragmentach oparte na czasie i mogą pobrać tylko potrzebnych składników) i segmentacji (której zdarzenia są dzielone w przypadku, gdy staną się zbyt duże). W transformacji danych może pomóc kilka prostych obliczeń. Na przykład, jeśli zdarzenie rozpoczęło się o 6300 (Takty) przy użyciu skali czasu 2997 (Takty/s) i framerate następnie 29,97 (ramek/s):

* Pocżątek/skala czasu= 2,1 s
* X Framerate (w sekundach) = 63 ramki

## <a name="face-detection-input-and-output-example"></a>Dane wejściowe wykrywanie twarzy i przykład danych wyjściowych
### <a name="input-video"></a>Wejściowy plik wideo
[Wejściowy plik wideo](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Konfiguracja zadania (ustawienie wstępne)
Podczas tworzenia zadania za pomocą **wykrywanie twarzy multimediów Azure**, należy określić ustawienie wstępne konfiguracji. Następujące ustawienie konfiguracji jest po prostu wykrywanie twarzy.

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
| Tryb |Bardzo szybko — szybkie przetwarzanie szybkość, ale mniej dokładne (ustawienie domyślne).|

### <a name="json-output"></a>Dane wyjściowe JSON
Poniższy przykład dane wyjściowe JSON zostały obcięte.

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


## <a name="emotion-detection-input-and-output-example"></a>Wykrywanie emocji na danych wejściowych i wyjściowych przykładu
### <a name="input-video"></a>Wejściowy plik wideo
[Wejściowy plik wideo](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Konfiguracja zadania (ustawienie wstępne)
Podczas tworzenia zadania za pomocą **wykrywanie twarzy multimediów Azure**, należy określić ustawienie wstępne konfiguracji. Określa następujące ustawienie konfiguracji do tworzenia opartych na wykrywanie emocji na notacji JSON.

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
| Tryb |Twarze: Tylko stoją w obliczu wykrywania.<br/>PerFaceEmotion: Zwróć emocji osobno dla każdego wykrywania twarzy.<br/>AggregateEmotion: Średnia emocji wartości zwracane dla wszystkich powierzchni w ramce. |
| AggregateEmotionWindowMs |Użyj, jeśli wybrany tryb AggregateEmotion. Określa długość wideo użyta do wyprodukowania każdej agregacji wyników, w milisekundach. |
| AggregateEmotionIntervalMs |Użyj, jeśli wybrany tryb AggregateEmotion. Określa częstotliwość w celu uzyskania wyników agregacji. |

#### <a name="aggregate-defaults"></a>Ustawienia domyślne agregacji
Poniżej są zalecane wartości ustawień okna i interwał agregacji. AggregateEmotionWindowMs powinien być dłuższy niż AggregateEmotionIntervalMs.

|| Wartości domyślne (s) | MAX(s) | Min(s) |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0,5 |2 |0.25|
| AggregateEmotionIntervalMs |0,5 |1 |0.25|

### <a name="json-output"></a>Dane wyjściowe JSON
JSON, dane wyjściowe dla agregacji rozpoznawania emocji (obcięty):

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
* Wideo obsługiwanych formatów danych wejściowych obejmują MP4, MOV i WMV.
* Zakres rozmiaru wykrywalny twarzy to 24 x 24 do 2048 x 2048 pikseli. Twarze poza tym zakresem nie zostanie wykryty.
* Dla każdego pliku wideo maksymalną liczbę twarzy, zwracany jest 64.
* Niektóre twarzy mogą nie zostać wykryte, ze względu na problemy techniczne; na przykład, bardzo dużych powierzchni kąty (head ułożenia) i duże zamknięcia. Czołowa i niemal czołowego twarzy ma najlepsze rezultaty.

## <a name="net-sample-code"></a>Przykładowy kod .NET

Poniższy program pokazuje jak:

1. Utworzenie elementu zawartości i przekaż plik multimedialny do niego.
2. Tworzenie zadania za pomocą zadania wykrywania twarzy w zależności od pliku konfiguracji, który zawiera następujące ustawienie wstępne json: 

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
[Przegląd Analityki usługi Azure Media Services](media-services-analytics-overview.md)

[Pokazy usługi Azure Media Analytics](https://amslabs.azurewebsites.net/demos/Analytics.html)

