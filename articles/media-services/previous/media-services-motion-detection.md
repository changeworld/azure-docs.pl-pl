---
title: Wykrywanie ruchów za pomocą usługi Azure Media Analytics | Dokumenty firmy Microsoft
description: Procesor multimediów Azure Media Motion Detector (MP) umożliwia efektywne identyfikowanie sekcji zainteresowań w ramach długiego i niezdarnego wideo.
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
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: f4c021531a4d04bf16e5dbee4172952433f675d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913008"
---
# <a name="detect-motions-with-azure-media-analytics"></a>Wykrywanie ruchów za pomocą usługi Azure Media Analytics

> [!NOTE]
> Procesor nośników **Azure Media Motion Detector** zostanie wycofany. W przypadku daty wycofania zobacz temat [starszych składników.](legacy-components.md)
 
## <a name="overview"></a>Omówienie

Procesor **multimediów Azure Media Motion Detector** (MP) umożliwia efektywne identyfikowanie sekcji zainteresowań w ramach długiego i niezdarnego wideo. Wykrywanie ruchu może być używane na statycznych nagraniach z kamery do identyfikacji sekcji wideo, w których występuje ruch. Generuje plik JSON zawierający metadane z sygnaturami czasowymi i region ograniczający, w którym wystąpiło zdarzenie.

Technologia ta, ukierunkowana na kanały wideo związane z bezpieczeństwem, jest w stanie kategoryzować ruch w odpowiednich zdarzeniach i fałszywych alarmach, takich jak cienie i zmiany oświetlenia. Pozwala to na generowanie alertów bezpieczeństwa z kanałów z kamer bez spamowania niekończącymi się nieistotnymi wydarzeniami, jednocześnie będąc w stanie wyodrębnić interesujące momenty z długich filmów z monitoringu.

**Usługa Azure Media Motion Detector** MP jest obecnie w wersji zapoznawczej.

Ten artykuł zawiera szczegółowe informacje na temat **detektora ruchu multimediów platformy Azure** i pokazuje, jak używać go z zestawem SDK usługi Media Services dla platformy .NET

## <a name="motion-detector-input-files"></a>Pliki wejściowe detektora ruchu
Pliki wideo. Obecnie obsługiwane są następujące formaty: MP4, MOV i WMV.

## <a name="task-configuration-preset"></a>Konfiguracja zadania (ustawienie wstępne)
Podczas tworzenia zadania za pomocą **narzędzia Azure Media Motion Detector**należy określić predefiniowane ustawienia konfiguracji. 

### <a name="parameters"></a>Parametry
Można użyć następujących parametrów:

| Nazwa | Opcje | Opis | Domyślne |
| --- | --- | --- | --- |
| czułośćPoziom |Ciąg:'niski', 'średni', 'wysoki' |Ustawia poziom czułości, na którym są zgłaszane ruchy. Dostosuj to, aby dostosować liczbę fałszywych alarmów. |"średni" |
| frameSamplingValue |Dodatnia wartość całkowita |Ustawia częstotliwość, z jaką algorytm jest uruchamiany. 1 równa się każdej klatce, 2 oznacza co drugą klatkę i tak dalej. |1 |
| detectLightChange (wykrywanie światła) |Wartość logiczna:'prawda', 'false' |Określa, czy zmiany światła są zgłaszane w wynikach |"Fałsz" |
| mergeTimeThreshold |Xs-time: Hh:mm:ss<br/>Przykład: 00:00:03 |Określa przedział czasu między zdarzeniami ruchu, w którym 2 zdarzenia są łączone i zgłaszane jako 1. |00:00:00 |
| wykrywanie Stref |Tablica stref wykrywania:<br/>- Strefa wykrywania to tablica 3 lub więcej punktów<br/>- Punkt jest współrzędną x i y od 0 do 1. |W tym artykule opisano listę stref wykrywania wielokątnych, które mają być używane.<br/>Wyniki są zgłaszane ze strefami jako identyfikatorem, a pierwszy z nich to 'id':0 |Pojedyncza strefa, która obejmuje całą ramę. |

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

## <a name="motion-detector-output-files"></a>Pliki wyjściowe detektora ruchu
Zadanie wykrywania ruchu zwraca plik JSON w zasobie wyjściowym, który opisuje alerty ruchu i ich kategorie w filmie. Plik zawiera informacje o czasie i czasie trwania ruchu wykrytego w filmie.

Interfejs API detektora ruchu udostępnia wskaźniki, gdy w stałym tle wideo znajdują się obiekty w tle (na przykład wideo z monitoringu). Czujka ruchu jest przeszkolona w zakresie redukcji fałszywych alarmów, takich jak oświetlenie i zmiany cienia. Obecne ograniczenia algorytmów obejmują filmy noktowizyjne, obiekty półprzezroczyste i małe obiekty.

### <a name="elements-of-the-output-json-file"></a><a id="output_elements"></a>Elementy wyjściowego pliku JSON
> [!NOTE]
> W najnowszej wersji format JSON dane wyjściowe zmienił się i może stanowić przełomową zmianę dla niektórych klientów.
> 
> 

W poniższej tabeli opisano elementy wyjściowego pliku JSON.

| Element | Opis |
| --- | --- |
| version |Odnosi się to do wersji interfejsu API wideo. Bieżąca wersja to 2. |
| skala czasu |"Kleszcze" na sekundę filmu. |
| przesunięcie |Przesunięcie czasu dla znaczników czasu w "kleszczach". W wersji 1.0 interfejsów API wideo zawsze będzie to 0. W przyszłych scenariuszach, które obsługujemy, ta wartość może ulec zmianie. |
| Ilość klatek |Liczba klatek na sekundę w wideo. |
| szerokość, wysokość |Odnosi się do szerokości i wysokości wideo w pikselach. |
| rozpoczynanie |Sygnatura czasowa startu w "kleszczach". |
| czas trwania |Długość zdarzenia, w "kleszczach". |
| interval |Interwał każdego wpisu w zdarzeniu w "kleszczach". |
| zdarzenia |Każdy fragment zdarzenia zawiera ruch wykryty w tym czasie. |
| type |W bieżącej wersji jest to zawsze "2" dla ruchu ogólnego. Ta etykieta zapewnia interfejsom API wideo elastyczność kategoryzowania ruchu w przyszłych wersjach. |
| regionId |Jak wyjaśniono powyżej, zawsze będzie to 0 w tej wersji. Ta etykieta zapewnia interfejsowi API wideo elastyczność w znajdowaniu ruchu w różnych regionach w przyszłych wersjach. |
| Regionów |Odnosi się do obszaru w filmie, w którym zależy Ci na ruchu. <br/><br/>-"id" reprezentuje obszar regionu - w tej wersji jest tylko jeden, ID 0. <br/>-"typ" reprezentuje kształt regionu, na który ci zależy ruch. Obecnie obsługiwane są "prostokąt" i "wielokąt".<br/> Jeśli określono "prostokąt", region ma wymiary w X, Y, Szerokość i Wysokość. Współrzędne X i Y reprezentują lewą górną współrzędne XY regionu w znormalizowanej skali od 0,0 do 1,0. Szerokość i wysokość reprezentują rozmiar regionu w znormalizowanej skali od 0,0 do 1,0. W bieżącej wersji X, Y, Szerokość i Wysokość są zawsze ustalane na 0, 0 i 1, 1. <br/>Jeśli określono "wielokąt", region ma wymiary w punktach. <br/> |
| Fragmenty |Metadane są podzielone na różne segmenty zwane fragmentami. Każdy fragment zawiera rozpoczęcie, czas trwania, wartość interwału i zdarzenia. Fragment bez zdarzeń oznacza, że nie wykryto żadnego ruchu w tym czasie rozpoczęcia i czasie trwania. |
| wsporniki [] |Każdy nawias reprezentuje jeden interwał w zdarzeniu. Puste nawiasy dla tego interwału oznaczają, że nie wykryto żadnego ruchu. |
| locations |Ten nowy wpis w obszarze Zdarzenia zawiera listę lokalizacji, w której wystąpił ruch. Jest to bardziej szczegółowe niż strefy wykrywania. |

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
* Obsługiwane formaty wideo wejściowe obejmują MP4, MOV i WMV.
* Wykrywanie ruchu jest zoptymalizowane pod kątem stacjonarnych filmów w tle. Algorytm koncentruje się na zmniejszaniu fałszywych alarmów, takich jak zmiany oświetlenia i cienie.
* Niektóre ruchy mogą nie zostać wykryte z powodu problemów technicznych; na przykład filmy noktowizyjne, obiekty półprzezroczyste i małe obiekty.

## <a name="net-sample-code"></a>Przykładowy kod platformy .NET

W poniższym programie pokazano, jak:

1. Utwórz zasób i przekaż plik multimedialny do zasobu.
2. Utwórz zadanie z zadaniem wykrywania ruchu wideo na podstawie pliku konfiguracyjnego zawierającego następujące ustawienia json: 
   
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
[Blog detektora ruchu usługi Azure Media Services](https://azure.microsoft.com/blog/motion-detector-update/)

[Omówienie usługi Azure Media Services Analytics](media-services-analytics-overview.md)

[Pokazy usługi Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

