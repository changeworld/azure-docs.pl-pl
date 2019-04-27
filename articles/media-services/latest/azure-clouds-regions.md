---
title: Chmury i regionów, w których usługa Azure Media Services jest dostępna w wersji 3 | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje o chmury platformy Azure i regionów, w których usługa Azure Media Services jest dostępna w wersji 3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/17/2019
ms.author: juliako
ms.openlocfilehash: 4f8851248c395a1f03c46490c8eb5e71221dd133
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733304"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Chmury i regionów, w których usługa Azure Media Services v3 istnieje

Azure Media Services v3 jest dostępny za pośrednictwem usługi Azure Resource Manager manifest w globalnej platformy Azure, Azure Government, Azure (Niemcy), Azure China 21Vianet. Jednak nie wszystkie funkcje usługi Media Services są dostępne w chmurach platformy Azure. W tym dokumencie przedstawiono availabilities główne składniki programu Media Services v3.

## <a name="feature-availability-in-azure-clouds"></a>Dostępność funkcji w innych chmurach platformy Azure

| Cecha|Globalnych regionów platformy Azure | Azure Government|Azure (Niemcy)|Azure w Chinach — 21Vianet|
| --- | --- | --- | --- | --- |
| [Usługa Azure EventGrid](reacting-to-media-services-events.md) | Dostępne | Niedostępne | Niedostępne | Niedostępne |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Dostępne | Niedostępne | Niedostępne | Niedostępne |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Dostępne | Niedostępne | Niedostępne | Niedostępne |
| [StandardEncoderPreset](encoding-concept.md) | Dostępne | Dostępne | Dostępne | Dostępne |
| [LiveEvents](live-streaming-overview.md) | Dostępne | Dostępne | Dostępne | Dostępne |
| [Punkty](streaming-endpoint-concept.md) | Dostępne | Dostępne | Dostępne | Dostępne |

## <a name="regionsgeographieslocations"></a>Regiony/lokalizacje geograficzne/lokalizacji

* [Regiony platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/)
* [Lokalizacje geograficzne platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/)

### <a name="region-code-name"></a>Nazwa kodu regionu 

Kiedy należy podać **lokalizacji** parametru, musisz podać nazwę kod regionu jako **lokalizacji** wartość. Aby uzyskać nazwę kod regionu Twoje konto jest w i wywołania powinny być kierowane do, można uruchomić następujący wiersz w [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

```bash
az account list-locations
```

Po uruchomieniu wiersz powyżej, zostanie wyświetlona lista wszystkich regionów świadczenia usługi Azure. Przejdź do regionu platformy Azure, która ma *displayName* szukasz i używać jej *nazwa* wartość **lokalizacji** parametru.

Na przykład dla regionu platformy Azure zachodnie stany USA 2 (wyświetlane poniżej), użytkownik użyje "westus2" **lokalizacji** parametru.

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

Następujące punkty końcowe są pamiętać podczas nawiązywania połączenia konta usługi Media Services z różnych krajowych chmur platformy Azure.

### <a name="global-azure"></a>Globalna platforma Azure

|Punkty końcowe ||
| --- | --- | 
| Azure Resource Manager |  `https://management.azure.com/` |
| Authentication | `https://login.microsoftonline.com/` | 
| Odbiorców tokenu | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

|Punkty końcowe||
| --- | --- | 
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Authentication | `https://login.microsoftonline.us/` | 
| Odbiorców tokenu | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure (Niemcy)

| Punkty końcowe ||
| --- | --- |  
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Authentication | `https://login.microsoftonline.de/` |
| Odbiorców tokenu | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure w Chinach — 21Vianet

|Punkty końcowe||
| --- | --- | 
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Authentication | `https://login.chinacloudapi.cn/` |
| Odbiorców tokenu |  `https://management.core.chinacloudapi.cn/` |

## <a name="next-steps"></a>Kolejne kroki

[Przegląd usługi Media Services v3](media-services-overview.md)
