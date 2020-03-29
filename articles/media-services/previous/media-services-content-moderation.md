---
title: Użyj narzędzia Azure Media Content Moderator do wykrywania możliwych treści dla dorosłych i rasistowskich | Dokumenty firmy Microsoft
description: Procesor multimediów programu Azure Media Content Moderator pomaga wykrywać potencjalne treści dla dorosłych i treści w filmach.
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
ms.openlocfilehash: 83fe7867a3128ac82597c028452863a1ad681ace
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914334"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Użyj narzędzia Azure Media Content Moderator do wykrywania możliwych treści dla dorosłych i rasistowskich 

> [!NOTE]
> Procesor multimediów **programu Azure Media Content Moderator** zostanie wycofany. W przypadku daty wycofania zobacz temat [starszych składników.](legacy-components.md)

## <a name="overview"></a>Omówienie
Procesor **multimediów programu Azure Media Content Moderator** (MP) umożliwia korzystanie z moderacji wspomaganej maszynowo dla filmów. Na przykład chcesz wykrywać potencjalną zawartość dla dorosłych i nieodpowiednią w filmach wideo oraz przesyłać zawartość oznaczoną flagą do przejrzenia przez zespoły ds. moderowania obsługiwanego przez ludzi.

**Pakiet mp programu Azure Media Content Moderator** jest obecnie w wersji zapoznawczej.

Ten artykuł zawiera szczegółowe informacje na temat **narzędzia Azure Media Content Moderator** i pokazano, jak go używać z zestawem SDK usługi Media Services dla platformy .NET.

## <a name="content-moderator-input-files"></a>Pliki wejściowe moderatora zawartości
Pliki wideo. Obecnie obsługiwane są następujące formaty: MP4, MOV i WMV.

## <a name="content-moderator-output-files"></a>Pliki wyjściowe moderatora zawartości
Moderowane dane wyjściowe w formacie JSON obejmują automatycznie wykrywane zdjęcia i klatki kluczowe. Klatki kluczowe są zwracane z wynikami zaufania dla możliwych treści dla dorosłych lub rasistowskich. Zawierają one również flagę logiczną wskazującą, czy zaleca się przegląd. Flaga rekomendacji przeglądu jest przypisywana wartości na podstawie wewnętrznych progów dla dorosłych i racy wyniki.

## <a name="elements-of-the-output-json-file"></a>Elementy wyjściowego pliku JSON

Zadanie tworzy plik wyjściowy JSON, który zawiera metadane dotyczące wykrytych zdjęć i klatek kluczowych oraz tego, czy zawierają one zawartość dla dorosłych, czy rasistowskie.

Wyjście JSON zawiera następujące elementy:

### <a name="root-json-elements"></a>Główne elementy JSON

| Element | Opis |
| --- | --- |
| version |Wersja Moderatora zawartości. |
| skala czasu |"Kleszcze" na sekundę filmu. |
| przesunięcie |Przesunięcie czasu dla sygnatur czasowych. W wersji 1.0 interfejsów API wideo ta wartość będzie zawsze 0. Ta wartość może ulec zmianie w przyszłości. |
| Ilość klatek |Liczba klatek na sekundę w wideo. |
| szerokość |Szerokość wyjściowej klatki wideo w pikselach.|
| height |Wysokość wyjściowej klatki wideo w pikselach.|
| całkowitaducja |Czas trwania wejściowego wideo w "kleszczach". |
| [Fragmenty](#fragments-json-elements) |Metadane są podzielone na różne segmenty zwane fragmentami. Każdy fragment jest automatycznie wykrytym strzałem z uruchomiem, czasem trwania, numerem interwału i zdarzeniami. |

### <a name="fragments-json-elements"></a>Fragmenty elementów JSON

|Element|Opis|
|---|---|
| rozpoczynanie |Godzina rozpoczęcia pierwszego zdarzenia w "kleszczach". |
| czas trwania |Długość fragmentu w "kleszczach". |
| interval |Interwał każdego wpisu zdarzenia w obrębie fragmentu w "kleszcze". |
| [Zdarzenia](#events-json-elements) |Każde zdarzenie reprezentuje klip, a każdy klip zawiera klatki kluczowe wykryte i śledzone w tym czasie. Jest to tablica zdarzeń. Zewnętrzna tablica reprezentuje jeden interwał czasu. Wewnętrzna tablica składa się z 0 lub większej liczby zdarzeń, które wystąpiły w danym momencie.|

### <a name="events-json-elements"></a>Wydarzenia JSON elementy

|Element|Opis|
|---|---|
| przeglądZaznane | `true`lub `false` w zależności od tego, czy **adultScore** lub **racyScore** przekraczają wewnętrzne progi. |
| adultScore (dorosłyScore) | Wskaźnik zaufania dla możliwych treści dla dorosłych w skali od 0,00 do 0,99. |
| racyScore (racyScore) | Wskaźnik zaufania dla możliwych treści rasistowskich, w skali od 0,00 do 0,99. |
| indeks | indeks ramki w skali od indeksu pierwszej klatki do ostatniego indeksu klatki. |
| sygnatura czasowa | Położenie ramki w "kleszczach". |
| shotIndex (włastrzemię) | Indeks strzału nadrzędnego. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Szybki start moderowania zawartości i przykładowe dane wyjściowe

### <a name="task-configuration-preset"></a>Konfiguracja zadania (ustawienie wstępne)
Podczas tworzenia zadania za pomocą **narzędzia Azure Media Content Moderator**należy określić predefiniowane ustawienia konfiguracji. Następujące ustawienie konfiguracji jest tylko dla moderowania zawartości.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>Przykład kodu .NET

Poniższy przykład kodu .NET używa narzędzia Media Services .NET SDK do uruchamiania zadania moderatora zawartości. Zasób usług multimedialnych przyjmuje jako dane wejściowe zawierające wideo, które ma być moderowane.
Zobacz [szybki start przewodnika moderatora zawartości,](../../cognitive-services/Content-Moderator/video-moderation-api.md) aby uzyskać pełny kod źródłowy i projekt programu Visual Studio.


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
[Omówienie usługi Azure Media Services Analytics](media-services-analytics-overview.md)

[Pokazy usługi Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o rozwiązaniu moderatora treści do [moderowania i recenzji.](../../cognitive-services/Content-Moderator/video-moderation-human-review.md)

Pobierz pełny kod źródłowy i projekt programu Visual Studio z [szybkiego startu moderowania wideo](../../cognitive-services/Content-Moderator/video-moderation-api.md). 

Dowiedz się, jak generować [recenzje wideo](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) z moderowane dane wyjściowe i [umiarkowane transkrypcje](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) w .NET.

Sprawdź szczegółowy moderacja wideo .NET [i przejrzyj samouczek](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md). 
