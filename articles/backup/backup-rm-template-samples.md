---
title: Szablony usługi Azure Resource Manager
description: Szablony usługi Azure Resource Manager do użytku z magazynami usług odzyskiwania i funkcjami usługi Azure Backup
ms.topic: sample
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 3c05f345c6c5df5d0350c75e52381c24b0609501
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74172179"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Szablony usługi Azure Resource Manager dla usługi Azure Backup

Poniższa tabela zawiera linki do szablonów usługi Azure Resource Manager używanych dla magazynów usługi Recovery Services i funkcji usługi Azure Backup. Aby poznać składnię i właściwości języka JSON, zobacz [Typy zasobów Microsoft.RecoveryServices](/azure/templates/microsoft.recoveryservices/allversions).

|   |   |
|---|---|
|**Magazyn usługi Recovery Services** | |
| [Tworzenie magazynu usług odzyskiwania](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Utwórz magazyn usługi Recovery Services. Magazynu można użyć dla usług Azure Backup i Azure Site Recovery. |
|**Tworzenie kopii zapasowych maszyn wirtualnych**| |
| [Tworzenie kopii zapasowych maszyn wirtualnych usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | Za pomocą istniejącego magazynu usługi Recovery Services i zasad usługi Backup można tworzyć kopie zapasowe maszyn wirtualnych usługi Resource Manager w tej samej grupie zasobów.|
| [Tworzenie kopii zapasowych maszyn wirtualnych infrastruktury IaaS w magazynie usługi Recovery Services](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Szablon tworzenia kopii zapasowej klasycznych maszyn wirtualnych i maszyn wirtualnych usługi Resource Manager. |
| [Tworzenie zasad cotygodniowego tworzenia kopii zapasowej dla maszyn wirtualnych infrastruktury IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | Szablon tworzy magazyn usług odzyskiwania i cotygodniowe zasady tworzenia kopii zapasowych, które są używane do tworzenia kopii zapasowych maszyn klasycznych i menedżera zasobów wirtualnych.|
| [Tworzenie zasad codziennego tworzenia kopii zapasowej dla maszyn wirtualnych infrastruktury IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | Szablon tworzy magazyn usług odzyskiwania i zasady codziennej kopii zapasowej, która służy do tworzenia kopii zapasowych maszyn klasycznych i menedżera zasobów wirtualnych.|
| [Wdrażanie maszyny wirtualnej z systemem Windows Server i włączoną kopią zapasową](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | Szablon umożliwia utworzenie maszyny wirtualnej z systemem Windows Server i magazynu usługi Recovery Services z włączonymi domyślnymi zasadami kopii zapasowej.|
|**Monitorowanie zadań tworzenia kopii zapasowych** |  |
| [Używanie dzienników usługi Azure Monitor za pomocą usługi Azure Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | Szablon wdraża dzienniki usługi Azure Monitor za pomocą usługi Azure Backup, która umożliwia monitorowanie zadań tworzenia kopii zapasowych i przywracania, alertów dotyczących tworzenia kopii zapasowych i magazynu w chmurze używanego w magazynach usług odzyskiwania.|  
|**1.** |  |
| [1.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) | Szablon tworzy przechowalnię usług odzyskiwania i zasady tworzenia kopii zapasowych specyficzne dla obciążenia. Rejestruje maszynę wirtualną z usługą Azure Backup i konfiguruje ochronę na tej maszynie wirtualnej. Obecnie działa tylko dla obrazów galerii SQL. |
|   |   |
