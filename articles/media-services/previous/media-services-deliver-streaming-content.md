---
title: Publikowanie zawartości usługi Azure Media Services przy użyciu platformy .NET | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć Lokalizator, który jest używany do tworzenia adresu URL przesyłania strumieniowego. Przykłady kodu są napisane w języku C# i używają SDK usługi Media Services dla platformy .NET.
author: juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: c53b1f83-4cb1-4b09-840f-9c145b7d6f8d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: 224c9cf5ef9925645de1d94dc5bc03c15ba91432
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="publish-azure-media-services-content-using-net"></a>Publikowanie zawartości usługi Azure Media Services przy użyciu platformy .NET
> [!div class="op_single_selector"]
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>Przegląd
Można strumienia o adaptacyjnej szybkości bitowej MP4 ustawione przez utworzenie lokalizatora OnDemand przesyłania strumieniowego i tworzenia adresu URL przesyłania strumieniowego. [Kodowanie zasób](media-services-encode-asset.md) temacie przedstawiono sposób kodowania w adaptacyjnej szybkości bitowej MP4 zestawu. 

> [!NOTE]
> Jeśli zawartość jest zaszyfrowany, należy skonfigurować zasady dostarczania elementu zawartości (zgodnie z opisem w [to](media-services-dotnet-configure-asset-delivery-policy.md) tematu) przed utworzeniem lokalizatora. 
> 
> 

Lokalizator OnDemand przesyłania strumieniowego umożliwia również tworzenie adresów URL wskazujących na pliki MP4, które można pobrać progresywnie.  

W tym temacie pokazano, jak utworzyć Lokalizator OnDemand przesyłania strumieniowego do publikowania zawartości i tworzenie Smooth, MPEG DASH i HLS adresów URL przesyłania strumieniowego. Zawiera także gorących do tworzenia adresów URL pobierania progresywnego. 

## <a name="create-an-ondemand-streaming-locator"></a>Utwórz Lokalizator OnDemand przesyłania strumieniowego
Aby utworzyć Lokalizator przesyłania strumieniowego na żądanie i uzyskiwanie adresów URL, należy wykonać następujące czynności:

1. Jeśli zawartość jest zaszyfrowany, należy zdefiniować zasadę dostępu.
2. Utwórz Lokalizator OnDemand przesyłania strumieniowego.
3. Jeśli planujesz strumieniowo, Pobierz przesyłania strumieniowego pliku manifestu (.ism) w elemencie zawartości. 
   
   Jeśli planujesz ma być pobierana progresywnie, Pobierz nazwy plików MP4 w elemencie zawartości.  
4. Tworzenie adresów URL do pliku manifestu lub plików MP4. 


>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Użyj tego samego Identyfikatora zasad, jeśli są zawsze przy użyciu tego samego dni / uprawnień dostępu. Na przykład zasady lokalizatorów, które powinny pozostać w miejscu przez długi czas (— przekazywanie zasady). Aby uzyskać więcej informacji, zobacz [ten](media-services-dotnet-manage-entities.md#limit-access-policies) temat.

### <a name="use-media-services-net-sdk"></a>Użyj usługi Media Services zestawu .NET SDK
Tworzenie adresy URL przesyłania strumieniowego 

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
        var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
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
> Można również strumienia zawartości za pośrednictwem połączenia SSL. Aby wykonać tą metodą, upewnij się, uruchamiania adresy URL przesyłania strumieniowego z protokołu HTTPS. Obecnie usługa AMS nie obsługuje protokołu SSL z domen niestandardowych.
> 
> 

Tworzenie adresy URL pobierania progresywnego 

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
Poniższy kod wywołuje metody rozszerzenia zestawu .NET SDK, które tworzenie lokalizatora i generowanie Smooth Streaming, HLS i MPEG-DASH adresy URL do adaptacyjnego przesyłania strumieniowego.
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
* [Pobieranie zasobów](media-services-deliver-asset-download.md)
* [Konfigurowanie zasad dostarczania elementów zawartości](media-services-dotnet-configure-asset-delivery-policy.md)

