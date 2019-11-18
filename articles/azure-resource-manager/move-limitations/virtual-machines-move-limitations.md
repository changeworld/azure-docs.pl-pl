---
title: Przenoszenie maszyn wirtualnych platformy Azure do nowej subskrypcji lub grupy zasobów
description: Użyj Azure Resource Manager, aby przenieść maszyny wirtualne do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: b03e9618e621216f74cb02828183df7ee6b502ea
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150936"
---
# <a name="move-guidance-for-virtual-machines"></a>Wskazówki dotyczące przenoszenia maszyn wirtualnych

W tym artykule opisano scenariusze, które nie są obecnie obsługiwane, oraz kroki umożliwiające przeniesienie maszyn wirtualnych przy użyciu kopii zapasowej.

## <a name="scenarios-not-supported"></a>Scenariusze nie są obsługiwane

Następujące scenariusze nie są jeszcze obsługiwane:

* Nie można przenieść Managed Disks w Strefy dostępności do innej subskrypcji.
* Nie można przenieść Virtual Machine Scale Sets ze standardową jednostką SKU Load Balancer lub z publicznym adresem IP standardowej jednostki SKU.
* Utworzona na podstawie zasobów w portalu Marketplace z planami dołączone maszyny wirtualne nie można przenosić między grupami zasobów lub subskrypcji. Usuń obsługę administracyjną maszyny wirtualnej w bieżącej subskrypcji i Wdróż ją ponownie w nowej subskrypcji.
* Nie można przenieść maszyn wirtualnych w istniejącej sieci wirtualnej do nowej subskrypcji, jeśli nie przeniesiesz wszystkich zasobów w sieci wirtualnej.
* Nie można przenosić maszyn wirtualnych o niskim priorytecie i zestawów skalowania maszyn wirtualnych o niskim priorytecie między grupami zasobów lub subskrypcjami.
* Maszyn wirtualnych w zestawie dostępności nie można przenieść pojedynczo.

## <a name="virtual-machines-with-azure-backup"></a>Maszyny wirtualne z Azure Backup

Aby przenieść maszyny wirtualne skonfigurowane przy użyciu Azure Backup, należy użyć następującego obejścia:

* Znajdź lokalizację maszyny wirtualnej.
* Znajdź grupę zasobów o następującym wzorcu nazewnictwa: `AzureBackupRG_<location of your VM>_1` na przykład AzureBackupRG_westus2_1
* Jeśli komputer znajduje się w witrynie Azure portal, a następnie sprawdź "Pokaż ukryte typy"
* Jeśli w programie PowerShell użyj `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` polecenia cmdlet
* Jeśli w interfejsu wiersza polecenia, użyj `az resource list -g AzureBackupRG_<location of your VM>_1`
* Znajdź zasób z typem `Microsoft.Compute/restorePointCollections`, który ma wzorzec nazewnictwa `AzureBackup_<name of your VM that you're trying to move>_###########`
* Usuń ten zasób. Ta operacja usuwa tylko natychmiastowe punkty odzyskiwania, a nie kopię zapasową danych w magazynie.
* Po zakończeniu usuwania można przenieść magazyn i maszynę wirtualną do subskrypcji docelowej. Po przeniesieniu można kontynuować wykonywanie kopii zapasowych bez utraty danych.
* Informacje o przenoszeniu magazynów usługi Recovery Service dla kopii zapasowej znajdują się w temacie [Recovery Services ograniczenia](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać polecenia przenoszenia zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../resource-group-move-resources.md).
