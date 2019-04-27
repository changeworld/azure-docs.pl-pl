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
ms.date: 11/26/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2ba3de32f4ec3b9f6faf1d5a51da9c1c91e4a2e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60732437"
---
# <a name="creating-filters-with-cli"></a>Tworzenie filtrów za pomocą interfejsu wiersza polecenia 

Podczas dostarczania zawartości do klientów (przesyłanie strumieniowe wydarzeń na żywo lub wideo na żądanie), klient może wymagać większej elastyczności niż opisane w pliku manifestu zasobu domyślnego. Usługa Azure Media Services umożliwia definiowanie filtrów kont i zasobów filtry dla zawartości. Aby uzyskać więcej informacji, zobacz [filtrów i manifestów dynamicznych](filters-dynamic-manifest-overview.md).

W tym temacie pokazano, jak skonfigurować filtr dla elementu zawartości wideo na żądanie i utworzyć za pomocą interfejsu wiersza polecenia dla usługi Media Services v3 [filtrów kont](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) i [filtry zasobów](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="prerequisites"></a>Wymagania wstępne 

- [Utwórz konto usługi Media Services](create-account-cli-how-to.md). Upewnij się, że do zapamiętania nazwę grupy zasobów i nazwę konta usługi Media Services. 
- Przegląd [filtrów i manifestów dynamicznych](filters-dynamic-manifest-overview.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Określa filtr 

W poniższym przykładzie zdefiniowano warunki wybór ścieżki, które są dodawane do końcowego manifestu. Ten filtr zawiera ścieżki audio, znajdujących się we 3 i wideo ścieżek, które mają szybkości transmisji bitów w 0-1000000 zakresu.

Filtrami zdefiniowanymi w spoczynku, zawierać obiekt JSON otoki "Properties".  

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

## <a name="next-step"></a>Następny krok

[Stream filmów wideo](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Zobacz także

[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
