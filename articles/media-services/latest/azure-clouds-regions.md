---
title: Chmury i regiony, w których dostępna jest usługa Azure Media Services w wersji 3
description: W tym artykule owieszeń dotyczących chmur i regionów platformy Azure, w których usługa Azure Media Services w wersji 3 jest dostępna.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.openlocfilehash: 1257bf4dfb0d5b2c4995cac760290f97293a0c0f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382974"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Chmury i regiony, w których istnieje usługa Azure Media Services w wersji 3

Usługa Azure Media Services w wersji 3 jest dostępna za pośrednictwem manifestu usługi Azure Resource Manager w globalnej platformie Azure, usłudze Azure Government, Azure Germany, Azure China 21Vianet. Jednak nie wszystkie funkcje usługi Media Services są dostępne we wszystkich chmurach platformy Azure. W tym dokumencie opisano dostępność głównych składników usługi Media Services w wersji 3.

## <a name="feature-availability-in-azure-clouds"></a>Dostępność funkcji w chmurach platformy Azure

| Funkcja|Globalne regiony platformy Azure | Azure Government|Azure (Niemcy)|Azure w Chinach — 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure Event Grid](reacting-to-media-services-events.md) | Dostępne | Niedostępne | Niedostępne | Niedostępne |
| [WideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Dostępne | Niedostępne | Niedostępne | Niedostępne |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Dostępne | Niedostępne | Niedostępne | Niedostępne |
| [StandardEncoderPreset](encoding-concept.md) | Dostępne | Dostępne | Dostępne | Dostępne |
| [LiveEvents (Wydarzenia na żywo)](live-streaming-overview.md) | Dostępne | Dostępne | Dostępne | Dostępne |
| [Punkty przesyłania strumieniowego](streaming-endpoint-concept.md) | Dostępne | Dostępne | Dostępne | Dostępne |

## <a name="regionsgeographieslocations"></a>Regiony/regiony geograficzne/lokalizacje

[Regiony, w których wdrożono usługę Azure Media Services](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>Nazwa kodowa regionu

Jeśli trzeba podać parametr **lokalizacji,** należy podać nazwę kodu regionu jako wartość **lokalizacji.** Aby uzyskać nazwę kodową regionu, w którego znajduje się Twoje konto i do którego powinno zostać skierowane połączenie, można uruchomić następujący wiersz w [usłudze Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

```azurecli-interactive
az account list-locations
```

Po uruchomieniu wiersza pokazano powyżej, otrzymasz listę wszystkich regionów platformy Azure. Przejdź do regionu platformy Azure, który ma *nazwę wyświetlania,* której szukasz, i użyj jego wartości *nazwy* dla parametru **lokalizacji.**

Na przykład dla regionu platformy Azure West US 2 (wyświetlane poniżej), użyjesz "westus2" dla **parametru lokalizacji.**

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>Punkty końcowe  

Następujące punkty końcowe są ważne, aby wiedzieć, podczas łączenia się z kontami usługi Media Services z różnych krajowych chmur platformy Azure.

### <a name="global-azure"></a>Globalna platforma Azure

|Punkty końcowe||
| --- | --- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Uwierzytelnianie | `https://login.microsoftonline.com/` |
| Grupa odbiorców tokenów | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

|Punkty końcowe||
| --- | --- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Uwierzytelnianie | `https://login.microsoftonline.us/` |
| Grupa odbiorców tokenów | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure (Niemcy)

| Punkty końcowe ||
| --- | --- |  
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Uwierzytelnianie | `https://login.microsoftonline.de/` |
| Grupa odbiorców tokenów | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure w Chinach — 21Vianet

|Punkty końcowe||
| --- | --- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Uwierzytelnianie | `https://login.chinacloudapi.cn/` |
| Grupa odbiorców tokenów |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Zobacz też

* [Regiony platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Obszary geograficzne platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Następne kroki

[Omówienie usługi Media Services w wersji 3](media-services-overview.md)
