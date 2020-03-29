---
title: Tworzenie filtrów za pomocą usługi Azure Media Services za pomocą interfejsu wiersza polecenia( Dokumenty firmy Microsoft
description: W tym artykule pokazano, jak używać interfejsu wiersza polecenia do tworzenia filtrów za pomocą usługi Azure Media Services w wersji 3.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 74516aa921e45917f327a193a1c972b021c9c8ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896076"
---
# <a name="creating-filters-with-cli"></a>Tworzenie filtrów za pomocą interfejsu wiersza polecenia 

Podczas dostarczania zawartości do klientów (przesyłanie strumieniowe wydarzeń na żywo lub wideo na żądanie) klient może potrzebować większej elastyczności niż opisana w pliku manifestu domyślnego zasobu. Usługa Azure Media Services umożliwia definiowanie filtrów kont i filtrów zasobów dla zawartości. 

Aby uzyskać szczegółowy opis tej funkcji i scenariuszy, w których jest używana, zobacz [Dynamiczne manifesty](filters-dynamic-manifest-overview.md) i [filtry](filters-concept.md).

W tym temacie pokazano, jak skonfigurować filtr zasobu wideo na żądanie i użyć interfejsu wiersza polecenia dla usługi Media Services w wersji 3 do [utworzenia filtrów kont](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) i [filtrów zasobów](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

> [!NOTE]
> Upewnij się, że przegląd [prezentacjiTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Wymagania wstępne 

- [Utwórz konto usługi Media Services](create-account-cli-how-to.md). Pamiętaj, aby zapamiętać nazwę grupy zasobów i nazwę konta usługi Media Services. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Definiowanie filtru 

Poniższy przykład definiuje warunki wyboru ścieżki, które są dodawane do końcowego manifestu. Filtr ten zawiera wszystkie ścieżki audio, które są EC-3 i wszelkie ścieżki wideo, które mają szybkość transmisji bitów w zakresie 0-1000000.

> [!TIP]
> Jeśli planujesz zdefiniować **filtry** w REST, należy zauważyć, że należy dołączyć "Właściwości" otoka JSON obiektu.  

```json
[
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
                "operation": "NotEqual"
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
```

## <a name="create-account-filters"></a>Tworzenie filtrów kont

Następujące polecenie [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) tworzy filtr konta z wyborami ścieżki filtru, które zostały [zdefiniowane wcześniej](#define-a-filter). 

Polecenie umożliwia przekazanie opcjonalnego `--tracks` parametru zawierającego JSON reprezentujący wybór ścieżki.  Użyj @{file}, aby załadować JSON z pliku. Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, określ całą ścieżkę pliku:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Zobacz też [przykłady JSON dla filtrów](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Tworzenie filtrów zasobów

Następujące polecenie [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) tworzy filtr zasobów z wyborami ścieżki filtru, które zostały [zdefiniowane wcześniej](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Zobacz też [przykłady JSON dla filtrów](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter).

## <a name="associate-filters-with-streaming-locator"></a>Kojarzenie filtrów z lokalizatorem przesyłania strumieniowego

Można określić listę filtrów zasobów lub kont, które miałyby zastosowanie do lokalizatora przesyłania strumieniowego. [Pakiet pakiet dynamiczny (punkt końcowy przesyłania strumieniowego)](dynamic-packaging-overview.md) stosuje tę listę filtrów wraz z tymi określonymi przez klienta w adresie URL. Ta kombinacja generuje [manifest dynamiczny](filters-dynamic-manifest-overview.md), który jest oparty na filtrach w adresie URL + filtry określone na lokalizatora przesyłania strumieniowego. Zaleca się użycie tej funkcji, jeśli chcesz zastosować filtry, ale nie chcesz ujawniać nazw filtrów w adresie URL.

Poniższy kod interfejsu wiersza polecenia pokazuje, `filters`jak utworzyć lokalizator przesyłania strumieniowego i określić . Jest to opcjonalna właściwość, która przyjmuje oddzieloną spacją listę nazw filtrów zasobów i/lub nazw filtrów kont.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Przesyłanie strumieniowe przy użyciu filtrów

Po zdefiniowaniu filtrów klienci mogą używać ich w adresie URL przesyłania strumieniowego. Filtry mogą być stosowane do adaptacyjnych protokołów przesyłania strumieniowego szybkości transmisji bitów: Apple HTTP Live Streaming (HLS), MPEG-DASH i Smooth Streaming.

W poniższej tabeli przedstawiono kilka przykładów adresów URL z filtrami:

|Protocol (Protokół)|Przykład|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Następny krok

[Przesyłanie strumieniowe filmów](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Zobacz też

[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
