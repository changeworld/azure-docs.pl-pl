---
title: Tworzenie filtrów za pomocą usługi Azure Media Services — interfejs API REST | Dokumentacja firmy Microsoft
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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 447116267e53f8c4df1e882ca30c6a2e906d314c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67114289"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Tworzenie filtrów za pomocą interfejsu API REST usługi Media Services

Podczas dostarczania zawartości do klientów (przesyłanie strumieniowe wydarzeń na żywo lub wideo na żądanie) klienta może wymagać większej elastyczności niż opisane w pliku manifestu zasobu domyślnego. Usługa Azure Media Services umożliwia definiowanie filtrów kont i zasobów filtry dla zawartości. 

Aby uzyskać szczegółowy opis tej funkcji i scenariuszy, w którym jest używany, zobacz [manifestów dynamicznych](filters-dynamic-manifest-overview.md) i [filtry](filters-concept.md).

W tym temacie pokazano, jak zdefiniować filtr dla wideo na żądanie zasobów i utworzyć za pomocą interfejsów API REST [filtrów kont](https://docs.microsoft.com/rest/api/media/accountfilters) i [filtry zasobów](https://docs.microsoft.com/rest/api/media/assetfilters). 

> [!NOTE]
> Upewnij się zapoznać się z [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Wymagania wstępne 

Aby wykonać kroki opisane w tym temacie, musisz:

- Przegląd [filtrów i manifestów dynamicznych](filters-dynamic-manifest-overview.md).
- [Konfigurowanie narzędzia Postman dla wywołania interfejsu API REST usługi Azure Media Services](media-rest-apis-with-postman.md).

    Upewnij się, że należy wykonać ostatni krok w temacie [pobrać tokenu usługi Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Określa filtr  

Poniżej przedstawiono **treść żądania** przykładu, który definiuje warunki wybór ścieżki, które są dodawane do manifestu. Ten filtr zawiera ścieżki audio, znajdujących się we 3 i wideo ścieżek, które mają szybkości transmisji bitów w 0-1000000 zakresu.

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

## <a name="create-account-filters"></a>Tworzenie filtrów konta

W kolekcji Postman, która został pobrany, wybierz **filtrów kont**->**tworzenia lub aktualizacji Filtr konta**.

**Umieścić** metoda żądania HTTP jest podobny do:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Wybierz **treści** kartę i wklej dane json kod [wcześniej zdefiniowaną](#define-a-filter).

Wybierz pozycję **Wyślij**. 

Filtr został utworzony.

Aby uzyskać więcej informacji, zobacz [tworzenia lub aktualizacji](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Zobacz też [JSON Przykłady filtrów](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Tworzenie filtrów zasobów  

W kolekcji Postman "Usługi Media Services v3", który został pobrany, wybierz **zasoby**->**tworzenia lub aktualizacji filtru zasobów**.

**Umieścić** metoda żądania HTTP jest podobny do:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Wybierz **treści** kartę i wklej dane json kod [wcześniej zdefiniowaną](#define-a-filter).

Wybierz pozycję **Wyślij**. 

Filtr zasób został utworzony.

Aby uzyskać szczegółowe informacje na temat Utwórz lub zaktualizuj zasób filtrów, zobacz [tworzenia lub aktualizacji](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Zobacz też [JSON Przykłady filtrów](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter). 

## <a name="associate-filters-with-streaming-locator"></a>Kojarzenie filtrów z lokalizatora przesyłania strumieniowego

Można określić listę filtrów zasobów lub konta, które będzie dotyczyć Twojego lokalizatora przesyłania strumieniowego. [Funkcji dynamicznego pakowania (punkt końcowy przesyłania strumieniowego)](dynamic-packaging-overview.md) ma zastosowanie ta lista filtrów wraz z tymi klienta określa się w adresie URL. Ta kombinacja generuje [manifestów dynamicznych](filters-dynamic-manifest-overview.md), która jest oparta na filtry w adresie URL i filtry, możesz określić na lokalizatora przesyłania strumieniowego. Zaleca się korzystania z tej funkcji, jeśli chcesz zastosować filtry, ale nie należy udostępniać nazwy filtru w adresie URL.

Aby utworzyć i skojarzyć filtrów z lokalizatora przesyłania strumieniowego przy użyciu usługi REST, należy użyć [utworzyć Lokalizatory przesyłania strumieniowego -](https://docs.microsoft.com/rest/api/media/streaminglocators/create) interfejsu API i określ `properties.filters` w [treść żądania](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body).
                                
## <a name="stream-using-filters"></a>Stream przy użyciu filtrów

Po zdefiniowaniu filtrów, klienci mogą ich używać w adresu URL przesyłania strumieniowego. Filtry można zastosować do adaptacyjną szybkością transmisji bitów, protokołów przesyłania strumieniowego: Apple HTTP Live przesyłania strumieniowego (HLS), między innymi MPEG-DASH i Smooth Streaming.

W poniższej tabeli przedstawiono przykładowe adresy URL przy użyciu filtrów:

|Protocol|Przykład|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Kolejne kroki

[Stream filmów wideo](stream-files-tutorial-with-rest.md) 
