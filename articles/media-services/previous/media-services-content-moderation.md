---
title: Użyj moderatora zawartości nośnika Azure, aby wykryć możliwe zawartość dla dorosłych i luksusowych | Dokumentacja firmy Microsoft
description: Łagodzenie wideo pomaga wykryć potencjalne zawartość dla dorosłych i luksusowych w wideo.
services: media-services
documentationcenter: ''
author: sanjeev3
manager: mikemcca
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/06/2018
ms.author: sajagtap
ms.openlocfilehash: e44308f38a138c0e186e41fc8310f8b480cd4e09
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788669"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Umożliwia wykrywanie możliwych zawartość dla dorosłych i luksusowych moderatora zawartości multimediów Azure

## <a name="overview"></a>Przegląd
**Moderatora zawartości nośnika Azure** procesor multimediów (MP) pozwala na użycie łagodzenia wspierana maszyny dla plików wideo. Na przykład można wykryć możliwe zawartość dla dorosłych i luksusowych w wideo i przejrzeć zawartość oflagowane przez zespoły łagodzenia człowieka.

**Moderatora zawartości nośnika Azure** pakiet administracyjny jest obecnie w przeglądzie.

Ten artykuł zawiera szczegółowe informacje o **moderatora zawartości nośnika Azure** i pokazuje, jak z niego korzystać z zestawu SDK usługi Media Services dla platformy .NET.

## <a name="content-moderator-input-files"></a>Pliki wejściowe moderatora zawartości
Pliki wideo. Obecnie obsługiwane są następujące formaty: MP4, MOV i WMV.

## <a name="content-moderator-output-files"></a>Pliki wyjściowe moderatora zawartości
Moderowane danych wyjściowych w formacie JSON zawiera zrzuty wykryto automatycznego i kluczowych. Klatek kluczowych są zwracane z zaufania wyniki dla możliwe zawartość dla dorosłych lub luksusowych. Obejmuje to też flagę logiczną, wskazującą, czy zaleca się przegląd. Flaga zalecenie przeglądu przypisano wartości na podstawie wewnętrznych progów dla dorosłych i luksusowych wyników.

## <a name="elements-of-the-output-json-file"></a>Elementy pliku wyjściowego w formacie JSON

To zadanie tworzy plik danych wyjściowych JSON zawierający metadane dotyczące wykrytego zrzuty i kluczowych oraz tego, czy zawierają one zawartość dla dorosłych lub luksusowych.

Dane wyjściowe JSON obejmuje następujące elementy:

### <a name="root-json-elements"></a>Elementy JSON głównego

| Element | Opis |
| --- | --- |
| wersja |Wersja moderatora zawartości. |
| skali czasu |"Impulsów" na sekundę wideo. |
| Przesunięcie |Przesunięcie czasu sygnatur czasowych. W wersji 1.0 interfejsów API, wideo wartość ta będzie zawsze równa 0. Ta wartość może zmienić w przyszłości. |
| szybkość klatek |Klatek na sekundę wideo. |
| Szerokość |Szerokość ramki wyjście wideo w pikselach.|
| Wysokość |Wysokość ramki wyjście wideo w pikselach.|
| TotalDuration |Czas trwania wejścia wideo w parametrem "ticks". |
| [fragmenty](#fragments-json-elements) |Metadane fragmentaryczne jest się w różnych segmentach fragmenty. Każdy fragment jest wykrywane automatycznie zrzut rozpoczęcia, czas trwania, liczba interwałów i zdarzenia. |

### <a name="fragments-json-elements"></a>Fragmenty elementów JSON

|Element|Opis|
|---|---|
| rozpoczynanie |Czas rozpoczęcia pierwsze zdarzenie parametrem "ticks". |
| Czas trwania |Długość fragmentu w parametrem "ticks". |
| interval |Interwał każdego wpisu zdarzeń w obrębie fragmentu, w parametrem "ticks". |
| [Zdarzenia](#events-json-elements) |Każde zdarzenie reprezentuje klipem i klipach zawiera ramek kluczowych wykryte i śledzone w tym czas trwania. Jest tablicą zdarzeń. Zewnętrzne tablicy reprezentuje jeden interwał czasu. Wewnętrzny tablicy składa się z 0 lub więcej zdarzeń, które wystąpiły w danym momencie.|

### <a name="events-json-elements"></a>Elementy JSON zdarzenia

|Element|Opis|
|---|---|
| reviewRecommended | `true` lub `false` w zależności od tego, czy **adultScore** lub **racyScore** przekroczenia progów wewnętrznego. |
| adultScore | Wynik zaufania dla możliwe zawartość dla dorosłych w skali od 0,00 do 0,99. |
| racyScore | Wynik zaufania możliwe luksusowych zawartości, w skali od 0,00 do 0,99. |
| indeks | Indeks ramki w skali od pierwszej ramki do ostatniego indeksu indeksu ramki. |
| sygnatura czasowa | Lokalizacja ramki parametrem "ticks". |
| shotIndex | Zrzut indeks elementu nadrzędnego. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Zawartości łagodzenia Szybki Start i przykładowe dane wyjściowe

### <a name="task-configuration-preset"></a>Konfiguracja zadania (ustawienia domyślne)
Podczas tworzenia zadania z **moderatora zawartości nośnika Azure**, należy określić ustawienia domyślne konfiguracji. Następujące ustawienie konfiguracji dotyczy tylko łagodzenia zawartości.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>Przykładowy kod .NET

Poniższy przykład kodu .NET używa SDK .NET usługi Media Services do uruchomienia zadania moderatora zawartości. Trwa nośników usług zasobów jako dane wejściowe, zawierający wideo, aby się łagodzenie.
Zobacz [moderatora zawartości wideo szybkiego startu](../../cognitive-services/Content-Moderator/video-moderation-api.md) pełny kod źródłowy i projekt programu Visual Studio.


```csharp
    /// <summary>
    /// Run the Content Moderator job on the designated Asset from local file or blob storage
    /// </summary>
    /// <param name="asset"></param>
    static void RunContentModeratorJob(IAsset asset)
    {
        // Grab the presets
        string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

        // grab instance of Azure Media Content Moderator MP
        IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

        // create Job with Content Moderator task
        IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
                asset.AssetFiles.First() + "_" + Guid.NewGuid()));

        ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
                mp, configuration,
                TaskOptions.None);
        contentModeratorTask.InputAssets.Add(asset);
        contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
            AssetCreationOptions.None);

        job.Submit();


        // Create progress printing and querying tasks
        Task progressPrintTask = new Task(() =>
        {
            IJob jobQuery = null;
            do
            {
                var progressContext = _context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                    .First();
                    Console.WriteLine(string.Format("{0}\t{1}",
                    DateTime.Now,
                    jobQuery.State));
                    Thread.Sleep(10000);
             }
             while (jobQuery.State != JobState.Finished &&
             jobQuery.State != JobState.Error &&
             jobQuery.State != JobState.Canceled);
        });
        progressPrintTask.Start();

        Task progressJobTask = job.GetExecutionProgressTask(
        CancellationToken.None);
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
        }

        DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
    }

For the full source code and the Visual Studio project, check out the [Content Moderator video quickstart](../../cognitive-services/Content-Moderator/video-moderation-api.md).

### JSON output

The following example of a Content Moderator JSON output was truncated.

> [!NOTE]
> Location of a keyframe in seconds = timestamp/timescale

    {
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]
    }
    ]
    }
```

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Powiązane linki
[Przegląd analiz usługi Azure Media Services](media-services-analytics-overview.md)

[W trakcie analizy multimediów Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat zawartości moderatora [graficznej łagodzenia i przejrzyj](../../cognitive-services/Content-Moderator/video-moderation-human-review.md).

Pobierz pełny kod źródłowy i projekt programu Visual Studio z [szybkiego startu wideo łagodzenia](../../cognitive-services/Content-Moderator/video-moderation-api.md). 

Dowiedz się, jak Generowanie [przegląda wideo](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) z danych wyjściowych moderowane, i [średnie zapisów](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) w .NET.

Zapoznaj się z szczegółowe .NET [łagodzenia i przejrzyj samouczek](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md). 
