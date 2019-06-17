---
title: Umożliwia wykrywanie możliwej zawartości dla dorosłych usługi Azure Media Content Moderator | Dokumentacja firmy Microsoft
description: Moderowanie filmów wideo ułatwia wykrywanie potencjalnych dorosłych i zawartości erotycznej w klipach wideo.
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
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: eb16f5e1e72e5a9379ad530ab9677adba2ccbbcd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61465681"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Użyj usługi Azure Media Content Moderator, aby wykrywanie możliwej zawartości dla dorosłych 

## <a name="overview"></a>Omówienie
**Pakietu Content Moderator multimediów Azure** procesor multimediów (MP) pozwala na użytek wspomagane maszynowo Moderowanie filmów wideo. Na przykład chcesz wykrywać potencjalną zawartość dla dorosłych i nieodpowiednią w filmach wideo oraz przesyłać zawartość oznaczoną flagą do przejrzenia przez zespoły ds. moderowania obsługiwanego przez ludzi.

**Pakietu Content Moderator multimediów Azure** pakiet administracyjny jest obecnie w wersji zapoznawczej.

Ten artykuł zawiera szczegółowe informacje o **pakietu Content Moderator multimediów Azure** i pokazuje, jak z niej korzystać z zestawu SDK usługi Media Services dla platformy .NET.

## <a name="content-moderator-input-files"></a>Content Moderator plików wejściowych
Pliki wideo. Obecnie obsługiwane są następujące formaty: MP4, MOV i WMV.

## <a name="content-moderator-output-files"></a>Zawartość plików wyjściowych moderatora
Moderowane danych wyjściowych w formacie JSON zawiera zrzuty automatycznie wykryte i klatki kluczowe. Klatki kluczowe są zwracane z pewnością wyniki dla możliwej zawartości dla dorosłych lub erotycznej. Te aktualizacje obejmują również flagę logiczną wskazującą, czy przeglądu jest zalecane. Flaga zalecenie przeglądu przypisano wartości w oparciu o progi wewnętrzne wyników dla dorosłych.

## <a name="elements-of-the-output-json-file"></a>Elementy danych wyjściowych pliku JSON

Zadanie tworzy plik danych wyjściowych JSON, który zawiera metadane dotyczące wykrytych zrzuty i klatki kluczowe oraz tego, czy zawierają one zawartości dla dorosłych lub erotycznej.

Dane wyjściowe JSON zawiera następujące elementy:

### <a name="root-json-elements"></a>Elementy katalogu głównego JSON

| Element | Opis |
| --- | --- |
| version |Wersja pakietu Content Moderator. |
| Skala czasu |"Impulsów" na sekundę filmu wideo. |
| offset |Przesunięcie czasu dla sygnatur czasowych. W wersji 1.0 interfejsów API Video ta wartość będzie zawsze równa 0. Ta wartość może zmienić w przyszłości. |
| szybkość klatek |Liczba klatek na sekundę w wideo. |
| Szerokość |Szerokość danych wyjściowych klatki wideo, w pikselach.|
| Wysokość |Wysokość dane wyjściowe klatki wideo, w pikselach.|
| TotalDuration |Czas trwania wideo w "taktów." danych wejściowych |
| [fragments](#fragments-json-elements) |Metadane jest podzielony się w różnych segmentach fragmentów. Każdy fragment jest automatycznie wykryte zrzut rozpoczęcia, czas trwania, liczba interwałów i zdarzenia. |

### <a name="fragments-json-elements"></a>Elementy JSON fragmentów

|Element|Opis|
|---|---|
| start |Czas rozpoczęcia pierwsze zdarzenie "taktów." |
| Czas trwania |Długość fragmentu, w "taktów." |
| interval |Interwał każdego wpisu zdarzenia w obrębie fragmentu, w "taktów." |
| [Zdarzenia](#events-json-elements) |Każde zdarzenie reprezentuje klipu i każdy plik zawiera klatki kluczowe wykryte i śledzenia w tym czas trwania. Istnieje szereg zdarzeń. Zewnętrzna tablica reprezentuje jeden interwał czasu. Wewnętrzna tablica składa się z 0 lub większej liczby zdarzeń, które wystąpiły w danym momencie.|

### <a name="events-json-elements"></a>Elementy JSON zdarzenia

|Element|Opis|
|---|---|
| reviewRecommended | `true` lub `false` zależności od tego, czy **adultScore** lub **racyScore** przekroczy progi wewnętrznego. |
| adultScore | Współczynnik ufności dla możliwe zawartości dla dorosłych w skali od 0,00 0,99. |
| racyScore | Współczynnik ufności możliwe zawartości erotycznej, na skalę 0,00 do 0,99. |
| index | Indeks ramki w skali od pierwszej ramki do ostatniego indeks indeksu ramki. |
| timestamp | Lokalizacja ramki "taktów." |
| shotIndex | Zrzut indeks elementu nadrzędnego. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Zawartość Moderowanie przewodnika Szybki Start i przykładowe dane wyjściowe

### <a name="task-configuration-preset"></a>Konfiguracja zadania (ustawienie wstępne)
Podczas tworzenia zadania za pomocą **pakietu Content Moderator multimediów Azure**, należy określić ustawienie wstępne konfiguracji. Następujące ustawienie konfiguracji jest tylko do moderacji zawartości.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>Przykładowy kod platformy .NET

Poniższy przykładowy kod .NET używa zestawu SDK .NET usługi Media Services do uruchomienia zadania pakietu Content Moderator. Pobiera nośników usług zasobów jako danych wejściowych, które zawiera film wideo, aby się łagodzenie.
Zobacz [wideo Szybki Start pakietu Content Moderator](../../cognitive-services/Content-Moderator/video-moderation-api.md) pełny kod źródłowy i projekt programu Visual Studio.


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
[Przegląd Analityki usługi Azure Media Services](media-services-analytics-overview.md)

[Pokazy usługi Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat pakietu Content Moderator [rozwiązania do obsługi wideo Moderowanie i przejrzyj](../../cognitive-services/Content-Moderator/video-moderation-human-review.md).

Uzyskaj pełny kod źródłowy i projekt programu Visual Studio z [Moderowanie filmów wideo przewodnika Szybki Start](../../cognitive-services/Content-Moderator/video-moderation-api.md). 

Dowiedz się, jak wygenerować [przeglądy wideo](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) z moderowanych dane wyjściowe, i [średni zapisy](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) na platformie .NET.

Zapoznaj się z szczegółowe .NET [Samouczek wideo Moderowanie i przejrzyj](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md). 
