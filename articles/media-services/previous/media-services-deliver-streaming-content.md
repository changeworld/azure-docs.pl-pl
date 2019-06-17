---
title: Publikowanie zawartości usługi Azure Media Services przy użyciu platformy .NET | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć Lokalizator, który jest używany do tworzenia adresu URL przesyłania strumieniowego. Przykłady kodu są napisane C# i użyj Media Services SDK dla platformy .NET.
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
ms.openlocfilehash: b1d0c070a9196eaa9a2706a607baa9a2926e2db4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051745"
---
# <a name="publish-media-services-content-using-net"></a>Publikowanie zawartości Media Services przy użyciu platformy .NET  
> [!div class="op_single_selector"]
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>Omówienie
Można przesyłanie strumieniowe z adaptacyjną szybkością transmisji bitów w formacie MP4 ustawione przez utworzenie lokalizatora OnDemand przesyłania strumieniowego i tworzenia adresu URL przesyłania strumieniowego. [Kodowanie elementu zawartości](media-services-encode-asset.md) temacie pokazano, jak do zakodowania w adaptacyjną szybkością transmisji bitów zestawu plików MP4. 

> [!NOTE]
> Jeśli zawartość jest zaszyfrowany, konfigurowanie zasad dostarczania elementów zawartości (zgodnie z opisem w [to](media-services-dotnet-configure-asset-delivery-policy.md) tematu) przed utworzeniem lokalizatora. 
> 
> 

Lokalizatora OnDemand przesyłania strumieniowego służy również do tworzenia adresów URL, które wskazują na pliki MP4, które można pobrać progresywnie.  

W tym temacie pokazano, jak utworzyć Lokalizator OnDemand przesyłania strumieniowego można opublikować element zawartości i tworzyć Smooth, MPEG DASH i HLS adresów URL przesyłania strumieniowego. Pokazuje także gorąca tworzyć adresy URL pobierania progresywnego. 

## <a name="create-an-ondemand-streaming-locator"></a>Tworzenie lokalizatora OnDemand przesyłania strumieniowego
Aby utworzyć Lokalizator przesyłania strumieniowego na żądanie i uzyskiwanie adresów URL, należy wykonać następujące czynności:

1. Jeśli zawartość jest zaszyfrowany, należy zdefiniować zasadę dostępu.
2. Tworzenie lokalizatora OnDemand przesyłania strumieniowego.
3. Jeśli użytkownik chce przesyłać strumieniowo, Pobierz przesyłania strumieniowego pliku manifestu (ISM) w elemencie zawartości. 
   
   Jeśli planujesz progresywnie uzyskać nazwy plików MP4 w elemencie zawartości.  
4. Tworzenie adresów URL do pliku manifestu lub plików MP4. 


>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Użyj tego samego Identyfikatora zasad, jeśli zawsze używasz tych samych dni / uprawnień dostępu. Na przykład zasad lokalizatorów, które powinny pozostać w miejscu przez długi czas (nieprzekazywane zasady). Aby uzyskać więcej informacji, zobacz [ten](media-services-dotnet-manage-entities.md#limit-access-policies) temat.

### <a name="use-media-services-net-sdk"></a>Zastosowania usług Media Services .NET SDK
Tworzyć adresy URL przesyłania strumieniowego 

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

Dane wyjściowe:

    URL to manifest for client streaming using Smooth Streaming protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
    URL to manifest for client streaming using HLS protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
    URL to manifest for client streaming using MPEG DASH protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


> [!NOTE]
> Można również przesyłać strumieniowo zawartość, za pośrednictwem połączenia SSL. Aby zrobić to podejście, upewnij się, rozpoczęciu adresy URL przesyłania strumieniowego przy użyciu protokołu HTTPS. Obecnie usługa AMS nie obsługuje protokołu SSL z zastosowaniem domen niestandardowych.
> 
> 

Tworzyć adresy URL pobierania progresywnego 

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
Dane wyjściowe:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4

    . . . 

### <a name="use-media-services-net-sdk-extensions"></a>Użyj rozszerzenia SDK .NET usługi Media Services
Poniższy kod wywołuje metody rozszerzenia zestawu .NET SDK, które utworzyć Lokalizator oraz generować Smooth Streaming, HLS i MPEG-DASH w adresach URL do adaptacyjnego przesyłania strumieniowego.
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

## <a name="next-steps"></a>Kolejne kroki
* [Pobierz zasoby](media-services-deliver-asset-download.md)
* [Konfigurowanie zasad dostarczania elementów zawartości](media-services-dotnet-configure-asset-delivery-policy.md)

