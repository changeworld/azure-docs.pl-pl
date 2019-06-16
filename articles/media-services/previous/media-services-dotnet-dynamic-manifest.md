---
title: Tworzenie filtrów za pomocą zestawu .NET SDK usługi Azure Media Services
description: W tym temacie opisano sposób tworzenia filtrów, więc klienta można ich używać do określonych sekcji strumienia strumienia. Usługa Media Services tworzy manifestów dynamicznych w celu uzyskania tego selektywne przesyłania strumieniowego.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 2f6894ca-fb43-43c0-9151-ddbb2833cafd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako;cenkdin
ms.openlocfilehash: 05b899658b5c58e15b2f30ab759eb49319979fee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61465562"
---
# <a name="creating-filters-with-media-services-net-sdk"></a>Tworzenie filtrów za pomocą usługi Media Services .NET SDK 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

Począwszy od wersji 2.17, Media Services umożliwia definiowanie filtrów dla zasobów. Te filtry są reguły po stronie serwera, które umożliwiają klientom chce wykonywać następujące czynności: odtwarzanie tylko na część wideo (zamiast odtwarzanie całego), lub określ tylko podzbiór odwzorowaniami audio i wideo, obsługujące przez klienta urządzenia (zamiast z wszystkie wersje, które są skojarzone z elementem zawartości). Filtrowanie zasobów odbywa się za pośrednictwem **manifestów dynamicznych**s, które są tworzone na żądanie klienta do przesyłania strumieniowego wideo oparte na określonej filtry.

Aby uzyskać szczegółowe informacje dotyczące filtrów i manifestów dynamicznych, zobacz [dynamiczne manifesty Przegląd](media-services-dynamic-manifest-overview.md).

W tym artykule przedstawiono sposób Media Services .NET SDK umożliwia tworzenie, aktualizowanie i usuwanie filtrów. 

Należy pamiętać o tym, jeśli zaktualizujesz filtru, może potrwać do dwóch minut, zanim punkt końcowy, aby odświeżyć zasady przesyłania strumieniowego. Zawartość zostało obsłużone za pomocą tego filtru (i w pamięci podręcznej serwerów proxy i Azure CDN pamięci podręcznych), aktualizacja tego filtru może spowodować błędy odtwarzacza. Zawsze należy wyczyścić pamięć podręczną po zaktualizowaniu filtru. Jeśli ta opcja nie jest możliwe, należy wziąć pod uwagę przy użyciu innego filtru. 

## <a name="types-used-to-create-filters"></a>Typy używane do tworzenia filtrów
Podczas tworzenia filtrów, używane są następujące typy: 

* **IStreamingFilter**.  Na następujący interfejs API REST jest oparty ten typ [filtru](https://docs.microsoft.com/rest/api/media/operations/filter)
* **IStreamingAssetFilter**. Na następujący interfejs API REST jest oparty ten typ [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* **PresentationTimeRange**. Na następujący interfejs API REST jest oparty ten typ [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* **FilterTrackSelectStatement** i **IFilterTrackPropertyCondition**. Te typy są oparte na następujących interfejsów API REST [FilterTrackSelect i FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

## <a name="createupdatereaddelete-global-filters"></a>Tworzenie/aktualizowanie/odczyt/usuwanie filtry globalne
Poniższy kod przedstawia sposób .NET umożliwia tworzenie, aktualizowanie i odczytu i usuwania zasobów filtrów.

```csharp
    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();

    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();

    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);

    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);

    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();

    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();
```

## <a name="createupdatereaddelete-asset-filters"></a>Filtry Tworzenie/aktualizowanie/odczyt/Usuwanie zasobu
Poniższy kod przedstawia sposób .NET umożliwia tworzenie, aktualizowanie i odczytu i usuwania zasobów filtrów.

```csharp
    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();


    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());

    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);

    filter.Update();

    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filterUpdated.Delete();

```


## <a name="build-streaming-urls-that-use-filters"></a>Tworzenie adresów URL, które używają filtrów przesyłania strumieniowego
Aby uzyskać informacje na temat publikowania i dostarczać zasobów, zobacz [dostarczanie zawartości klientom Przegląd](media-services-deliver-content-overview.md).

Następujące przykłady przedstawiają sposób dodawania filtrów do przesyłania strumieniowego adresami URL.

**MPEG DASH** 

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live przesyłania strumieniowego V4 (HLS)**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live przesyłania strumieniowego (HLS) V3**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Omówienie manifestów dynamicznych](media-services-dynamic-manifest-overview.md)

