---
title: Hyperlapse Media pliki z usługi Azure Media Hyperlapse | Dokumentacja firmy Microsoft
description: Usługa Azure Media Hyperlapse tworzy smooth czasu uzyskanie płynnych wideo poklatkowych na podstawie zawartości pierwszą osobą, która lub kamery akcji. W tym temacie pokazano, jak używać Media Indexer.
services: media-services
documentationcenter: ''
author: asolanki
manager: johndeu
editor: ''
ms.assetid: 37d54db6-9cf3-4ae9-b3c6-0d29c744e965
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2018
ms.author: adsolank
ms.openlocfilehash: 3b361202d2d80f879ae6d23ec29782bc6f837f96
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632878"
---
# <a name="hyperlapse-media-files-with-azure-media-hyperlapse"></a>Hyperlapse Media pliki z usługi Azure Media Hyperlapse

> [!NOTE]
> Tę funkcję wersji zapoznawczej usługi Azure Media Services zostanie wkrótce wycofany. Od 19 grudnia 2018 r. usługi Media Services nie jest już wprowadzi zmiany i udoskonalenia Media Hyperlapse. Na 29 marca 2019 r będzie wycofane i nie będą już dostępne.

Usługa Azure Media Hyperlapse jest nośnik procesora (MP) który tworzy smooth czasu uzyskanie płynnych wideo poklatkowych na podstawie zawartości pierwszą osobą, która lub kamery akcji.  Element równorzędny oparte na chmurze, aby [pulpitu Hyperlapse Pro Microsoft Research i telefoniczną Hyperlapse Mobile](https://aka.ms/hyperlapse), Microsoft Hyperlapse dla usługi Azure Media Services korzysta z ogromnej skali przetwarzania multimediów usługi multimediów Azure platform na poziomie skalowanie i wykonywanie równoległe zbiorczo Hyperlapse przetwarzania.

> [!IMPORTANT]
> Microsoft Hyperlapse jest zaprojektowana by najlepiej pracować na pierwszą osobą, która zawartości za pomocą aparatu przenoszenia. Mimo że nadal aparatu nagrań z monitorowania może nadal działać, wydajności i jakości procesor multimediów Azure Media Hyperlapse nie można zagwarantować dla innych typów zawartości.
> 
> 

Azure Media Hyperlapse zadania przyjmuje jako MP4, MOV lub WMV trwały plik wejściowy wraz z plikiem konfiguracji, który określa, które klatki filmu wideo powinien być czas, jaki upłynął i jakie szybkości (np. pierwszych 10 000 ramek na 2 x).  Dane wyjściowe są stabilizowanych i czas, jaki upłynął dobór wejściowego pliku wideo.

## <a name="hyperlapse-an-asset"></a>Przyspieszone ujęcia Poklatkowe zasobu
Najpierw należy przekazać żądany plik wejściowy do usługi Azure Media Services.  Aby dowiedzieć się więcej na temat pojęć związanych z przekazywania i zarządzania zawartością, przeczytaj [zarządzania zawartością artykułu](media-services-portal-vod-get-started.md).

### <a id="configuration"></a>Ustawienie konfiguracji dla usługi przyspieszone ujęcia Poklatkowe
Po zawartości w ramach konta usługi Media Services, należy utworzyć ustawienia konfiguracji.  W poniższej tabeli opisano pola określone przez użytkownika:

| Pole | Opis |
| --- | --- |
| StartFrame |Ramka, na którym ma się zacząć przetwarzania Microsoft Hyperlapse. |
| NumFrames |Liczba ramek do przetworzenia |
| Szybkość |Czynnik przyspieszenia wejściowego klipu wideo. |

Oto przykład zgodność pliku konfiguracji XML i JSON:

**Ustawienie wstępne XML:**
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
        <Sources>
            <Source StartFrame="0" NumFrames="10000" />
        </Sources>
        <Options>
            <Speed>12</Speed>
        </Options>
    </Preset>
```

**Ustawienie wstępne JSON:**
```json
    {
        "Version":1.0,
        "Sources": [
            {
                "StartFrame":0,
                "NumFrames":2147483647
            }
        ],
        "Options": {
            "Speed":1,
            "Stabilize":false
        }
    }
```

### <a id="sample_code"></a> Technologia Microsoft Hyperlapse przy użyciu zestawu SDK .NET usługi AMS
Poniższa metoda przekazuje plik multimedialny jako zasób usługi i tworzy zadanie z procesorem multimediów Azure Media Hyperlapse.

> [!NOTE]
> CloudMediaContext powinno mieć już w zakresie o nazwie "kontekst" dla tego kodu do pracy.  Aby dowiedzieć się więcej na ten temat, przeczytaj [zarządzania zawartością artykułu](media-services-dotnet-get-started.md).
> 
> [!NOTE]
> Argument ciągu "hyperConfig" powinien traktować jako konfigurację zgodność wstępnie ustawione w formacie JSON lub XML, zgodnie z powyższym opisem.
> 
> 

```csharp
        static bool RunHyperlapseJob(string input, string output, string hyperConfig)
        {
            // create asset with input file
            IAsset asset = context
            .Assets
            .CreateAssetAndUploadSingleFile(input, "My Hyperlapse Input", AssetCreationOptions.None);

            // grab instances of Azure Media Hyperlapse MP
            IMediaProcessor mp = context
            .MediaProcessors
            .GetLatestMediaProcessorByName("Azure Media Hyperlapse");

            // create Job with Hyperlapse task
            IJob job = context
            .Jobs
            .Create(String.Format("Hyperlapse {0}", input));

            if (String.IsNullOrEmpty(hyperConfig))
            {
            // config cannot be empty
            return false;
            }

            hyperConfig = File.ReadAllText(hyperConfig);

            ITask hyperlapseTask = job.Tasks.AddNew("Hyperlapse task",
            mp,
            hyperConfig,
            TaskOptions.None);
            hyperlapseTask.InputAssets.Add(asset);
            hyperlapseTask.OutputAssets.AddNew("Hyperlapse output",
            AssetCreationOptions.None);

            job.Submit();

            // Create progress printing and querying tasks
            Task progressPrintTask = new Task(() =>
            {

            IJob jobQuery = null;
            do
            {
                var progressContext = context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}\t{2}",
                DateTime.Now,
                jobQuery.State,
                jobQuery.Tasks[0].Progress));
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
                return false;                  
            }

        DownloadAsset(job.OutputMediaAssets.First(), output);
        return true;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }


    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }
```

### <a id="file_types"></a>Obsługiwane typy plików
* MP4
* MOV
* WMV

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Powiązane linki
[Przegląd Analityki usługi Azure Media Services](media-services-analytics-overview.md)

[Pokazy usługi Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

