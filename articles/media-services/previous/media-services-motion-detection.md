---
title: Wykrywanie ruchów za pomocą usługi Azure Media Analytics | Dokumentacja firmy Microsoft
description: Wykrywanie ruchu multimediów Azure procesor multimediów (MP) umożliwia wydajne identyfikują sekcje zainteresowania w wideo w przeciwnym razie długich i procesu.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/19/2019
ms.author: milanga;juliako;
ms.openlocfilehash: e0b083cba575f4d1c0eb19afb76fca29431ae75e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463535"
---
# <a name="detect-motions-with-azure-media-analytics"></a>Wykrywanie ruchów za pomocą usługi Azure Media Analytics
## <a name="overview"></a>Omówienie
**Wykrywanie ruchu multimediów Azure** procesor multimediów (MP) pozwala na efektywne identyfikują sekcje zainteresowania w wideo w przeciwnym razie długich i procesu. Wykrywanie ruchu można na statyczne aparatu nagrań z monitorowania identyfikują sekcje wideo, w której występuje ruchu. Generuje plik JSON zawierający metadane za pomocą sygnatur czasowych i otaczający region, w której wystąpiło zdarzenie.

Skierowany strumieniowych źródeł wideo zabezpieczeń, ta technologia jest w stanie kategoryzowanie ruchu do odpowiednie zdarzenia i fałszywych alarmów, takie jak cieni i zmian oświetlenia. Umożliwia generowanie alertów zabezpieczeń z kanałami informacyjnymi aparatu bez otrzymywania wiadomości-śmieci nieskończone zdarzeń nie ma znaczenia, będąc wyodrębnić chwil zainteresowania z nadzoru długich wideo.

**Wykrywanie ruchu multimediów Azure** pakiet administracyjny jest obecnie w wersji zapoznawczej.

Ten artykuł zawiera szczegółowe informacje o **wykrywanie ruchu multimediów Azure** i pokazuje, jak z niej korzystać z zestawu SDK usługi Media Services dla platformy .NET

## <a name="motion-detector-input-files"></a>Pliki wejściowe wykrywanie ruchu
Pliki wideo. Obecnie obsługiwane są następujące formaty: MP4, MOV i WMV.

## <a name="task-configuration-preset"></a>Konfiguracja zadania (ustawienie wstępne)
Podczas tworzenia zadania za pomocą **wykrywanie ruchu multimediów Azure**, należy określić ustawienie wstępne konfiguracji. 

### <a name="parameters"></a>Parametry
Można użyć następujących parametrów:

| Name (Nazwa) | Opcje | Opis | Domyślne |
| --- | --- | --- | --- |
| sensitivityLevel |Ciąg: "niska", "średnie", "wysoka" |Ustawia poziom poufności w ruchy, które są zgłaszane. Dostosuj, aby dostosować liczbę wyników fałszywie dodatnich. |"średnia" |
| frameSamplingValue |dodatnia liczba całkowita |Określa częstotliwość, na którym działa algorytmu. 1 jest równe w każdej klatce, 2 oznacza, że co drugi ramki i tak dalej. |1 |
| detectLightChange |Wartość logiczna: "prawda", "false" |Określa, czy zmiany światła są zgłaszane w wynikach |'False' |
| mergeTimeThreshold |Czas xs: : Mm: ss<br/>Przykład: 00:00:03 |Określa przedział czasu między zdarzeniami ruchu, gdzie znajdują się 2 zdarzenia można łączyć i raportowane jako 1. |00:00:00 |
| detectionZones |Tablica stref wykrywania:<br/>-Wykrywanie strefa jest tablica 3 lub więcej punktów<br/>— Punkt jest x i y Współrzędna z zakresu od 0 do 1. |W tym artykule opisano listę stref wielokątne wykrywania do użycia.<br/>Wyniki są zgłaszane wraz z stref jako identyfikator z pierwszy z nich jest 'id': 0 |Jedną strefę, która obejmuje całą ramkę. |

### <a name="json-example"></a>Przykład kodu JSON

```json
    {
      "version": "1.0",
      "options": {
        "sensitivityLevel": "medium",
        "frameSamplingValue": 1,
        "detectLightChange": "False",
        "mergeTimeThreshold":
        "00:00:02",
        "detectionZones": [
          [
            {"x": 0, "y": 0},
            {"x": 0.5, "y": 0},
            {"x": 0, "y": 1}
           ],
          [
            {"x": 0.3, "y": 0.3},
            {"x": 0.55, "y": 0.3},
            {"x": 0.8, "y": 0.3},
            {"x": 0.8, "y": 0.55},
            {"x": 0.8, "y": 0.8},
            {"x": 0.55, "y": 0.8},
            {"x": 0.3, "y": 0.8},
            {"x": 0.3, "y": 0.55}
          ]
        ]
      }
    }
```

## <a name="motion-detector-output-files"></a>Pliki wyjściowe wykrywanie ruchu
Zadanie wykrywania ruchu zwraca plik w formacie JSON w trwałego danych wyjściowych, który opisuje alerty ruchu oraz ich kategorii, w ramach filmu wideo. Plik zawiera informacje o czas i czas trwania ruchu wykryte w trakcie filmu wideo.

Interfejs API wykrywanie ruchu zawiera wskaźniki po obiektów w ruchu w tle środka wideo (na przykład, nadzór, wideo). Wykrywanie ruchu jest uczony w celu wyeliminowania fałszywych alarmów, takie jak oświetlenia i zmiany w tle. Bieżące ograniczenia algorytmów obejmują nocy przetwarzania wideo, obiekty półprzezroczyste i małych obiektów.

### <a id="output_elements"></a>Elementy danych wyjściowych pliku JSON
> [!NOTE]
> W najnowszej wersji formatu danych wyjściowych JSON został zmieniony i może stanowić istotną zmianę dla niektórych klientów.
> 
> 

W poniższej tabeli opisano elementy danych wyjściowych pliku JSON.

| Element | Opis |
| --- | --- |
| Version |Odnosi się do wersji interfejsu API wideo. Bieżąca wersja to 2. |
| Timescale |"Impulsów" na sekundę filmu wideo. |
| Offset |Przesunięcie czasu dla sygnatury czasowe w "taktów." W wersji 1.0 interfejsów API Video ta będzie zawsze równa 0. W przyszłości scenariusze, które firma Microsoft obsługuje, ta wartość może ulec zmianie. |
| Framerate |Liczba klatek na sekundę w wideo. |
| Width, Height |Odnosi się do szerokości i wysokości wideo w pikselach. |
| Start |Sygnatura czasowa rozpoczęcia w "taktach". |
| Duration |Długość zdarzenia w "znaczników". |
| Interval |Interwał każdego wpisu w zdarzeniu w "taktach". |
| Events |Każdy fragment zdarzeń zawiera ruchu wykryte w tym czas trwania. |
| Type |W bieżącej wersji jest to zawsze "2" dla ogólnego ruchu. Dzięki temu etykiety interfejsów API Video elastyczność do kategoryzowania ruchu w przyszłych wersji. |
| RegionID |Jak wyjaśniono powyżej, ta będzie zawsze równa 0 w tej wersji. Ta etykieta dostarcza interfejs API wideo elastyczność, aby znaleźć ruchu w różnych regionach w przyszłych wersjach. |
| Regions |Odnosi się do obszaru w wideo, gdzie interesujące Cię ruchu. <br/><br/>-"id" reprezentuje obszar region — w tej wersji jest tylko jedna, identyfikator: 0. <br/>-"type" reprezentuje kształt obszaru interesujące Cię do ruchu. Obecnie są obsługiwane "prostokąt" i "polygon".<br/> Jeśli określono "prostokąt" region ma wymiarów w X, Y, szerokość i wysokość. Współrzędne X i Y reprezentują współrzędnych XY po lewej stronie górnego regionu na znormalizowaną skali od 0.0 do 1.0. Szerokość i wysokość reprezentuje rozmiar obszaru na znormalizowaną skali od 0.0 do 1.0. W bieżącej wersji X, Y, szerokość i wysokość są zawsze ustalone na poziomie 0, 0 i 1, 1. <br/>Jeśli określono "polygon" region ma wymiarów w punktach. <br/> |
| Fragments |Metadane jest podzielony się w różnych segmentach fragmentów. Każdy fragment zawiera rozpoczęcie, czas trwania, wartość interwału i zdarzenia. Fragment ze zdarzeniami nie oznacza, że ruchu nie został wykryty podczas tej godziny rozpoczęcia i czas trwania. |
| Nawiasy kwadratowe] |Każdy nawiasu reprezentuje jeden interwał w zdarzeniu. Puste nawiasy kwadratowe dla tego interwału oznacza, że ruch nie zostało wykryte. |
| locations |Ten nowy wpis w obszarze zdarzenia zawiera listę lokalizacji, w którym wystąpił ruchu. Jest to bardziej szczegółowe niż stref wykrywania. |

Poniższy przykład kodu JSON przedstawiono dane wyjściowe:

```json
    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],
```

## <a name="limitations"></a>Ograniczenia
* Wideo obsługiwanych formatów danych wejściowych obejmują MP4, MOV i WMV.
* Wykrywanie ruchu jest zoptymalizowany pod kątem filmów wideo w tle stacjonarnych. Algorytm koncentruje się na skróceniu fałszywych alarmów, takie jak zmiany oświetlenia i cieni.
* Niektóre ruchu mogą nie zostać wykryte, ze względu na problemy techniczne; na przykład nocne przetwarzania wideo, obiekty półprzezroczyste i małych obiektów.

## <a name="net-sample-code"></a>Przykładowy kod .NET

Poniższy program pokazuje jak:

1. Utworzenie elementu zawartości i przekaż plik multimedialny do niego.
2. Tworzenie zadania za pomocą zadania wykrywania ruchu na obrazie wideo w zależności od pliku konfiguracji, który zawiera następujące ustawienie wstępne json: 
   
    ```json
            {
            "Version": "1.0",
            "Options": {
                "SensitivityLevel": "medium",
                "FrameSamplingValue": 1,
                "DetectLightChange": "False",
                "MergeTimeThreshold":
                "00:00:02",
                "DetectionZones": [
                [
                    {"x": 0, "y": 0},
                    {"x": 0.5, "y": 0},
                    {"x": 0, "y": 1}
                ],
                [
                    {"x": 0.3, "y": 0.3},
                    {"x": 0.55, "y": 0.3},
                    {"x": 0.8, "y": 0.3},
                    {"x": 0.8, "y": 0.55},
                    {"x": 0.8, "y": 0.8},
                    {"x": 0.55, "y": 0.8},
                    {"x": 0.3, "y": 0.8},
                    {"x": 0.3, "y": 0.55}
                ]
                ]
            }
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

namespace VideoMotionDetection
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

            // Run the VideoMotionDetection job.
            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\VideoMotionDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
        }

        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Video Motion Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Video Motion Detection Job");

            // Get a reference to Azure Media Motion Detector.
            string MediaProcessorName = "Azure Media Motion Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Video Motion Detection Output Asset", AssetCreationOptions.None);

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
[Blog dotyczący platformy Azure wykrywanie ruchu usługi Media Services](https://azure.microsoft.com/blog/motion-detector-update/)

[Przegląd Analityki usługi Azure Media Services](media-services-analytics-overview.md)

[Pokazy usługi Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

