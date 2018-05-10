---
title: Wykrywanie ruchów z analizy multimediów Azure | Dokumentacja firmy Microsoft
description: Procesor multimediów czujnik ruchu Azure Media (MP) umożliwia wydajne zidentyfikować sekcje zawierają informacje przydatne w wideo w innym przypadku długich i procesu.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: milanga;juliako;
ms.openlocfilehash: 8488b968fe2ab823479d70a98ba86be97b28f67d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="detect-motions-with-azure-media-analytics"></a>Wykrywanie ruchów z analizy multimediów Azure
## <a name="overview"></a>Przegląd
**Czujnik ruchu Azure Media** procesor multimediów (MP) umożliwia wydajne zidentyfikować sekcje zawierają informacje przydatne w wideo w innym przypadku długich i procesu. Wykrywanie ruchu można w statycznej kamery sekcje wideo, gdzie występuje ruchu. Generuje plik JSON zawierający metadane z sygnatury czasowe i ograniczający regionu, w którym wystąpiło zdarzenie.

Docelowe do źródła wideo zabezpieczeń, ta technologia jest w stanie kategoryzację ruchu na odpowiednie zdarzenia i fałszywych alarmów, takie jak cieni i zmian oświetlenia. Umożliwia generowanie alertów zabezpieczeń z aparatu fotograficznego źródła danych bez otrzymywania wiadomości-śmieci nieskończone zdarzenia nie ma znaczenia, będąc wyodrębnić chwil płynących z wideo długi nadzoru.

**Czujnik ruchu Azure Media** pakiet administracyjny jest obecnie w przeglądzie.

Ten artykuł zawiera szczegółowe informacje o **czujnik ruchu Azure Media** i pokazuje, jak z niego korzystać z zestawu SDK usługi Media Services dla platformy .NET

## <a name="motion-detector-input-files"></a>Pliki wejściowe wykrywanie ruchu
Pliki wideo. Obecnie obsługiwane są następujące formaty: MP4, MOV i WMV.

## <a name="task-configuration-preset"></a>Konfiguracja zadania (ustawienia domyślne)
Podczas tworzenia zadania z **czujnik ruchu Azure Media**, należy określić ustawienia domyślne konfiguracji. 

### <a name="parameters"></a>Parametry
Można użyć poniższych parametrów:

| Name (Nazwa) | Opcje | Opis | Domyślne |
| --- | --- | --- | --- |
| sensitivityLevel |Ciąg: "Niski", "medium',"Wysoki" |Ustawia poziom czułości na ruchy, które są zgłaszane. Dostosuj, aby dostosować liczbę fałszywych alarmów. |"Średni" |
| frameSamplingValue |Dodatnia liczba całkowita |Określa częstotliwość, w której działa algorytmu. Każda ramka jest równa 1, 2 oznacza, że co drugi ramki i tak dalej. |1 |
| detectLightChange |Wartość logiczna: "prawda", "fałsz" |Określa, czy zmiany światła są podane w wynikach |"Fałsz" |
| mergeTimeThreshold |Czas xs: Hh: mm:<br/>Przykład: 00:00:03 |Określa przedział czasu między zdarzeniami ruchu, gdzie są 2 zdarzenia można łączyć i zgłaszane jako 1. |00:00:00 |
| detectionZones |Tablica wykrywania stref:<br/>-Wykrywania strefy jest tablicą 3 lub więcej punktów<br/>— Punkt jest x i y Współrzędna z zakresu od 0 do 1. |Zawiera opis listy wykrywania wielobocznych stref ma być używany.<br/>Wyniki są zgłaszane z strefy jako Identyfikatora, z których pierwszy z nich jest 'id': 0 |Jednej strefie, która obejmuje całą ramkę. |

### <a name="json-example"></a>Przykład JSON

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
Zadanie wykrywania ruchu zwraca plik JSON trwałego dane wyjściowe opisuje alerty ruchu i ich w kategorie wideo. Plik zawiera informacje o czas i czas trwania ruchu wykryto wideo.

Interfejs API wykrywanie ruchu zawiera wskaźniki po obiektów w ruchu w stałej tło w formie wideo (na przykład nadzoru, wideo). Czujnik ruchu jest uczony zmniejszenie fałszywe alarmy, takie jak oświetlenia i zmiany w tle. Bieżące ograniczenia algorytmy obejmują nocy wizji wideo, półprzezroczyste obiektów i małych obiektów.

### <a id="output_elements"></a>Elementy pliku wyjściowego w formacie JSON
> [!NOTE]
> W najnowszej wersji formatu danych wyjściowych JSON została zmieniona, może reprezentować istotne zmiany w przypadku niektórych klientów.
> 
> 

W poniższej tabeli opisano elementy pliku wyjściowego w formacie JSON.

| Element | Opis |
| --- | --- |
| Wersja |Odnosi się do wersji interfejsu API wideo. Bieżąca wersja to 2. |
| skali czasu |"Impulsów" na sekundę wideo. |
| Przesunięcie |Przesunięcie czasu dla sygnatury czasowe w parametrem "ticks". W wersji 1.0 interfejsów API, wideo ta będzie zawsze równa 0. W przyszłości scenariusze, które firma Microsoft obsługuje, ta wartość może zmienić. |
| szybkość klatek |Klatek na sekundę wideo. |
| Szerokość, wysokość |Odnosi się do szerokości i wysokości wideo w pikselach. |
| Uruchamianie |Sygnatura czasowa rozpoczęcia w parametrem "ticks". |
| Czas trwania |Długość zdarzenia w parametrem "ticks". |
| Interwał |Interwał każdego wpisu w zdarzeniu w parametrem "ticks". |
| Zdarzenia |Każdy fragment zdarzenia zawiera ruchu w tym czas trwania. |
| Typ |W bieżącej wersji jest to zawsze "2" dla ogólnego ruchu. Dzięki temu etykiety wideo API elastyczność kategoryzację ruchu w przyszłych wersjach. |
| RegionID |Zgodnie z powyższymi wskazówkami, ta będzie zawsze równa 0 w tej wersji. Etykieta daje API wideo elastyczność można znaleźć ruchu w różnych regionach w przyszłych wersjach. |
| Regiony |Odnosi się do obszaru w wideo, gdzie interesujących ruchu. <br/><br/>-"id" reprezentuje obszar region — w tej wersji jest tylko jeden, identyfikator: 0. <br/>-"type" reprezentuje kształt obszaru interesujących dla ruchu. "Prostokąt" i "wielokąta" są obecnie obsługiwane.<br/> Jeśli określono "prostokąt" region ma wymiarów w X, Y, szerokość i wysokość. Współrzędne X i Y reprezentują górna lewa współrzędnych XY regionu w znormalizowane skali od 0,0 do 1,0. Szerokość i wysokość reprezentuje rozmiar regionu w znormalizowane skali od 0,0 do 1,0. W bieżącej wersji X, Y, szerokość i wysokość jest zawsze ustalone na 0, 0 i 1, 1. <br/>Jeśli określono "wielokąta" region ma wymiarów w punktach. <br/> |
| fragmenty |Metadane fragmentaryczne jest się w różnych segmentach fragmenty. Każdy fragmentu zawiera rozpoczęcia, czas trwania, liczba interwałów i zdarzenia. Fragment ze zdarzeniami nie oznacza, że ruch nie został wykryty podczas tej godziny rozpoczęcia i czas trwania. |
| Nawiasy kwadratowe] |Każdy nawiasu reprezentuje jeden interwał w zdarzeniu. Puste nawiasy dla tego przedziału oznacza, że ruch nie została wykryta. |
| Lokalizacje |Ten nowy wpis w polu zdarzenia wymieniono lokalizacji, w którym wystąpił ruchu. Jest to bardziej szczegółowy niż stref wykrywania. |

W poniższym przykładzie JSON przedstawiono dane wyjściowe:

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
* Obsługiwane formaty wideo wejściowych obejmują MP4, MOV i WMV.
* Wykrywanie ruchu jest zoptymalizowana pod kątem wideo nieruchomy tła. Algorytm koncentruje się na zmniejszenie fałszywe alarmy, takie jak zmiany oświetlenia i cieni.
* Niektóre ruchu mogą nie być wykrywane z powodu problemów technicznych; na przykład nocy wizji wideo, półprzezroczyste obiektów i małych obiektów.

## <a name="net-sample-code"></a>.NET przykładowy kod

Następujących programów przedstawiono sposób:

1. Utworzenie elementu zawartości i przesyłanie pliku multimediów do elementu zawartości.
2. Utwórz zadanie z zadania wykrywania ruchu na obrazie wideo w zależności od pliku konfiguracji, który zawiera następujące ustawienie json: 
   
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
            task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);

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
[Blog Azure Media Services ruchu detektora](https://azure.microsoft.com/blog/motion-detector-update/)

[Przegląd analiz usługi Azure Media Services](media-services-analytics-overview.md)

[W trakcie analizy multimediów Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

