---
title: Szablony usługi Azure Resource Manager dla usługi Azure Backup
description: Przykłady programu PowerShell dla usługi Azure Backup
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: bed2c002cacdac1e47852e6fa3181885af6733d2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236804"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Szablony usługi Azure Resource Manager dla usługi Azure Backup

Poniższa tabela zawiera linki do szablonów usługi Azure Resource Manager używanych dla magazynów usługi Recovery Services i funkcji usługi Azure Backup. Aby poznać składnię i właściwości języka JSON, zobacz [Typy zasobów Microsoft.RecoveryServices](/azure/templates/microsoft.recoveryservices/allversions).

|   |   |
|---|---|
|**Magazyn usługi Recovery Services** | |
| [Tworzenie magazynu usługi Recovery Services](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Utwórz magazyn usługi Recovery Services. Magazynu można użyć dla usług Azure Backup i Azure Site Recovery. |
|**Tworzenie kopii zapasowych maszyn wirtualnych**| |
| [Tworzenie kopii zapasowych maszyn wirtualnych usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | Za pomocą istniejącego magazynu usługi Recovery Services i zasad usługi Backup można tworzyć kopie zapasowe maszyn wirtualnych usługi Resource Manager w tej samej grupie zasobów.|
| [Tworzenie kopii zapasowych maszyn wirtualnych infrastruktury IaaS w magazynie usługi Recovery Services](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Szablon tworzenia kopii zapasowej klasycznych maszyn wirtualnych i maszyn wirtualnych usługi Resource Manager. |
| [Tworzenie zasad cotygodniowego tworzenia kopii zapasowej dla maszyn wirtualnych infrastruktury IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | Szablon umożliwia utworzenie magazynu usługi Recovery Services i zasad kopii zapasowych co tydzień, który jest używany do tworzenia kopii zapasowych maszyn z klasycznej i wirtualną usługi Resource Manager.|
| [Tworzenie zasad codziennego tworzenia kopii zapasowej dla maszyn wirtualnych infrastruktury IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | Szablon umożliwia utworzenie magazynu usługi Recovery Services i codzienne zasad kopii zapasowych, który jest używany do tworzenia kopii zapasowych maszyn z klasycznej i wirtualną usługi Resource Manager.|
| [Wdrażanie maszyny wirtualnej z systemem Windows Server i włączoną kopią zapasową](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | Szablon umożliwia utworzenie maszyny wirtualnej z systemem Windows Server i magazynu usługi Recovery Services z włączonymi domyślnymi zasadami kopii zapasowej.|
|**Monitorowanie zadań kopii zapasowej** |  |
| [Używanie dzienników usługi Azure Monitor za pomocą usługi Azure Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | Szablon umożliwia wdrożenie usługi Azure Monitor dzienników przy użyciu usługi Azure Backup, co pozwala na monitorowanie kopii zapasowych i przywracania, zadania, alertów kopii zapasowych i magazynu w chmurze używanego w Magazyny usługi Recovery Services.|  
|**Tworzenie kopii zapasowych programu SQL Server na maszynie Wirtualnej platformy Azure** |  |
| [Tworzenie kopii zapasowych programu SQL Server na maszynie Wirtualnej platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) | Szablon umożliwia utworzenie magazynu usługi Recovery Services i zasady tworzenia kopii zapasowych z określonego obciążenia. Rejestruje maszyny Wirtualnej za pomocą usługi Azure Backup i konfiguruje ochronę tej maszyny Wirtualnej. Obecnie usługa działa tylko dla obrazów z galerii programu SQL. |
|   |   |
