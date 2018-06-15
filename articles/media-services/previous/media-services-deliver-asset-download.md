---
title: Pobrać na komputer - Azure Media Services zasoby | Dokumentacja firmy Microsoft
description: Dowiedz się, pobierania zasoby do komputera. Przykłady kodu są napisane w języku C# i używają SDK usługi Media Services dla platformy .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 8908a1dd-3ffb-4f18-955d-4c8e2d82fc5d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: ed53fe191dcf740f949b2d9cdcc3c97e30d85544
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788228"
---
# <a name="how-to-deliver-an-asset-by-download"></a>Porady: dostarczanie zasobów do pobrania
W tym artykule opisano opcje dostarczania zasobów nośnika przekazane do usługi Media Services. W wielu scenariuszach aplikacji można dostarczać zawartości Media Services. Po kodowania, Pobierz zasoby wygenerowanego media lub uzyskiwać do nich dostęp za pomocą Lokalizator przesyłania strumieniowego. Aby uzyskać lepszą wydajność i skalowalność może również udostępniać zawartość za pomocą sieci dostarczania zawartości (CDN).

Ten przykład przedstawia sposób pobierania zasoby nośnika z usługi Media Services na komputerze lokalnym. Kod zapytania zadania skojarzone z kontem usługi Media Services według Identyfikatora zadania i uzyskuje dostęp do jego **OutputMediaAssets** kolekcji (czyli zestaw jeden lub więcej zasobów nośnika danych wyjściowych, który powoduje uruchomienie zadania). W tym przykładzie pokazano, jak pobrać zasoby nośnika danych wyjściowych z zadania, ale można zastosować te same podejście, aby pobrać inne zasoby.

>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Użyj tego samego Identyfikatora zasad, jeśli zawsze korzystają z tej samej dni / dostęp uprawnień, na przykład zasady dla lokalizatorów, które powinny pozostać w miejscu przez długi czas (— przekazywanie zasady). Więcej informacji znajduje się w [tym](media-services-dotnet-manage-entities.md#limit-access-policies) artykule.

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
[Dostarczania transmisji strumieniowej zawartości](media-services-deliver-streaming-content.md)

