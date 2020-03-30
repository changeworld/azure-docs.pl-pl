---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 3295c7e677e4c9bf7bff27614fe5fa4fa3200570
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334624"
---
| Zasób | Limit |
| --- | --- |
| [Role sieci Web lub pracownika na wdrożenie](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| [Wejściowe punkty końcowe wystąpienia](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) na wdrożenie |25 |
| [Wejściowe punkty końcowe](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) na wdrożenie |25 |
| [Wewnętrzne punkty końcowe](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) na wdrożenie |25 |
| [Hostowane certyfikaty usług](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) na wdrożenie |199 |

<sup>1.</sup> Każda usługa Azure Cloud Service z rolami sieci web lub procesu roboczego może mieć dwa wdrożenia, jeden dla środowiska produkcyjnego i jeden dla przemieszczania. Ten limit odnosi się do liczby różnych ról, czyli konfiguracji. Ten limit nie odwołuje się do liczby wystąpień na rolę, czyli skalowania.

