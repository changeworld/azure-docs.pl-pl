---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: be71b269e618d13a126e4005754b307e9c6517d7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66160049"
---
## <a name="azure-backup"></a>Azure Backup

Kopie zapasowe maszyn wirtualnych platformy Azure z obciążeń produkcyjnych, użyj usługi Azure Backup. Usługa Azure Backup obsługuje spójnych z aplikacją kopii zapasowych dla maszyn wirtualnych systemu Linux i Windows. Usługa Azure Backup tworzy punkty odzyskiwania przechowywane w geograficznie nadmiarowych magazynach odzyskiwania. Z punktu odzyskiwania można przywrócić całą maszynę wirtualną lub tylko poszczególne pliki. 

Proste, praktyczne wprowadzenie do usługi Azure Backup dla maszyn wirtualnych platformy Azure, zapoznaj się z samouczkiem "kopie zapasowe maszyn wirtualnych platformy Azure", dla [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) lub [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).

Aby uzyskać więcej informacji na temat działania usługi Azure Backup, zobacz [Planowanie infrastruktury kopii zapasowej maszyny Wirtualnej na platformie Azure](../articles/backup/backup-azure-vms-introduction.md)


## <a name="azure-site-recovery"></a>Azure Site Recovery

Usługa Azure Site Recovery chroni maszyny wirtualne od scenariusza awarii podczas całego regionu występują po awarii z powodu głównych klęski żywiołowe lub przerw w działaniu usługi powszechne. Można skonfigurować usługi Azure Site Recovery dla maszyn wirtualnych, dzięki czemu będzie można odzyskać aplikacji za pomocą jednego kliknięcia w ciągu kilku minut. Można replikować w wybranym regionie platformy Azure, nie jest ograniczona do połączonych w parę regionów. 

Próbne operacje odzyskiwania po awarii można uruchomić z na żądanie testu pracy w trybie Failover, bez wywierania wpływu na obciążenia produkcyjne ani na trwającą replikację. Utwórz plany odzyskiwania, aby organizować tryb failover i powrotu po awarii dla całej aplikacji działających na wielu maszynach wirtualnych. Funkcja planu odzyskiwania jest zintegrowana z elementami runbook usługi Azure automation.

Możesz rozpocząć od [replikowanie maszyn wirtualnych](https://aka.ms/a2a-getting-started). 

## <a name="managed-snapshots"></a>Zarządzane migawki 

W środowiskach tworzenia i testowania migawek zapewniają szybki i prosty opcji tworzenia kopii zapasowych maszyn wirtualnych, które korzystają z dysków zarządzanych. Migawki zarządzanej jest tylko do odczytu pełnej kopii dysku zarządzanego. Migawki istnieć niezależnie od dysku źródłowego i można tworzyć nowe dyski zarządzane odbudowywania maszyny Wirtualnej. Są one rozliczane oparte na użytej części dysku. Jeśli na przykład utworzysz migawkę dysku zarządzanego z zaprowizowaną pojemnością 64 GB i rzeczywistym użyciem danych 10 GB, migawka zostanie obciążona opłatami tylko za użyte dane o rozmiarze 10 GB.  

Aby uzyskać więcej informacji na temat tworzenia migawek zobacz:

* [Tworzenie kopii wirtualnego dysku twardego przechowywanej jako dysk zarządzany przy użyciu migawek w systemie Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Tworzenie kopii wirtualnego dysku twardego przechowywanej jako dysk zarządzany przy użyciu migawek w systemie Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>Kolejne kroki
Możesz wypróbować usługę Azure Backup, wykonując "Kopii zapasowych maszyn wirtualnych Windows w ramach samouczka" dla [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) lub [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).
