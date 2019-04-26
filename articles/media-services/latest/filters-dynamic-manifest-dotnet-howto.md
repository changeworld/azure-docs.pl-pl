---
title: Tworzenie filtrów za pomocą zestawu .NET SDK usługi Azure Media Services
description: W tym temacie opisano sposób tworzenia filtrów, więc klienta można ich używać do określonych sekcji strumienia strumienia. Usługa Media Services tworzy manifestów dynamicznych w celu uzyskania tego selektywne przesyłania strumieniowego.
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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 3517a9c0aabf9e8ec029405f14461626d32335a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322740"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Tworzenie filtrów za pomocą zestawu SDK .NET usługi Media Services

Podczas dostarczania zawartości do klientów (przesyłanie strumieniowe wydarzeń na żywo lub wideo na żądanie) klienta może wymagać większej elastyczności niż opisane w pliku manifestu zasobu domyślnego. Usługa Azure Media Services umożliwia definiowanie filtrów kont i zasobów filtry dla zawartości. Aby uzyskać więcej informacji, zobacz [filtrów i manifestów dynamicznych](filters-dynamic-manifest-overview.md).

W tym temacie pokazano, jak zdefiniować filtr dla wideo na żądanie zasobów oraz tworzenia za pomocą zestawu SDK .NET usługi Media Services [filtrów kont](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) i [filtry zasobów](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet). 

## <a name="prerequisites"></a>Wymagania wstępne 

- Przegląd [filtrów i manifestów dynamicznych](filters-dynamic-manifest-overview.md).
- [Utwórz konto usługi Media Services](create-account-cli-how-to.md). Upewnij się, że do zapamiętania nazwę grupy zasobów i nazwę konta usługi Media Services. 
- Uzyskaj informacje potrzebne do [dostęp do interfejsów API](access-api-cli-how-to.md)
- Przegląd [przekazywanie, kodowanie i przesyłanie strumieniowe przy użyciu usługi Azure Media Services](stream-files-tutorial-with-api.md) aby zobaczyć, jak [rozpocząć korzystanie z zestawu SDK platformy .NET](stream-files-tutorial-with-api.md#start_using_dotnet)

## <a name="define-a-filter"></a>Określa filtr  

Na platformie .NET, skonfiguruj opcje śledzenia z [FilterTrackSelection](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) i [FilterTrackPropertyCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet) klasy. 

Poniższy kod definiuje filtr, który zawiera wszystkie ścieżki audio, znajdujących się we 3 i wideo ścieżek, które mają szybkości transmisji bitów w 0-1000000 zakresu.

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Audio", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Video", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Bitrate, "0-1000000", FilterTrackPropertyCompareOperation.Equal)
};

List<FilterTrackSelection> includedTracks = new List<FilterTrackSelection>()
{
    new FilterTrackSelection(audioConditions),
    new FilterTrackSelection(videoConditions)
};
```

## <a name="create-account-filters"></a>Tworzenie filtrów konta

Poniższy kod przedstawia sposób użycia platformy .NET do tworzenia filtru konta, zawierającą wszystkie wybory śledzenie [zdefiniowanych powyżej](#define-a-filter). 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Tworzenie filtrów zasobów

Poniższy kod przedstawia sposób użycia platformy .NET do tworzenia filtru zasobów, zawierającą wszystkie wybory śledzenie [zdefiniowanych powyżej](#define-a-filter). 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="next-steps"></a>Kolejne kroki

[Stream filmów wideo](stream-files-tutorial-with-api.md) 


