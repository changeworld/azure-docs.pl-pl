---
title: Redagowanie krojów z analizy multimediów Azure | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono sposób redagowanie krojów z analizy multimediów Azure.
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
ms.author: juliako;
ms.openlocfilehash: 910cc246aa19e19b109fc660682c6b2dc239cbb7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="redact-faces-with-azure-media-analytics"></a>Redagowanie krojów z analizy multimediów Azure
## <a name="overview"></a>Przegląd
**Azure Media Redactor** jest [analizy multimediów Azure](media-services-analytics-overview.md) procesor multimediów (MP) oferuje skalowalne krój redakcyjne w chmurze. Redakcyjne krój umożliwia modyfikowanie wideo do rozmycia kroje wybrane osoby. Można korzystać z usługi redakcyjne krój w publicznych scenariusze bezpieczeństwa i nośnika wiadomości. Kilka minut najmniejszym zawiera wiele kroje może zająć godzin redagowanie ręcznie, ale z tą usługą krój redakcyjne wymagany wykonanie kilku prostych krokach. Aby uzyskać więcej informacji, zobacz [to](https://azure.microsoft.com/blog/azure-media-redactor/) blogu.

Ten artykuł zawiera szczegółowe informacje o **Azure Media Redactor** i pokazuje, jak z niego korzystać z zestawu SDK usługi Media Services dla platformy .NET.

## <a name="face-redaction-modes"></a>Tryby redakcyjne krój
Twarzy redakcyjne polega na wykrywaniu kroje w każdej ramce wideo i śledzenia obiektu krój zarówno przodu i do tyłu w czasie, tak, aby w tej samej osoby mogą rozmyciu z innych kątów również. Proces automatycznego redakcyjne jest złożony i jest nie zawsze produktu 100% żądanego wyniku, z tego powodu analizy multimediów udostępnia kilka sposobów, aby zmodyfikować ostateczne dane wyjściowe.

Oprócz pełni automatycznym jest przepływ pracy dwa przebiegu, co umożliwia wybór/dezaktywuje-selection z znaleziono kroje za pomocą listy identyfikatorów. Aby dowolnego na dopasowania ramki PZ używa również, plik metadanych w formacie JSON. Ten przepływ pracy jest podzielony na **Analizuj** i **Redact** trybów. Możesz połączyć ze sobą dwa tryby w jednym przebiegu uruchomioną obu zadań w jedno zadanie. Ten tryb jest nazywany **nomenklatury**.

### <a name="combined-mode"></a>Tryb połączone
To spowoduje utworzenie zredagowanym mp4 automatycznie bez żadnych ręcznego wprowadzania.

| Etap | Nazwa pliku | Uwagi |
| --- | --- | --- |
| Wejściowy zasobów |foo.bar |Wideo w formacie WMV, MOV lub MP4 |
| Dane wejściowe konfiguracji |Zadania konfiguracji ustawień wstępnych. |{"version": "1.0", "Opcje": {'mode': "łączyć"}} |
| Dane wyjściowe zasobów |foo_redacted.mp4 |Wideo z rozmycia stosowane |

#### <a name="input-example"></a>Przykład wejściowych:
[Wyświetl ten film](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>Przykład danych wyjściowych:
[Wyświetl ten film](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>Analizowanie tryb
**Analizowanie** przebieg przepływu pracy Przekaż dwa przyjmuje wejście wideo i tworzy plik JSON krój lokalizacji i obrazów jpg każdego wykryto twarzy na obrazie.

| Etap | Nazwa pliku | Uwagi |
| --- | --- | --- |
| Wejściowy zasobów |foo.bar |Wideo w formacie WMV, MPV lub MP4 |
| Dane wejściowe konfiguracji |Zadania konfiguracji ustawień wstępnych. |{"version": "1.0", "Opcje": {'mode': "analizowanie"}} |
| Dane wyjściowe zasobów |foo_annotations.json |Adnotacja danych lokalizacji krój w formacie JSON. To można edytować przez użytkownika, aby zmodyfikować rozmycia ograniczenia pola. Zobacz poniższy przykład. |
| Dane wyjściowe zasobów |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Przycięty jpg każdego wykryto krój, gdzie numer wskazuje etykiety powierzchni |

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
Drugi przebieg przepływu pracy ma większą liczbę wejść, które muszą być połączone w jeden zasobów.

W tym listę identyfikatorów rozmycia, oryginalnego wideo i adnotacje JSON. W tym trybie używa adnotacje do zastosowania do rozmycia na wejściowego pliku wideo.

Dane wyjściowe z przebiegu analizy nie obejmuje oryginalnego wideo. Wideo musi zostać przekazany do wprowadzania zasobów dla zadania trybu Redact i wybrany jako plik podstawowy.

| Etap | Nazwa pliku | Uwagi |
| --- | --- | --- |
| Wejściowy zasobów |foo.bar |Wideo w formacie WMV, MPV lub MP4. Taka sama jak w kroku 1 wideo. |
| Wejściowy zasobów |foo_annotations.json |Plik metadanych adnotacje z fazy, bez modyfikacji opcjonalne. |
| Wejściowy zasobów |foo_IDList.txt (opcjonalnie) |Lista krój identyfikatorów redagowanie rozdzielonych opcjonalne nowy wiersz. Jeśli pole pozostanie puste, to rozmywa wszystkich powierzchni. |
| Dane wejściowe konfiguracji |Zadania konfiguracji ustawień wstępnych. |{"version": "1.0", "Opcje": {'mode': "redagowanie"}} |
| Dane wyjściowe zasobów |foo_redacted.mp4 |Wideo z rozmycia stosowane w oparciu adnotacji |

#### <a name="example-output"></a>Przykładowe dane wyjściowe
Jest to IDList z jednego Identyfikatora zaznaczone dane wyjściowe.

[Wyświetl ten film](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

Przykład foo_IDList.txt
 
     1
     2
     3

## <a name="blur-types"></a>Rozmywa typów

W **nomenklatury** lub **Redact** trybie 5 rozmycia różne tryby, są dostępne za pośrednictwem konfiguracji wejściowych JSON: **małej**, **Med**, **Wysokiej**, **pole**, i **czarne**. Domyślnie **Med** jest używany.

Przykłady typów rozmycia poniżej można znaleźć.

### <a name="example-json"></a>Przykład JSON:

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

## <a name="elements-of-the-output-json-file"></a>Elementy pliku wyjściowego w formacie JSON

MP redakcyjne zawiera wysokiej precyzji krój lokalizacji wykrywania i śledzenia, który może wykryć maksymalnie 64 kroje ludzkich w ramce wideo. Kroje czołowego Podaj najlepsze wyniki, podczas boczne i w małych kroje (mniejsze niż lub równe 24 x 24 piksele) są trudne.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET przykładowy kod

Następujących programów przedstawiono sposób:

1. Utworzenie elementu zawartości i przesyłanie pliku multimediów do elementu zawartości.
2. Utwórz zadanie z zadaniem redakcyjne krój oparty na pliku konfiguracji, który zawiera następujące ustawienie json: 

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
[Przegląd analiz usługi Azure Media Services](media-services-analytics-overview.md)

[W trakcie analizy multimediów Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

