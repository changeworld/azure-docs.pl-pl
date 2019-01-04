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
ms.openlocfilehash: 5aa617edf13aee9c5899a59c46aeb729f202719f
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744211"
---
# <a name="creating-filters-with-cli"></a>Tworzenie filtrów za pomocą interfejsu wiersza polecenia 

Podczas dostarczania zawartości do klientów (przesyłanie strumieniowe wydarzeń na żywo lub wideo na żądanie), klient może wymagać większej elastyczności niż opisane w pliku manifestu zasobu domyślnego. Usługa Azure Media Services umożliwia definiowanie filtrów kont i zasobów filtry dla zawartości. Aby uzyskać więcej informacji, zobacz [filtrów i manifestów dynamicznych](filters-dynamic-manifest-overview.md).

W tym temacie pokazano, jak skonfigurować filtr dla elementu zawartości wideo na żądanie i utworzyć za pomocą interfejsu wiersza polecenia dla usługi Media Services v3 [filtrów kont](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) i [filtry zasobów](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="prerequisites"></a>Wymagania wstępne 

- Zainstaluj interfejs wiersza polecenia i korzystaj z niego lokalnie. Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

    Obecnie nie wszystkie polecenia [interfejsu wiersza polecenia usługi Media Services w wersji 3](https://aka.ms/ams-v3-cli-ref) działają w usłudze Azure Cloud Shell. Zaleca się używanie interfejsu wiersza polecenia lokalnie.
- [Utwórz konto usługi Media Services](create-account-cli-how-to.md). Upewnij się, że do zapamiętania nazwę grupy zasobów i nazwę konta usługi Media Services. 
- Przegląd [filtrów i manifestów dynamicznych](filters-dynamic-manifest-overview.md).

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

Następujące polecenie umożliwia opcję `--tracks` akceptujący pliku. Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, należy określić ścieżkę całego pliku:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @c:\tracks.json
```

Jeśli używasz usługi Azure Cloud Shell przekazania pliku do usługi Cloud Shell (znaleźć przycisk pobierania/przekazywania plików w górnej części okna powłoki). Następnie możesz odwołać się go następująco:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Zobacz też [JSON Przykłady filtrów](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Tworzenie filtrów zasobów

Następujące [filtrowania zawartości usługi ams az](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) polecenie umożliwia utworzenie filtru zasobów za pomocą filtru śledzenie wybrane opcje, które były [wcześniej zdefiniowaną](#define-a-filter). 

> [!TIP]
> Zapoznaj się z informacjami o określaniu lokalizacji nazwy pliku w poprzedniej sekcji.

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Zobacz też [JSON Przykłady filtrów](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter).

## <a name="next-step"></a>Następny krok

[Stream filmów wideo](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Zobacz także

[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
