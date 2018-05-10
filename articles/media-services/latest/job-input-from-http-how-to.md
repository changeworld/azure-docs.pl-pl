---
title: Utwórz dane wejściowe Azure Media Services zadania na podstawie adresu URL HTTP (s) | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono sposób tworzenia wprowadzania zadania z adresu URL HTTP (s).
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: a47dc3e4939dd27d7ac11be823a09b8a6cba1f60
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="create-a-job-input-from-an-https-url"></a>Utwórz wprowadzania zadania na podstawie adresu URL HTTP (s)

W Media Services w wersji 3 po przesłaniu zadania, aby przetwarzać pliki wideo, należy sprawdzić, gdzie można znaleźć wejściowy plik wideo usługi Media Services. Jest jedną z opcji, aby określić adres URL HTTP (s) jako danych wejściowych (jak pokazano w tym przykładzie) zadanie. Na przykład pełna, zobacz [próbki github](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Przykładowe .net

Poniższy kod przedstawia sposób tworzenia zadania z adresu URL HTTPS danych wejściowych.

```csharp
private static Job SubmitJob(IAzureMediaServicesClient client, string transformName, string jobName)
{
    Asset outputAsset = client.Assets.CreateOrUpdate(Guid.NewGuid().ToString() + "-output", new Asset());

    JobInputHttp jobInput = 
        new JobInputHttp(files: new[] { "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4" });

    JobOutput[] jobOutputs =
    {
        new JobOutputAsset(outputAsset.Name),
    };

    Job job = client.Jobs.CreateOrUpdate(
        transformName,
        jobName,
        new Job
        {
            Input = jobInput,
            Outputs = jobOutputs,
        });

    return job;
}
```

## <a name="next-steps"></a>Kolejne kroki

[Utwórz wprowadzania zadania z pliku lokalnego](job-input-from-local-file-how-to.md).
