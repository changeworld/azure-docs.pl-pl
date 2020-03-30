---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183184"
---
Ważne jest, aby uświadomić sobie, że istnieją dwa sposoby konfigurowania odbiornika grupy dostępności na platformie Azure. Sposoby różnią się typem modułu równoważenia obciążenia platformy Azure używane podczas tworzenia odbiornika. W poniższej tabeli opisano różnice:

| Typ modułu równoważenia obciążenia | Wdrażanie | Zastosowania: |
| --- | --- | --- |
| **Zewnętrznych** |Używa *publicznego wirtualnego adresu IP* usługi w chmurze obsługującej maszyny wirtualne (maszyny wirtualne). |Należy uzyskać dostęp do odbiornika spoza sieci wirtualnej, w tym z Internetu. |
| **Wewnętrznego** |Używa *wewnętrznego modułu równoważenia obciążenia* z adresem prywatnym dla odbiornika. |Dostęp do odbiornika można uzyskać tylko z tej samej sieci wirtualnej. Ten dostęp obejmuje sieć VPN typu lokacja-lokacja w scenariuszach hybrydowych. |

> [!IMPORTANT]
> Dla odbiornika, który używa publicznego adresu VIP usługi w chmurze (zewnętrznego modułu równoważenia obciążenia), tak długo, jak klient, odbiornik i bazy danych znajdują się w tym samym regionie platformy Azure, nie będzie ponosić opłat wychodzących. W przeciwnym razie wszelkie dane zwracane za pośrednictwem odbiornika jest uważany za wyjście i jest naliczana z normalnymi szybkości transferu danych. 
> 
> 

Równoważenie obciążenia sieciowego można skonfigurować tylko w sieciach wirtualnych o zakresie regionalnym. Istniejące sieci wirtualne, które zostały skonfigurowane dla grupy koligacji, nie mogą używać równoważenia obciążenia sieciowego. Aby uzyskać więcej informacji, zobacz [Omówienie wewnętrznego modułu równoważenia obciążenia](../articles/load-balancer/load-balancer-internal-overview.md).

