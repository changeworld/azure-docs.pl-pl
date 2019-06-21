---
title: Klipu podrzędnego wideo podczas kodowania za pomocą usługi Azure Media Services
description: W tym temacie opisano sposób klipu podrzędnego wideo podczas kodowania za pomocą usługi Azure Media Services przy użyciu zestawu .NET SDK
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/09/2019
ms.author: juliako
ms.openlocfilehash: 3d584ee742aa93cdecf4b04d942afb2ed83a7357
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305104"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Klipu podrzędnego wideo podczas kodowania za pomocą usługi Media Services — .NET

Można przycięcia lub klipu podrzędnego wideo podczas kodowania za pomocą [zadania](https://docs.microsoft.com/rest/api/media/jobs). Ta funkcja działa ze wszystkimi [Przekształcanie](https://docs.microsoft.com/rest/api/media/transforms) utworzonego za pomocą [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ustawienia wstępne, lub [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) ustawienia wstępne.

Następujące C# przykład tworzy zadanie, które przycina wideo w zasobie, ponieważ przesyła zadania kodowania. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym temacie, musisz:

- [Tworzenie konta usługi Azure Media Services](create-account-cli-how-to.md)
- Tworzenie transformacji i dane wejściowe i wyjściowe zasoby. Możesz dowiedzieć się, jak utworzyć transformacji i zasoby wejściowe i wyjściowe [przekazywanie, kodowanie i przesyłanie strumieniowe filmów wideo przy użyciu platformy .NET](stream-files-tutorial-with-api.md) samouczka.
- Przegląd [kodowaniem pojęciem](encoding-concept.md) tematu.

## <a name="example"></a>Przykład

```csharp
/// <summary>
/// Submits a request to Media Services to apply the specified Transform to a given input video.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="transformName">The name of the transform.</param>
/// <param name="jobName">The (unique) name of the job.</param>
/// <param name="inputAssetName">The name of the input asset.</param>
/// <param name="outputAssetName">The (unique) name of the  output asset that will store the result of the encoding job. </param>
// <SubmitJob>
private static async Task<Job> JobWithBuiltInStandardEncoderWithSingleClipAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName,
    string inputAssetName,
    string outputAssetName)
{
    var jobOutputs = new List<JobOutputAsset>
    {
        new JobOutputAsset(state: JobState.Queued, progress: 0, assetName: outputAssetName)
    };

    var clipStart = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 20)
    };

    var clipEnd = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 30)
    };

    var jobInput = new JobInputAsset(assetName: inputAssetName, start: clipStart, end: clipEnd);

    Job job = await client.Jobs.CreateAsync(
        resourceGroupName,
        accountName,
        transformName,
        jobName,
        new Job(input: jobInput, outputs: jobOutputs.ToArray(), name: jobName)
        {
            Description = $"A Job with transform {transformName} and single clip.",
            Priority = Priority.Normal,
        });

    return job;
}
```

## <a name="next-steps"></a>Kolejne kroki

[Jak kodowanie za pomocą niestandardowej transformacji](customize-encoder-presets-how-to.md) 
