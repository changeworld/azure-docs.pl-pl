---
title: Publikowanie zawartości usługi Azure Media Services przy użyciu platformy .NET | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć lokalizator używany do tworzenia adresu URL przesyłania strumieniowego. Przykłady kodu są zapisywane w języku C# i używają zestawu SDK usług multimedialnych dla platformy .NET.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: c53b1f83-4cb1-4b09-840f-9c145b7d6f8d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 615a6afb0f7a3e133603db10e7c79add3322070c
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476696"
---
# <a name="publish-media-services-content-using-net"></a>Publikowanie zawartości usługi Media Services przy użyciu platformy .NET  
> [!div class="op_single_selector"]
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>Omówienie
Można przesyłać strumieniowo adaptacyjny zestaw MP4 szybkości transmisji bitów, tworząc lokalizator przesyłania strumieniowego OnDemand i tworząc adres URL przesyłania strumieniowego. W temacie [kodowania zasobu](media-services-encode-asset.md) pokazano, jak zakodować do adaptacyjnego zestawu MP4 z szybkością transmisji bitów. 

> [!NOTE]
> Jeśli zawartość jest szyfrowana, przed utworzeniem lokalizatora skonfiguruj zasady dostarczania zasobów (zgodnie z [opisem](media-services-dotnet-configure-asset-delivery-policy.md) w tym temacie). 
> 
> 

Lokalizatora przesyłania strumieniowego OnDemand można również użyć do tworzenia adresów URL wskazujących pliki MP4, które można pobierać stopniowo.  

W tym temacie pokazano, jak utworzyć lokalizator przesyłania strumieniowego OnDemand, aby opublikować zasób i utworzyć adresy URL przesyłania strumieniowego Smooth, MPEG DASH i HLS. Pokazuje również gorąco budować progresywne adresy URL pobierania. 

## <a name="create-an-ondemand-streaming-locator"></a>Tworzenie lokalizatora strumieniowego OnDemand
Aby utworzyć lokalizator przesyłania strumieniowego OnDemand i uzyskać adresy URL, musisz wykonać następujące czynności:

1. Jeśli zawartość jest szyfrowana, zdefiniuj zasady dostępu.
2. Utwórz lokalizator przesyłania strumieniowego OnDemand.
3. Jeśli planujesz przesyłanie strumieniowe, pobierz plik manifestu przesyłania strumieniowego (.ism) w zasobie. 
   
   Jeśli planujesz stopniowe pobieranie, pobierz nazwy plików MP4 w zasobie.  
4. Tworzenie adresów URL do plików manifestu lub plików MP4. 


>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Użyj tego samego identyfikatora zasad, jeśli zawsze używasz tych samych dni / uprawnień dostępu. Na przykład zasady dla lokalizatorów, które mają pozostać w miejscu przez długi czas (zasady nieprzegraniania). Aby uzyskać więcej informacji, zobacz [ten](media-services-dotnet-manage-entities.md#limit-access-policies) temat.

### <a name="use-media-services-net-sdk"></a>Korzystanie z sdk usługi Media Services .NET
Tworzenie adresów URL przesyłania strumieniowego 

```csharp
    private static void BuildStreamingURLs(IAsset asset)
    {

        // Create a 30-day readonly access policy. 
          // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.ToList().Where(f => f.Name.ToLower().
                                    EndsWith(".ism")).
                                    FirstOrDefault();

        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
        Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
        Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
        Console.WriteLine();
    }
```

Wyjścia:

    URL to manifest for client streaming using Smooth Streaming protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
    URL to manifest for client streaming using HLS protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
    URL to manifest for client streaming using MPEG DASH protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


> [!NOTE]
> Można również przesyłać strumieniowo zawartość za pomocą połączenia TLS. Aby to zrobić, upewnij się, że adresy URL przesyłania strumieniowego zaczynają się od protokołu HTTPS. Obecnie usługa AMS nie obsługuje protokołu TLS z domenami niestandardowymi.
> 
> 

Tworzenie progresywnych adresów URL pobierania 

```csharp
    private static void BuildProgressiveDownloadURLs(IAsset asset)
    {
        // Create a 30-day readonly access policy. 
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create an OnDemandOrigin locator to the asset. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get MP4 files.
        IEnumerable<IAssetFile> mp4AssetFiles = asset
            .AssetFiles
            .ToList()
            .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Create a full URL to the MP4 files. Use this to progressively download files.
        foreach (var pd in mp4AssetFiles)
            Console.WriteLine(originLocator.Path + pd.Name);
    }
```
Wyjścia:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4

    . . . 

### <a name="use-media-services-net-sdk-extensions"></a>Korzystanie z rozszerzeń SDK usługi Media Services .NET
Poniższy kod wywołuje metody rozszerzeń .NET SDK, które tworzą lokalizator i generują adresy URL Smooth Streaming, HLS i MPEG-DASH w celu adaptacyjnego przesyłania strumieniowego.
```csharp
    // Create a loctor.
    _context.Locators.Create(
        LocatorType.OnDemandOrigin,
        inputAsset,
        AccessPermissions.Read,
        TimeSpan.FromDays(30));

    // Get the streaming URLs.
    Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
    Uri hlsUri = inputAsset.GetHlsUri();
    Uri mpegDashUri = inputAsset.GetMpegDashUri();

    Console.WriteLine(smoothStreamingUri);
    Console.WriteLine(hlsUri);
    Console.WriteLine(mpegDashUri);
```

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Następne kroki
* [Pobieranie zasobów](media-services-deliver-asset-download.md)
* [Konfigurowanie zasad dostarczania zasobów](media-services-dotnet-configure-asset-delivery-policy.md)

