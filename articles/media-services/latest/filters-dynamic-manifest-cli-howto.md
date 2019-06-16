---
title: Interfejs wiersza polecenia umożliwiają tworzenie filtrów za pomocą usługi Azure Media Services | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak tworzenie filtrów za pomocą usługi Media Services za pomocą interfejsu wiersza polecenia.
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
ms.openlocfilehash: c6007b66e31996db5c6b043219470968a7b05031
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67114687"
---
# <a name="creating-filters-with-cli"></a>Tworzenie filtrów za pomocą interfejsu wiersza polecenia 

Podczas dostarczania zawartości do klientów (przesyłanie strumieniowe wydarzeń na żywo lub wideo na żądanie), klient może wymagać większej elastyczności niż opisane w pliku manifestu zasobu domyślnego. Usługa Azure Media Services umożliwia definiowanie filtrów kont i zasobów filtry dla zawartości. 

Aby uzyskać szczegółowy opis tej funkcji i scenariuszy, w którym jest używany, zobacz [manifestów dynamicznych](filters-dynamic-manifest-overview.md) i [filtry](filters-concept.md).

W tym temacie pokazano, jak skonfigurować filtr dla elementu zawartości wideo na żądanie i utworzyć za pomocą interfejsu wiersza polecenia dla usługi Media Services v3 [filtrów kont](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) i [filtry zasobów](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

> [!NOTE]
> Upewnij się zapoznać się z [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Wymagania wstępne 

- [Utwórz konto usługi Media Services](create-account-cli-how-to.md). Upewnij się, że do zapamiętania nazwę grupy zasobów i nazwę konta usługi Media Services. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Określa filtr 

W poniższym przykładzie zdefiniowano warunki wybór ścieżki, które są dodawane do końcowego manifestu. Ten filtr zawiera ścieżki audio, znajdujących się we 3 i wideo ścieżek, które mają szybkości transmisji bitów w 0-1000000 zakresu.

> [!TIP]
> Jeśli zamierzasz zdefiniować **filtry** w spoczynku, zwróć uwagę, muszą zawierać obiekt JSON otoki "Properties".  

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

## <a name="create-account-filters"></a>Tworzenie filtrów konta

Następujące [filtru konta usługi ams az](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) polecenie umożliwia utworzenie filtru konta za pomocą filtru śledzenie wybrane opcje, które były [wcześniej zdefiniowaną](#define-a-filter). 

To polecenie umożliwia przekazywanie opcjonalny `--tracks` parametr, który zawiera JSON reprezentujący wybór ścieżki.  Użyj @{file}, aby załadować JSON z pliku. Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, należy określić ścieżkę całego pliku:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Zobacz też [JSON Przykłady filtrów](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Tworzenie filtrów zasobów

Następujące [filtrowania zawartości usługi ams az](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) polecenie umożliwia utworzenie filtru zasobów za pomocą filtru śledzenie wybrane opcje, które były [wcześniej zdefiniowaną](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Zobacz też [JSON Przykłady filtrów](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter).

## <a name="associate-filters-with-streaming-locator"></a>Kojarzenie filtrów z lokalizatora przesyłania strumieniowego

Można określić listę filtrów zasobów lub konta, które będzie dotyczyć Twojego lokalizatora przesyłania strumieniowego. [Funkcji dynamicznego pakowania (punkt końcowy przesyłania strumieniowego)](dynamic-packaging-overview.md) ma zastosowanie ta lista filtrów wraz z tymi klienta określa się w adresie URL. Ta kombinacja generuje [manifestów dynamicznych](filters-dynamic-manifest-overview.md), która jest oparta na filtry w adresie URL i filtry, możesz określić na lokalizatora przesyłania strumieniowego. Zaleca się korzystania z tej funkcji, jeśli chcesz zastosować filtry, ale nie należy udostępniać nazwy filtru w adresie URL.

Poniższy kod interfejsu wiersza polecenia pokazuje, jak utworzyć Lokalizator przesyłania strumieniowego i określ `filters`. Jest to opcjonalna właściwość, która przyjmuje rozdzielonych spacjami listę nazw filtrów zasobów i/lub nazwy filtru kont.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Stream przy użyciu filtrów

Po zdefiniowaniu filtrów, klienci mogą ich używać w adresu URL przesyłania strumieniowego. Filtry można zastosować do adaptacyjną szybkością transmisji bitów, protokołów przesyłania strumieniowego: Apple HTTP Live przesyłania strumieniowego (HLS), między innymi MPEG-DASH i Smooth Streaming.

W poniższej tabeli przedstawiono przykładowe adresy URL przy użyciu filtrów:

|Protocol|Przykład|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Następny krok

[Stream filmów wideo](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Zobacz także

[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
