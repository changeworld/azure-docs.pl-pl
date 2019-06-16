---
title: Redagowanie twarze za pomocą usługi Azure Media Analytics | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak modyfikowanie twarze za pomocą usługi Azure media analytics.
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
ms.author: juliako;
ms.openlocfilehash: 1fe003ae13bc5f195932f4f140e17c4dc2791959
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61247402"
---
# <a name="redact-faces-with-azure-media-analytics"></a>Redagowanie twarze za pomocą usługi Azure Media Analytics 
## <a name="overview"></a>Omówienie
**Usługa Azure Media Redactor** jest [analizy multimediów Azure](media-services-analytics-overview.md) procesor multimediów (MP), który oferuje pomocą redakcji twarzy skalowalny w chmurze. Pomocą redakcji twarzy umożliwia modyfikowanie wideo w celu rozmycie twarze wybranych osób. Można użyć usługa redakcji twarzy w publicznych scenariuszach bezpieczeństwa i mediów informacyjnych. Kilka minut materiał, który zawiera wiele powierzchni może zająć godzin redagowanie ręcznie, ale z tą usługą procesu redakcji twarzy wymaga tylko kilku prostych krokach. Aby uzyskać więcej informacji, zobacz [to](https://azure.microsoft.com/blog/azure-media-redactor/) blogu.

Ten artykuł zawiera szczegółowe informacje o **usługi Azure Media Redactor** i pokazuje, jak z niej korzystać z zestawu SDK usługi Media Services dla platformy .NET.

## <a name="face-redaction-modes"></a>Tryby redakcji twarzy
Redakcji twarzy polega na wykrywanie twarzy w każdej klatce, wideo i śledzenia obiektu twarzy zarówno do przodu i wstecz w czasie, dzięki czemu tę samą osobę, można rozmyciu z innych kąty, jak również. Proces redakcyjne automatyczne jest złożony, i jest nie zawsze produktu 100% żądaną produktu wyjściowego, z tego powodu usługa Media Analytics zapewnia kilka sposobów, aby zmodyfikować końcowych danych wyjściowych.

Oprócz pełni automatycznym Brak przepływu pracy dwuprzebiegową, która umożliwia wybór/cofania-selection znaleziono twarze za pomocą listy identyfikatorów. Ponadto zapewnienie dowolnego na ramkę dostosowania pakietu Administracyjnego programu używa plik metadanych w formacie JSON. Ten przepływ pracy zostanie podzielona na **analizy** i **Redact** tryby. Możesz połączyć dwa tryby w jednym przebiegu, z systemem zarówno do zadań w jedno zadanie. ten tryb jest nazywany **Łączony**.

### <a name="combined-mode"></a>Tryb połączone
To daje zostały zredagowane mp4 automatycznie bez wszelkich ręcznych danych wejściowych.

| Etap | Nazwa pliku | Uwagi |
| --- | --- | --- |
| Zasób wejściowy |foo.bar |Wideo w formacie MP4, WMV i MOV |
| Dane wejściowe konfiguracji |Ustawienie konfiguracji Zadań wstępne |{"version": "1.0',"opcje": {"mode":"połączone"}} |
| Elementu zawartości wyjściowej |foo_redacted.mp4 |Wideo z Rozmycie stosowane |

#### <a name="input-example"></a>Przykład danych wejściowych:
[Wyświetlanie tego wideo](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>Przykład danych wyjściowych:
[Wyświetlanie tego wideo](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>Tryb analizy
**Analizowanie** przebiegu przepływu pracy dwuprzebiegową przyjmuje wideo zestaw danych wejściowych i tworzy plik JSON zawierający lokalizacjach twarzy i obrazów jpg wszystkich wykryty twarzy.

| Etap | Nazwa pliku | Uwagi |
| --- | --- | --- |
| Zasób wejściowy |foo.bar |Wideo w formacie MP4, WMV i MPV |
| Dane wejściowe konfiguracji |Ustawienie konfiguracji Zadań wstępne |{"version": "1.0',"Opcje": {"mode":"Analizuj"}} |
| Elementu zawartości wyjściowej |foo_annotations.json |Dane adnotacji lokalizacjach twarzy w formacie JSON. Mogą to być edytowane przez użytkownikowi na modyfikację Rozmycie blokujących pola. Zobacz poniższy przykład. |
| Elementu zawartości wyjściowej |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Przycięty jpg każdego wykryte twarze, gdzie numer wskazuje Etykiety twarz |

#### <a name="output-example"></a>Przykład danych wyjściowych:

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

### <a name="redact-mode"></a>Redagowanie tryb
Drugi przebieg przepływu pracy ma zbyt wiele danych wejściowych, które muszą być połączone w pojedynczy zasób.

W tym listę Identyfikatorów do rozmycia, klip wideo i adnotacje JSON. W tym trybie używa adnotacje do zastosowania Rozmycie na wejściowego pliku wideo.

Dane wyjściowe z analizy — dostęp próbny nie obejmuje oryginalnego filmu wideo. Wideo musi zostać przekazany do wejściowego elementu dla zadania trybu Redact i wybrany jako plik podstawowy.

| Etap | Nazwa pliku | Uwagi |
| --- | --- | --- |
| Zasób wejściowy |foo.bar |Film wideo w formacie MP4, WMV i MPV. Wartość taka sama wideo, tak jak w kroku 1. |
| Zasób wejściowy |foo_annotations.json |plik metadanych adnotacje z fazy, z modyfikacjami opcjonalne. |
| Zasób wejściowy |foo_IDList.txt (opcjonalnie) |Opcjonalne nowy wiersz rozdzielaną listę identyfikatorów redagowanie rozpoznawania twarzy. Jeśli pole pozostanie puste, to rozmywa twarze na wszystkie. |
| Dane wejściowe konfiguracji |Ustawienie konfiguracji Zadań wstępne |{"version": "1.0',"Opcje": {"mode":"redagowanie"}} |
| Elementu zawartości wyjściowej |foo_redacted.mp4 |Plik wideo za pomocą Rozmycie stosowane na podstawie adnotacji |

#### <a name="example-output"></a>Przykładowe dane wyjściowe
To wynika z IDList identyfikatorem jeden wybrany.

[Wyświetlanie tego wideo](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

Przykład foo_IDList.txt
 
     1
     2
     3

## <a name="blur-types"></a>Rozmycie typów

W **łączony** lub **Redact** trybie 5 tryby różnych rozmycia, możesz wybrać spośród za pośrednictwem konfiguracji danych wejściowych JSON: **Niska**, **Med**, **wysokiej**, **pole**, i **czarny**. Domyślnie **Med** jest używany.

Można znaleźć przykłady typów Rozmycie poniżej.

### <a name="example-json"></a>Przykładowy plik JSON:

```json
    {'version':'1.0', 'options': {'Mode': 'Combined', 'BlurType': 'High'}}
```

#### <a name="low"></a>Małe

![Małe](./media/media-services-face-redaction/blur1.png)
 
#### <a name="med"></a>MED

![MED](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>Wysoka

![Wysoka](./media/media-services-face-redaction/blur3.png)

#### <a name="box"></a>Box

![Box](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>Czarny

![Czarny](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>Elementy danych wyjściowych pliku JSON

Pakiet administracyjny redakcyjne zapewnia wykrywania położenia twarzy dużej dokładności i śledzenia, który może wykryć do 64 ludzkich twarzy w ramce wideo. Czołowego twarzy zapewniają najlepsze rezultaty, podczas boczne i małym twarzy (mniejsze niż lub równe 24 x 24 piksele) są trudne.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>Przykładowy kod .NET

Poniższy program pokazuje jak:

1. Utworzenie elementu zawartości i przekaż plik multimedialny do niego.
2. Utworzenie zadania z zadaniem redakcji twarzy oparty na pliku konfiguracji, który zawiera następujące ustawienie wstępne json: 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
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

## <a name="next-steps"></a>Kolejne kroki

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Powiązane linki
[Przegląd Analityki usługi Azure Media Services](media-services-analytics-overview.md)

[Pokazy usługi Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

