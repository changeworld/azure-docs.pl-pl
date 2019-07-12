---
title: Przenoszenie maszyn wirtualnych platformy Azure do nowej subskrypcji lub grupy zasobów | Dokumentacja firmy Microsoft
description: Usługa Azure Resource Manager, aby przenieść maszyny wirtualne do nowej grupy zasobów lub subskrypcji.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 095ed1c8d2328b1eb391042125526696ba8cda49
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723547"
---
# <a name="move-guidance-for-virtual-machines"></a>Przenieś wskazówki dotyczące maszyn wirtualnych

W tym artykule opisano scenariusze, które nie są obecnie obsługiwane i kroki umożliwiające przeniesienie maszyn wirtualnych przy użyciu kopii zapasowej.

## <a name="scenarios-not-supported"></a>Scenariusze nieobsługiwane

Jeszcze nie są obsługiwane następujące scenariusze:

* Dyski zarządzane w strefach dostępności nie można przenieść do innej subskrypcji.
* Maszyn wirtualnych przy użyciu certyfikatu przechowywanego w usłudze Key Vault można przenieść do nowej grupy zasobów w tej samej subskrypcji, ale nie w subskrypcjach.
* Nie można przenieść Virtual Machine Scale Sets przy użyciu standardowych jednostek SKU modułu równoważenia obciążenia lub standardowego publicznego adresu IP jednostki SKU.
* Utworzona na podstawie zasobów w portalu Marketplace z planami dołączone maszyny wirtualne nie można przenosić między grupami zasobów lub subskrypcji. Anulowanie aprowizacji maszyny wirtualnej w bieżącej subskrypcji i Wdróż ponownie w nowej subskrypcji.
* Maszyny wirtualne w istniejącej sieci wirtualnej, ale nie są przenoszone wszystkich zasobów w sieci wirtualnej.

## <a name="virtual-machines-with-azure-backup"></a>Maszyny wirtualne za pomocą usługi Azure Backup

Aby przenieść maszyny wirtualne skonfigurowane przy użyciu usługi Azure Backup, należy użyć następującego obejścia:

* Znajdź lokalizację maszyny wirtualnej.
* Znajdź grupę zasobów, z następującym wzorcem nazewnictwa: `AzureBackupRG_<location of your VM>_1` na przykład AzureBackupRG_westus2_1
* Jeśli komputer znajduje się w witrynie Azure portal, a następnie sprawdź "Pokaż ukryte typy"
* Jeśli w programie PowerShell użyj `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` polecenia cmdlet
* Jeśli w interfejsu wiersza polecenia, użyj `az resource list -g AzureBackupRG_<location of your VM>_1`
* Znaleźć zasobu o typie `Microsoft.Compute/restorePointCollections` zawierający wzorzec nazewnictwa `AzureBackup_<name of your VM that you're trying to move>_###########`
* Usuń ten zasób. Ta operacja usuwa tylko punkty natychmiastowe odzyskiwanie, a nie dane kopii zapasowej w magazynie.
* Po zakończeniu usuwania magazynu i maszyn wirtualnych można przenieść do subskrypcji docelowej. Po przeniesieniu można kontynuować tworzenie kopii zapasowych bez utraty danych.
* Aby uzyskać informacji na temat przenoszenia magazynów usługi Recovery na potrzeby kopii zapasowych, zobacz [ograniczenia usług Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Następne kroki

Poleceń przenieść zasoby można znaleźć [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../resource-group-move-resources.md).
