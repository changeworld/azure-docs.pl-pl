---
title: Przenoszenie maszyn wirtualnych platformy Azure do nowej subskrypcji lub grupy zasobów
description: Usługa Azure Resource Manager umożliwia przenoszenie maszyn wirtualnych do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 97c49f90dab2aafd89de322e57ad44ff1fc9d367
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75479763"
---
# <a name="move-guidance-for-virtual-machines"></a>Wskazówki dotyczące przenoszenia dla maszyn wirtualnych

W tym artykule opisano scenariusze, które nie są obecnie obsługiwane i kroki, aby przenieść maszyny wirtualne z kopią zapasową.

## <a name="scenarios-not-supported"></a>Scenariusze nie są obsługiwane

Następujące scenariusze nie są jeszcze obsługiwane:

* Dysków zarządzanych w strefach dostępności nie można przenieść do innej subskrypcji.
* Nie można przenosić zestawów skalowania maszyny wirtualnej ze standardowym modułem równoważenia obciążenia jednostki SKU lub publicznego adresu IP standardowej jednostki SKU.
* Maszyn wirtualnych utworzonych z zasobów portalu Marketplace z dołączonymi planami nie można przenosić między grupami zasobów lub subskrypcjami. De-aprowizowania maszyny wirtualnej w bieżącej subskrypcji i wdrożyć ponownie w nowej subskrypcji.
* Nie można przenieść maszyn wirtualnych w istniejącej sieci wirtualnej do nowej subskrypcji, jeśli nie przenosisz wszystkich zasobów w sieci wirtualnej.
* Nie można przenosić maszyn wirtualnych o niskim priorytecie i zestawów skalowania maszyn wirtualnych o niskim priorytecie między grupami zasobów lub subskrypcjami.
* Maszyny wirtualne w zestawie dostępności nie można przenosić indywidualnie.

## <a name="virtual-machines-with-azure-backup"></a>Maszyny wirtualne z pomocą kopii zapasowej platformy Azure

Aby przenieść maszyny wirtualne skonfigurowane za pomocą usługi Azure Backup, należy użyć następującego obejścia:

* Znajdź lokalizację maszyny wirtualnej.
* Znajdź grupę zasobów z następującym `AzureBackupRG_<location of your VM>_1` wzorcem nazewnictwa: na przykład AzureBackupRG_westus2_1
* Jeśli w witrynie Azure portal, a następnie zaznacz "Pokaż ukryte typy"
* Jeśli w programie PowerShell użyj polecenia `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdlet
* Jeśli w cli, należy użyć`az resource list -g AzureBackupRG_<location of your VM>_1`
* Znajdowanie zasobu `Microsoft.Compute/restorePointCollections` o typie, który ma wzorzec nazewnictwa`AzureBackup_<name of your VM that you're trying to move>_###########`
* Usuń ten zasób. Ta operacja usuwa tylko punkty odzyskiwania błyskawiczne, a nie dane kopii zapasowej w magazynie.
* Po zakończeniu usuwania można przenieść magazyn i maszynę wirtualną do subskrypcji docelowej. Po przeprowadzce można kontynuować tworzenie kopii zapasowych bez utraty danych.
* Aby uzyskać informacje dotyczące przenoszenia magazynów usługi odzyskiwania do tworzenia kopii zapasowych, zobacz [Ograniczenia usług odzyskiwania](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać polecenia przenoszenia zasobów, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../move-resource-group-and-subscription.md).
