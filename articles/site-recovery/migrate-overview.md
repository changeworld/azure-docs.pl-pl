---
title: Migrowanie serwerów i maszyn wirtualnych na platformę Azure za pomocą usługi Azure Site Recovery
description: W tym artykule opisano sposób migracji lokalnych i maszyn wirtualnych usługi Azure IaaS na platformę Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: a7107eae5c798deb78d4d35eccdf4adcf5273335
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388903"
---
# <a name="about-migration"></a>Informacje o migracji

Przeczytaj ten artykuł, aby uzyskać krótkie omówienie sposobu, w jaki usługa [Azure Site Recovery](site-recovery-overview.md) pomaga migrować maszyny. 

> [!TIP]
> Teraz należy użyć usługi Migracji platformy Azure do migracji maszyn wirtualnych i serwerów na platformę Azure, zamiast usługi Azure Site Recovery. [Dowiedz się więcej](../migrate/migrate-services-overview.md).


Oto, co można przeprowadzić podczas migracji za pomocą funkcji Site Recovery:

- **Migrowanie z lokalnego na platformę Azure:** migrowanie lokalnych maszyn wirtualnych z funkcji Hyper V, maszyn wirtualnych vmware i serwerów fizycznych na platformę Azure. Po zakończeniu migracji obciążenia uruchomione na maszynach lokalnych będą działać na maszynach wirtualnych platformy Azure. 
- **Przeprowadzić migrację na platformie Azure**: przeprowadź migrację maszyn wirtualnych platformy Azure między regionami świadczenia usługi Azure. 
- **Przeprowadzić migrację usługi AWS**: przeprowadź migrację wystąpień usługi AWS dla systemu Windows do maszyn wirtualnych IaaS platformy Azure. 

> [!NOTE]
> Teraz można przeprowadzić migrację z lokalnego na platformę Azure przy użyciu usługi Azure Migrate. [Dowiedz się więcej](../migrate/migrate-overview.md).

## <a name="what-do-we-mean-by-migration"></a>Co mamy na myśli przez migrację?

Oprócz używania usługi Site Recovery do odzyskiwania po awarii lokalnych i maszyn wirtualnych platformy Azure można użyć usługi Site Recovery do ich migracji. Na czym polega różnica?

- W przypadku odzyskiwania po awarii regularnie replikujesz maszyny na platformie Azure. W przypadku awarii, można zakończyć się niepowodzeniem komputerów z lokacji głównej do dodatkowej lokacji platformy Azure i uzyskać do nich dostęp z tego miejsca. Gdy lokacja główna jest ponownie dostępna, powrót po awarii z platformy Azure.
- W przypadku migracji można replikować maszyny lokalne na platformie Azure lub maszyny wirtualne platformy Azure do regionu pomocniczego. Następnie niepowodzenie maszyny Wirtualnej z lokacji głównej do pomocniczego i zakończyć proces migracji. Nie ma powrotu po awarii.  


## <a name="migration-scenarios"></a> Scenariusze migracji

**Scenariusz** | **Szczegóły**
--- | ---
**Migrowanie z zasobów lokalnych na platformę Azure** | Lokalne maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper V i serwery fizyczne można migrować na platformę Azure. Aby to zrobić, należy wykonać prawie te same kroki, jak w przypadku pełnego odzyskiwania po awarii. Po prostu nie uchybień komputerów z powrotem z platformy Azure do lokacji lokalnej.
**Migrowanie między regionami platformy Azure** | Maszyny wirtualne platformy Azure można migrować z jednego regionu platformy Azure do innego. Po zakończeniu migracji można skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure teraz w regionie pomocniczym, do którego została zmigrowana.
**Migracja usługi AWS na platformę Azure** | Możesz zmigrować wystąpienia usługi AWS na maszyny wirtualne platformy Azure. Site Recovery traktuje wystąpienia AWS jako serwery fizyczne do celów migracji. 

## <a name="next-steps"></a>Następne kroki

- [Migrowanie maszyn lokalnych na platformę Azure](migrate-tutorial-on-premises-azure.md)
- [Migrowanie maszyn wirtualnych między regionami świadczenia usługi Azure](azure-to-azure-tutorial-migrate.md)
- [Migracja usługi AWS na platformę Azure](migrate-tutorial-aws-azure.md)
