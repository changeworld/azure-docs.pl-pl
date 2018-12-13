---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: eb1fe7f83ed83efe078be0aadf26cc7db6f498e2
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52886266"
---
| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| [Role sieć Web/proces roboczy na wdrożenie](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Wystąpienie danych wejściowych punktów końcowych](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) na wdrożenie |25 |25 |
| [Wejściowe punkty końcowe](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) na wdrożenie |25 |25 |
| [Wewnętrznych punktów końcowych](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) na wdrożenie |25 |25 |
| [Certyfikaty usług hostowanych](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) na wdrożenie |199 |199 |

<sup>1</sup>każdej usługi w chmurze przy użyciu ról sieć Web/proces roboczy może mieć dwa wdrożenia, jeden dla środowiska produkcyjnego i jeden dla przemieszczania. Należy również zauważyć, że ten limit odwołuje się do liczby różne role (Konfiguracja) i nie liczbę wystąpień każdej roli (skalowanie).

