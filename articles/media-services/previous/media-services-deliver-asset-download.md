---
title: Pobierz zasoby usługi Media Services na komputerze — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się o pobrać zasoby na komputer. Przykłady kodu są napisane C# i użyj Media Services SDK dla platformy .NET.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61465664"
---
# <a name="how-to-deliver-an-asset-by-download"></a>Instrukcje: Dostarczanie zasobu przez pobieranie  
W tym artykule omówiono opcje dostarczania zasobów multimedialnych przekazany do usługi Media Services. Umożliwia dostarczanie zawartości Media Services, w wielu scenariuszach aplikacji. Po zakodowaniu, pobrać zasobów multimedialnych wygenerowany lub uzyskać do nich dostęp za pomocą Lokalizator przesyłania strumieniowego. Aby uzyskać lepszą wydajność i skalowalność umożliwia dostarczanie zawartości przy użyciu sieci dostarczania zawartości (CDN).

W tym przykładzie pokazano, jak można pobrać zasobów multimedialnych z usługi Media Services na komputerze lokalnym. Kod zapytania zadania skojarzonego z kontem usługi Media Services, identyfikator zadania i uzyskuje dostęp do jego **OutputMediaAssets** kolekcję (która jest zestawem jeden lub więcej zasobów multimedialnych danych wyjściowych, wynikające z uruchamiania zadania). Ten przykład pokazuje, jak można pobrać zasobów multimedialnych dane wyjściowe z zadania, ale można zastosować to samo podejście można pobrać inne zasoby.

>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Użyj tego samego Identyfikatora zasad, jeśli zawsze używasz tych samych dni / dostęp do uprawnień, na przykład zasad lokalizatorów, które powinny pozostać w miejscu przez długi czas (nieprzekazywane zasady). Więcej informacji znajduje się w [tym](media-services-dotnet-manage-entities.md#limit-access-policies) artykule.

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
[Dostarczanie zawartości przesyłanej strumieniowo](media-services-deliver-streaming-content.md)

