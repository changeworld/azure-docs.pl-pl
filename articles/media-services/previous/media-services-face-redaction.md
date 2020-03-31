---
title: Redagowanie twarzy za pomocą usługi Azure Media Analytics | Dokumenty firmy Microsoft
description: Usługa Azure Media Redactor to procesor multimediów usługi Azure Media Analytics, który oferuje skalowalną redakcję twarzy w chmurze. W tym artykule pokazano, jak redagować twarze za pomocą analizy multimediów platformy Azure.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6a1b7a76ef1efda51f09ac733b3d434235ff40ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900300"
---
# <a name="redact-faces-with-azure-media-analytics"></a>Redagowanie twarzy za pomocą usługi Azure Media Analytics 
## <a name="overview"></a>Omówienie
**Usługa Azure Media Redactor** to procesor multimediów [usługi Azure Media Analytics](media-services-analytics-overview.md) (MP), który oferuje skalowalną redakcję twarzy w chmurze. Funkcja przeredagowania twarzy umożliwia modyfikowanie filmu w celu rozmycia twarzy wybranych osób. Możesz użyć usługi redagowania twarzy w scenariuszach bezpieczeństwa publicznego i mediów informacyjnych. Kilka minut materiału, który zawiera wiele twarzy może potrwać wiele godzin, aby redagować ręcznie, ale z tej usługi proces redakcyjny twarzy będzie wymagać tylko kilku prostych kroków. Aby uzyskać więcej informacji, zobacz [ten](https://azure.microsoft.com/blog/azure-media-redactor/) blog.

Ten artykuł zawiera szczegółowe informacje na temat **usługi Azure Media Redactor** i pokazuje, jak go używać z zestawem SDK usługi Media Services dla platformy .NET.

## <a name="face-redaction-modes"></a>Tryby przeredagowania twarzy
Redakcja twarzy polega na wykrywaniu twarzy w każdej klatce wideo i śledzeniu obiektu twarzy zarówno do przodu, jak i do tyłu w czasie, dzięki czemu ta sama osoba może być zamazana pod innymi kątami. Zautomatyzowany proces redakcyjny jest złożony i nie zawsze generuje 100% żądanych danych wyjściowych, z tego powodu Media Analytics zapewnia kilka sposobów modyfikowania ostatecznego wyjścia.

Oprócz trybu w pełni automatycznego, istnieje dwuprzebiegowy przepływ pracy, który umożliwia wybór / de-wybór znalezionych twarzy za pośrednictwem listy identyfikatorów. Ponadto, aby dowolne dopasowania na klatkę MP używa pliku metadanych w formacie JSON. Ten przepływ pracy jest podzielony na **tryby Analizy** i **Redact.** Można połączyć dwa tryby w jednym przebiegu, który uruchamia oba zadania w jednym zadaniu; ten tryb nazywa się **Combined**.

### <a name="combined-mode"></a>Tryb łączony
Powoduje to automatyczne zredagowanie mp4 bez ręcznego wprowadzania danych.

| Etap | Nazwa pliku | Uwagi |
| --- | --- | --- |
| Zasób wejściowy |foo.bar |Wideo w formacie WMV, MOV lub MP4 |
| Config wejściowy |Predefiniowane ustawienia konfiguracji zadania |{'version':'1.0', 'options': {'mode':'combined'}} |
| Zasób wyjściowy |foo_redacted.mp4 |Wideo z zastosowanym rozmyciem |

#### <a name="input-example"></a>Przykład wejściowy:
[zobacz ten film](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>Przykładowe dane wyjściowe:
[zobacz ten film](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>Tryb analizy
Przebieg **analizy** dwuprzebiegowego przepływu pracy pobiera wejście wideo i tworzy plik JSON lokalizacji twarzy oraz obrazy jpg każdej wykrytej twarzy.

| Etap | Nazwa pliku | Uwagi |
| --- | --- | --- |
| Zasób wejściowy |foo.bar |Wideo w formacie WMV, MPV lub MP4 |
| Config wejściowy |Predefiniowane ustawienia konfiguracji zadania |{'version':'1.0', 'options': {'mode':'analyze'}} |
| Zasób wyjściowy |foo_annotations.json |Dane adnotacji lokalizacji twarzy w formacie JSON. Może to być edytowane przez użytkownika, aby zmodyfikować rozmyte obwiednie. Zobacz przykład poniżej. |
| Zasób wyjściowy |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Przycięte o rozcięcie każdej wykrytej twarzy, gdzie liczba wskazuje labelId twarzy |

#### <a name="output-example"></a>Przykładowe dane wyjściowe:

```json
    {
      "version": 1,
      "timescale": 24000,
      "offset": 0,
      "framerate": 23.976,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 48048,
          "interval": 1001,
          "events": [
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [
              {
                "index": 13,
                "id": 1138,
                "x": 0.29537,
                "y": -0.18987,
                "width": 0.36239,
                "height": 0.80335
              },
              {
                "index": 13,
                "id": 2028,
                "x": 0.60427,
                "y": 0.16098,
                "width": 0.26958,
                "height": 0.57943
              }
            ],

    … truncated
```

### <a name="redact-mode"></a>Tryb redact
Drugi przebieg przepływu pracy zajmuje większą liczbę danych wejściowych, które muszą być połączone w jeden zasób.

Obejmuje to listę identyfikatorów do rozmycia, oryginalny film i adnotacje JSON. W tym trybie adnotacje są stosowane do rozmywania na wejściowym filmie wideo.

Dane wyjściowe z przebiegu Analizy nie zawierają oryginalnego wideo. Wideo musi zostać przesłane do zasobu wejściowego dla zadania trybu Redact i wybrane jako plik podstawowy.

| Etap | Nazwa pliku | Uwagi |
| --- | --- | --- |
| Zasób wejściowy |foo.bar |Wideo w formacie WMV, MPV lub MP4. Ten sam film, jak w kroku 1. |
| Zasób wejściowy |foo_annotations.json |adnotacje plik metadanych z fazy pierwszej, z opcjonalnymi modyfikacjami. |
| Zasób wejściowy |foo_IDList.txt (opcjonalnie) |Opcjonalna nowa lista oddzielonych wierszami identyfikatorów twarzy do redagowanie. Jeśli pozostanie puste, spowoduje to rozmycie wszystkich ścian. |
| Config wejściowy |Predefiniowane ustawienia konfiguracji zadania |{'version':'1.0', 'options': {'mode':'redact'}} |
| Zasób wyjściowy |foo_redacted.mp4 |Wideo z rozmyciem zastosowanym na podstawie adnotacji |

#### <a name="example-output"></a>Przykładowe dane wyjściowe
Jest to dane wyjściowe z listy identyfikatorów z wybraną jednym identyfikatorem.

[zobacz ten film](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

Przykład foo_IDList.txt
 
     1
     2
     3

## <a name="blur-types"></a>Typy rozmycia

W trybie **Połączone** lub **Redact** istnieje 5 różnych trybów rozmycia, z których można wybierać za pomocą konfiguracji wejściowej JSON: **Niski,** **Med,** **Wysoki,** **Box**i **Czarny.** Domyślnie używany jest **Med.**

Próbki typów rozmycia można znaleźć poniżej.

### <a name="example-json"></a>Przykład JSON:

```json
    {'version':'1.0', 'options': {'Mode': 'Combined', 'BlurType': 'High'}}
```

#### <a name="low"></a>Małe

![Małe](./media/media-services-face-redaction/blur1.png)
 
#### <a name="med"></a>Med

![Med](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>Wysoka

![Wysoka](./media/media-services-face-redaction/blur3.png)

#### <a name="box"></a>Box

![Box](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>Czarny

![Czarny](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>Elementy wyjściowego pliku JSON

Redaction MP zapewnia precyzyjne wykrywanie i śledzenie lokalizacji twarzy, które może wykryć do 64 ludzkich twarzy w ramce wideo. Ściany czołowe zapewniają najlepsze wyniki, podczas gdy ściany boczne i małe ściany (mniej lub równe 24x24 pikselom) są trudne.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>Przykładowy kod platformy .NET

W poniższym programie pokazano, jak:

1. Utwórz zasób i przekaż plik multimedialny do zasobu.
2. Utwórz zadanie z zadaniem redakcyjnym twarzy na podstawie pliku konfiguracyjnego zawierającego następujące ustawienia json: 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
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

namespace FaceRedaction
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

            // Run the FaceRedaction job.
            var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                        @"C:\supportFiles\FaceRedaction\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
        }

        static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Face Redaction Input Asset",
            AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Redaction Job");

            // Get a reference to Azure Media Redactor.
            string MediaProcessorName = "Azure Media Redactor";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Redaction Task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);

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

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Powiązane linki
[Omówienie usługi Azure Media Services Analytics](media-services-analytics-overview.md)

[Pokazy usługi Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

