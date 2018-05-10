---
title: Plikach multimedialnych przyspieszonych ujęć poklatkowych za pomocą usługi Azure Media Hyperlapse | Dokumentacja firmy Microsoft
description: Azure Media Hyperlapse tworzy smooth czas, jaki upłynął wideo z pierwszą osobą lub akcji aparatu zawartości. W tym temacie pokazano, jak używać nośnika indeksatora.
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
ms.openlocfilehash: ed64a616538ed4699abc03225a2dcf27d164521f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="hyperlapse-media-files-with-azure-media-hyperlapse"></a>Plikach multimedialnych przyspieszonych ujęć poklatkowych za pomocą usługi Azure Media Hyperlapse
Azure Media Hyperlapse jest nośnik procesora (MP) tworzącą smooth czas, jaki upłynął wideo z pierwszą osobą lub akcji aparatu zawartości.  Element równorzędny oparte na chmurze do [desktop Pro przyspieszonych ujęć poklatkowych i opartych na telefon komórkowy przyspieszonych ujęć poklatkowych Microsoft Research](http://aka.ms/hyperlapse), Hyperlapse firmy Microsoft dla usługi Azure Media Services używa bardzo dużej skali przetwarzania Media Services multimediów Azure Platforma na poziomie skalowanie i parallelize zbiorcze przyspieszonych ujęć poklatkowych przetwarzania.

> [!IMPORTANT]
> Microsoft Hyperlapse jest zaprojektowana by najlepiej pracować na pierwszą osobą zawartości za pomocą przenoszenia aparatu. Mimo że nadal kamery może nadal działać, wydajności i jakości procesor multimediów Azure Media przyspieszonych ujęć poklatkowych nie można zagwarantować dla innych typów zawartości.
> 
> 

Azure Media Hyperlapse zadania przyjmuje jako plik wejściowy elementu MP4, MOV lub WMV zasobów wraz z plikiem konfiguracji, który określa, które ramki wideo powinna być czas, jaki upłynął i jakie szybkości (np. pierwszych 10 000 ramek, x 2).  Dane wyjściowe są stabilnych i czas, jaki upłynął dobór wejściowego pliku wideo.

## <a name="hyperlapse-an-asset"></a>Przyspieszonych ujęć poklatkowych zasobów
Najpierw należy przesłać żądany plik wejściowy do usługi Azure Media Services.  Aby dowiedzieć się więcej na temat pojęć związanych z przekazywanie i zarządzania zawartością, przeczytaj [zarządzania zawartością artykułu](media-services-portal-vod-get-started.md).

### <a id="configuration"></a>Ustawienie konfiguracji dla przyspieszonych ujęć poklatkowych
Gdy zawartość jest na koncie usługi Media Services, należy utworzyć ustawienia konfiguracji.  W poniższej tabeli opisano pola określone przez użytkownika:

| Pole | Opis |
| --- | --- |
| StartFrame |Ramka, na którym powinny one zacząć przetwarzania Hyperlapse firmy Microsoft. |
| NumFrames |Liczba ramek do przetworzenia |
| Szybkość |Współczynnik, z którą ma zostać przyspieszenia wejściowy plik wideo. |

Poniżej przedstawiono przykładowy plik konfiguracji zgodność w pliku XML i JSON:

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

### <a id="sample_code"></a> Microsoft przyspieszonych ujęć poklatkowych przy użyciu zestawu SDK .NET usługi AMS
Następująca metoda przekazuje plik nośnika jako zasób i tworzy zadanie z procesora Azure Media przyspieszonych ujęć poklatkowych nośnika.

> [!NOTE]
> CloudMediaContext ma już w zakresie o nazwie "context" dla tego kodu do pracy.  Aby dowiedzieć się więcej na ten temat, przeczytaj [zarządzania zawartością artykułu](media-services-dotnet-get-started.md).
> 
> [!NOTE]
> Argument ciągu "hyperConfig" powinien być konfiguracji zgodność ustawień w formacie JSON i XML, zgodnie z powyższym opisem.
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
[Przegląd analiz usługi Azure Media Services](media-services-analytics-overview.md)

[W trakcie analizy multimediów Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

