---
title: Tworzenie filtrów za pomocą interfejsu API REST usługi Azure Media Services w wersji 3
description: W tym temacie opisano sposób tworzenia filtrów, aby klient mógł ich używać do przesyłania strumieniowego określonych sekcji strumienia. Usługa Media Services tworzy dynamiczne manifesty w celu osiągnięcia tego selektywnego przesyłania strumieniowego.
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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: f9134dd3bc926e6e2f454e5187e03365e91ed22a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780338"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Tworzenie filtrów za pomocą interfejsu API REST usługi Media Services

Podczas dostarczania zawartości klientom (przesyłanie strumieniowe wydarzeń na żywo lub wideo na żądanie) klient może potrzebować większej elastyczności niż opisana w pliku manifestu domyślnego zasobu. Usługa Azure Media Services umożliwia definiowanie filtrów kont i filtrów zasobów dla zawartości. 

Aby uzyskać szczegółowy opis tej funkcji i scenariuszy, w których jest używana, zobacz [Dynamiczne manifesty](filters-dynamic-manifest-overview.md) i [filtry](filters-concept.md).

W tym temacie pokazano, jak zdefiniować filtr zasobu Wideo na żądanie i użyć interfejsów API REST do [utworzenia filtrów kont](https://docs.microsoft.com/rest/api/media/accountfilters) i [filtrów zasobów](https://docs.microsoft.com/rest/api/media/assetfilters). 

> [!NOTE]
> Upewnij się, że przegląd [prezentacjiTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Wymagania wstępne 

Aby wykonać kroki opisane w tym temacie, należy:

- Przejrzyj [filtry i manifesty dynamiczne](filters-dynamic-manifest-overview.md).
- [Skonfiguruj listonosz dla wywołań interfejsu API REST usługi Azure Media Services](media-rest-apis-with-postman.md).

    Upewnij się, że postępuj zgodnie z ostatnim krokiem w temacie [Pobierz token usługi Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Definiowanie filtru  

Poniżej przedstawiono **przykład treści żądania,** który definiuje warunki wyboru ścieżki, które są dodawane do manifestu. Filtr ten zawiera wszystkie ścieżki audio, które są EC-3 i wszelkie ścieżki wideo, które mają szybkość transmisji bitów w zakresie 0-1000000.

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "Equal"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>Tworzenie filtrów kont

W pobranej kolekcji Listonosz wybierz pozycję **Filtry**->kont**Utwórz lub zaktualizuj filtr kont**.

Metoda żądania **PUT** HTTP jest podobna do:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Wybierz kartę **Treść** i wklej zdefiniowany [wcześniej](#define-a-filter)kod json .

Wybierz pozycję **Wyślij**. 

Filtr został utworzony.

Aby uzyskać więcej informacji, zobacz [Tworzenie lub aktualizowanie](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Zobacz też [przykłady JSON dla filtrów](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Tworzenie filtrów zasobów  

W pobranej kolekcji Postman usługi "Media Services w wersji 3" wybierz pozycję **Zasoby**->**Utwórz lub zaktualizuj filtr zasobów**.

Metoda żądania **PUT** HTTP jest podobna do:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Wybierz kartę **Treść** i wklej zdefiniowany [wcześniej](#define-a-filter)kod json .

Wybierz pozycję **Wyślij**. 

Filtr zasobów został utworzony.

Aby uzyskać szczegółowe informacje na temat tworzenia lub aktualizowania filtrów zasobów, zobacz [Tworzenie lub aktualizowanie](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Zobacz też [przykłady JSON dla filtrów](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Kojarzenie filtrów z lokalizatorem przesyłania strumieniowego

Można określić listę filtrów zasobów lub kont, które miałyby zastosowanie do lokalizatora przesyłania strumieniowego. [Pakiet pakiet dynamiczny (punkt końcowy przesyłania strumieniowego)](dynamic-packaging-overview.md) stosuje tę listę filtrów wraz z tymi określonymi przez klienta w adresie URL. Ta kombinacja generuje [manifest dynamiczny](filters-dynamic-manifest-overview.md), który jest oparty na filtrach w adresie URL + filtry określone na lokalizatora przesyłania strumieniowego. Zaleca się użycie tej funkcji, jeśli chcesz zastosować filtry, ale nie chcesz ujawniać nazw filtrów w adresie URL.

Aby utworzyć i skojarzyć filtry z lokalizatorem przesyłania strumieniowego przy użyciu `properties.filters` funkcji REST, użyj [lokalizatorów przesyłania strumieniowego — Tworzenie](https://docs.microsoft.com/rest/api/media/streaminglocators/create) interfejsu API i określ w treści [żądania](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body).
                                
## <a name="stream-using-filters"></a>Przesyłanie strumieniowe przy użyciu filtrów

Po zdefiniowaniu filtrów klienci mogą używać ich w adresie URL przesyłania strumieniowego. Filtry mogą być stosowane do adaptacyjnych protokołów przesyłania strumieniowego szybkości transmisji bitów: Apple HTTP Live Streaming (HLS), MPEG-DASH i Smooth Streaming.

W poniższej tabeli przedstawiono kilka przykładów adresów URL z filtrami:

|Protocol (Protokół)|Przykład|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Następne kroki

[Przesyłanie strumieniowe filmów](stream-files-tutorial-with-rest.md) 
