---
title: Tworzenie filtrów za pomocą zestawu .NET SDK usługi Azure Media Services
description: W tym temacie opisano sposób tworzenia filtrów, aby klient mógł ich używać do przesyłania strumieniowego określonych sekcji strumienia. Usługa Media Services tworzy dynamiczne manifesty w celu osiągnięcia tego selektywnego przesyłania strumieniowego.
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
ms.author: juliako
ms.reviewer: cenkdin
ms.openlocfilehash: c60b223f91a151bf63cabc5e95816f2545022503
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69016601"
---
# <a name="creating-filters-with-media-services-net-sdk"></a>Tworzenie filtrów za pomocą sdk usługi Media Services .NET 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [Reszta](media-services-rest-dynamic-manifest.md)
> 
> 

Począwszy od wersji 2.17, program Media Services umożliwia definiowanie filtrów dla zasobów. Filtry te są regułami po stronie serwera, które umożliwiają klientom wybranie takich czynności, jak: odtwarzanie tylko sekcji wideo (zamiast odtwarzania całego filmu) lub określanie tylko podzbioru wersji audio i wideo, które może obsługiwać urządzenie klienta (zamiast wszystkich wersji skojarzonych z zasobem). To filtrowanie zasobów odbywa się za pomocą **manifestów dynamicznych,** które są tworzone na żądanie klienta w celu przesyłania strumieniowego wideo na podstawie określonych filtrów.

Aby uzyskać bardziej szczegółowe informacje dotyczące filtrów i manifestu dynamicznego, zobacz [Omówienie manifestów dynamicznych](media-services-dynamic-manifest-overview.md).

W tym artykule pokazano, jak używać pakietu SDK usługi Media Services .NET do tworzenia, aktualizowania i usuwania filtrów. 

Uwaga, jeśli zaktualizujesz filtr, może upłynąć do dwóch minut dla punktu końcowego przesyłania strumieniowego, aby odświeżyć reguły. Jeśli zawartość została wyświetlona przy użyciu tego filtru (i buforowana w plikach proxy i pamięci podręcznych sieci CDN), zaktualizowanie tego filtru może spowodować błędy odtwarzacza. Zawsze wyczyść pamięć podręczną po zaktualizowaniu filtru. Jeśli ta opcja nie jest możliwa, należy rozważyć użycie innego filtru. 

## <a name="types-used-to-create-filters"></a>Typy używane do tworzenia filtrów
Podczas tworzenia filtrów używane są następujące typy: 

* **IStreamingFilter**.  Ten typ jest oparty na następującym [filtrze](https://docs.microsoft.com/rest/api/media/operations/filter) interfejsu API REST
* **IStreamingAssetFilter**. Ten typ jest oparty na następujących REST API [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* **PresentationTimeRange**. Ten typ jest oparty na następujących rest API [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* **FilterTrackSelectStatement** i **IFilterTrackPropertyCondition**. Te typy są oparte na następujących interfejsach API REST [FilterTrackSelect i FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

## <a name="createupdatereaddelete-global-filters"></a>Tworzenie/aktualizowanie/odczytywanie/usuwanie filtrów globalnych
Poniższy kod pokazuje, jak używać platformy .NET do tworzenia, aktualizowania, odczytywania i usuwania filtrów zasobów.

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

## <a name="createupdatereaddelete-asset-filters"></a>Tworzenie/aktualizowanie/odczytywanie/usuwanie filtrów zasobów
Poniższy kod pokazuje, jak używać platformy .NET do tworzenia, aktualizowania, odczytywania i usuwania filtrów zasobów.

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


## <a name="build-streaming-urls-that-use-filters"></a>Tworzenie adresów URL przesyłania strumieniowego, które używają filtrów
Aby uzyskać informacje na temat publikowania i dostarczania zasobów, zobacz [Omówienie dostarczania zawartości klientom.](media-services-deliver-content-overview.md)

Poniższe przykłady pokazują, jak dodać filtry do adresów URL przesyłania strumieniowego.

**MPEG DASH** 

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Transmisja na żywo (HLS) v4**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Transmisja na żywo (HLS) v3**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Omówienie manifestów dynamicznych](media-services-dynamic-manifest-overview.md)

