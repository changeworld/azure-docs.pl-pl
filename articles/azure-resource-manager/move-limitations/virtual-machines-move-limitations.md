---
title: Przenieś Virtual Machines platformy Azure do nowej subskrypcji lub grupy zasobów | Microsoft Docs
description: Użyj Azure Resource Manager, aby przenieść maszyny wirtualne do nowej grupy zasobów lub subskrypcji.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tomfitz
ms.openlocfilehash: 443d6f2bcbb61d9106b079a4e63c48bb433d19c6
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286741"
---
# <a name="move-guidance-for-virtual-machines"></a>Wskazówki dotyczące przenoszenia maszyn wirtualnych

W tym artykule opisano scenariusze, które nie są obecnie obsługiwane, oraz kroki umożliwiające przeniesienie maszyn wirtualnych przy użyciu kopii zapasowej.

## <a name="scenarios-not-supported"></a>Scenariusze nie są obsługiwane

Następujące scenariusze nie są jeszcze obsługiwane:

* Nie można przenieść Managed Disks w Strefy dostępności do innej subskrypcji.
* Nie można przenieść Virtual Machine Scale Sets ze standardową jednostką SKU Load Balancer lub z publicznym adresem IP standardowej jednostki SKU.
* Nie można przenosić maszyn wirtualnych utworzonych z zasobów witryny Marketplace z dołączonymi planami między grupami zasobów lub subskrypcjami. Usuń obsługę administracyjną maszyny wirtualnej w bieżącej subskrypcji i Wdróż ją ponownie w nowej subskrypcji.
* Nie można przenieść maszyn wirtualnych w istniejącej sieci wirtualnej do nowej subskrypcji, jeśli nie przeniesiesz wszystkich zasobów w sieci wirtualnej.
* Nie można przenosić maszyn wirtualnych o niskim priorytecie i zestawów skalowania maszyn wirtualnych o niskim priorytecie między grupami zasobów lub subskrypcjami.
* Maszyn wirtualnych w zestawie dostępności nie można przenieść pojedynczo.

## <a name="virtual-machines-with-azure-backup"></a>Maszyny wirtualne z Azure Backup

Aby przenieść maszyny wirtualne skonfigurowane przy użyciu Azure Backup, należy użyć następującego obejścia:

* Znajdź lokalizację maszyny wirtualnej.
* Znajdź grupę zasobów o następującym wzorcu nazewnictwa: `AzureBackupRG_<location of your VM>_1` na przykład AzureBackupRG_westus2_1
* Jeśli w Azure Portal, zaznacz pole wyboru "Pokaż ukryte typy"
* Jeśli w programie PowerShell, użyj polecenia cmdlet `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1`
* Jeśli w interfejsie wiersza polecenia, użyj `az resource list -g AzureBackupRG_<location of your VM>_1`
* Znajdź zasób z typem `Microsoft.Compute/restorePointCollections`, który ma wzorzec nazewnictwa `AzureBackup_<name of your VM that you're trying to move>_###########`
* Usuń ten zasób. Ta operacja usuwa tylko natychmiastowe punkty odzyskiwania, a nie kopię zapasową danych w magazynie.
* Po zakończeniu usuwania można przenieść magazyn i maszynę wirtualną do subskrypcji docelowej. Po przeniesieniu można kontynuować wykonywanie kopii zapasowych bez utraty danych.
* Informacje o przenoszeniu magazynów usługi Recovery Service dla kopii zapasowej znajdują się w temacie [Recovery Services ograniczenia](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać polecenia przenoszenia zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../resource-group-move-resources.md).
