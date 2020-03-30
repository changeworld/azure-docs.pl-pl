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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183225"
---
## <a name="azure-backup"></a>Azure Backup

Aby wykonać kopię zapasową maszyn wirtualnych platformy Azure z uruchomionymi obciążeniami produkcyjnymi, użyj usługi Azure Backup. Usługa Azure Backup obsługuje kopie zapasowe spójne z aplikacjami zarówno dla maszyn wirtualnych z systemem Windows, jak i linux. Usługa Azure Backup tworzy punkty odzyskiwania przechowywane w geograficznie nadmiarowych magazynach odzyskiwania. Z punktu odzyskiwania można przywrócić całą maszynę wirtualną lub tylko poszczególne pliki. 

Aby zapoznać się z prostym, praktycznym wprowadzeniem do usługi Azure Backup for Azure VMs, zobacz samouczek "Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure" dla [systemu Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) lub [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).

Aby uzyskać więcej informacji na temat działania usługi Azure Backup, zobacz [Planowanie infrastruktury kopii zapasowej maszyny Wirtualnej na platformie Azure](../articles/backup/backup-azure-vms-introduction.md)


## <a name="azure-site-recovery"></a>Azure Site Recovery

Usługa Azure Site Recovery chroni maszyny wirtualne przed poważnym scenariuszem awarii, gdy w całym regionie wystąpi awaria spowodowana poważną klęską żywiołową lub powszechną przerwą w świadczeniu usług. Można skonfigurować usługę Azure Site Recovery dla maszyn wirtualnych, dzięki czemu można odzyskać aplikację za pomocą jednego kliknięcia w ciągu kilku minut. Można replikować do wybranego regionu platformy Azure, nie jest ograniczona do sparowanych regionów. 

Można uruchomić ćwiczenia odzyskiwania po awarii z testami na żądanie, bez wpływu na obciążenia produkcyjne lub ciągłą replikację. Tworzenie planów odzyskiwania do organizowania pracy awaryjnej i powrotu po awarii całej aplikacji uruchomionej na wielu maszynach wirtualnych. Funkcja planu odzyskiwania jest zintegrowana z elementami runbook automatyzacji platformy Azure.

Można rozpocząć, [replikując maszyny wirtualne](https://aka.ms/a2a-getting-started). 

## <a name="managed-snapshots"></a>Migawki zarządzane 

W środowiskach deweloperskich i testowych migawki zapewniają szybką i prostą opcję tworzenia kopii zapasowych maszyn wirtualnych korzystających z dysków zarządzanych. Migawka zarządzana jest pełną kopią dysku zarządzanego tylko do odczytu. Migawki istnieją niezależnie od dysku źródłowego i mogą służyć do tworzenia nowych dysków zarządzanych do odbudowy maszyny Wirtualnej. Są one rozliczane na podstawie używanej części dysku. Na przykład jeśli utworzysz migawkę dysku zarządzanego o aprowizowanym pojemności 64 GB i rzeczywistym rozmiarze używanych danych 10 GB, migawka będzie rozliczana tylko dla używanego rozmiaru danych 10 GB.  

Aby uzyskać więcej informacji na temat tworzenia migawek, zobacz:

* [Tworzenie kopii wirtualnego dysku twardego przechowywanej jako dysk zarządzany przy użyciu migawek w systemie Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Tworzenie kopii wirtualnego dysku twardego przechowywanej jako dysk zarządzany przy użyciu migawek w systemie Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>Następne kroki
Możesz wypróbować usługę Azure Backup, wykonując "Tworzenie kopii zapasowych maszyn wirtualnych systemu Windows" dla [systemu Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) lub [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).
