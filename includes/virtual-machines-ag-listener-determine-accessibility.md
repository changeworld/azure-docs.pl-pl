---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097724"
---
Należy weź pod uwagę, że istnieją dwa sposoby konfigurowania odbiornika grupy dostępności na platformie Azure. Sposoby różnią się w typie modułu równoważenia obciążenia platformy Azure, używane podczas tworzenia odbiornika. W poniższej tabeli opisano różnice:

| Typ modułu równoważenia obciążenia | Wdrażanie | Zastosowania: |
| --- | --- | --- |
| **Zewnętrzne** |Używa *publiczny wirtualny adres IP* usługi w chmurze, który jest hostem maszyny wirtualne (VM). |Należy otworzyć odbiornika z spoza sieci wirtualnej, łącznie z Internetu. |
| **Wewnętrzny** |Używa *wewnętrznego modułu równoważenia obciążenia* za pomocą prywatnego adresu dla odbiornika. |Możesz uzyskać dostęp do odbiornika tylko z w ramach tej samej sieci wirtualnej. Ten dostęp obejmuje sieci VPN typu lokacja lokacja w scenariuszach hybrydowych. |

> [!IMPORTANT]
> Aby uzyskać odbiornik, który korzysta z usługi w chmurze publicznych adresów VIP (zewnętrznym modułem równoważenia obciążenia), tak długo, jak klient odbiornik i bazy danych znajdują się w tym samym regionie platformy Azure, nie będą naliczane opłaty za ruch wychodzący. W przeciwnym razie wszelkich zwróconych przez odbiornik danych jest uznawany za ruch wychodzący i go jest naliczana normalna transferu danych. 
> 
> 

Wewnętrznym modułem równoważenia obciążenia można skonfigurować tylko w sieciach wirtualnych o zakresie regionalnym. Istniejące sieci wirtualne, które zostały skonfigurowane dla grupy koligacji nie można użyć wewnętrznego modułu równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [omówienie modułu równoważenia obciążenia wewnętrznego](../articles/load-balancer/load-balancer-internal-overview.md).

<!-- Update_Description: update meta properties -->