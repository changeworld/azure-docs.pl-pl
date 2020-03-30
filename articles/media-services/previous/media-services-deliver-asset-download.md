---
title: Pobieranie zasobów usługi Media Services na komputer — platforma Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o pobieraniu zasobów na komputer. Przykłady kodu są zapisywane w języku C# i używają zestawu SDK usług multimedialnych dla platformy .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 8908a1dd-3ffb-4f18-955d-4c8e2d82fc5d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 21fcc6ae09718ffbb22e1d438926586dd3cde71d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61465664"
---
# <a name="how-to-deliver-an-asset-by-download"></a>Jak: Dostarczanie zasobu przez pobranie  
W tym artykule omówiono opcje dostarczania zasobów multimedialnych przekazanych do usługi Media Services. Zawartość usługi Media Services można dostarczać w wielu scenariuszach aplikacji. Po zakodowaniu pobierz wygenerowane zasoby multimedialne lub uzyskaj do nich dostęp za pomocą lokalizatora przesyłania strumieniowego. Aby zwiększyć wydajność i skalowalność, można również dostarczać zawartość przy użyciu sieci dostarczania zawartości (CDN).

W tym przykładzie pokazano, jak pobrać zasoby multimedialne z usługi Media Services na komputer lokalny. Kod wysyła kwerendy do zadań skojarzonych z kontem usługi Media Services według identyfikatora zadania i uzyskuje dostęp do jego kolekcji **OutputMediaAssets** (która jest zestawem jednego lub więcej wyjściowych zasobów multimedialnych, które wynikają z uruchomienia zadania). W tym przykładzie pokazano, jak pobrać wyjściowe zasoby multimedialne z zadania, ale można zastosować to samo podejście do pobierania innych zasobów.

>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Użyj tego samego identyfikatora zasad, jeśli zawsze używasz tych samych dni / uprawnień dostępu, na przykład zasad dla lokalizatorów, które mają pozostać w miejscu przez długi czas (zasady nieprześledania). Więcej informacji znajduje się w [tym](media-services-dotnet-manage-entities.md#limit-access-policies) artykule.

```csharp
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 

        // Get a reference to the job. 
        IJob job = GetJob(jobId);

        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];

        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);

        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };

        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;

            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

            Console.WriteLine("File download path:  " + localDownloadPath);

            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

            outputFile.DownloadProgressChanged -= DownloadProgress;
        }

        Task.WaitAll(downloadTasks.ToArray());

        return outputAsset;
    }

    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }
```


## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Dostarczanie zawartości strumieniowej](media-services-deliver-streaming-content.md)

