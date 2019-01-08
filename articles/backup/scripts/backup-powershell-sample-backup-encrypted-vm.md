---
title: Przykładowy skrypt programu Azure PowerShell — tworzenie kopii zapasowej maszyny wirtualnej platformy Azure | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell — tworzenie kopii zapasowej maszyny wirtualnej platformy Azure
services: backup
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
editor: ''
tags: ''
ms.assetid: ''
ms.service: backup
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/07/2017
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: eb22dc88c971e0ddc293fabd64bfd30145b2edd1
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2018
ms.locfileid: "53651388"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Tworzenie zaszyfrowanej kopii zapasowej maszyny wirtualnej platformy Azure za pomocą programu PowerShell

Ten skrypt umożliwia utworzenie magazynu usługi Recovery Services z magazynem geograficznie nadmiarowym (GRS) dla zaszyfrowanej maszyny wirtualnej platformy Azure. Względem magazynu są stosowane domyślne zasady ochrony. Te zasady powodują generowanie kopii zapasowej maszyny wirtualnej codziennie oraz przechowywanie każdej kopii zapasowej przez 30 dni. Skrypt wyzwala również początkowy punkt odzyskiwania dla maszyny wirtualnej i przechowuje ten punkt odzyskiwania przez 365 dni. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia wdrożenia. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.


| Polecenie | Uwagi | 
|---|---| 
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. | 
| [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/New-AzureRmRecoveryServicesVault) | Tworzy magazyn usług Recovery Services w celu przechowywania kopii zapasowych. | 
| [Set-AzureRmRecoveryServicesBackupProperties](/powershell/module/azurerm.recoveryservices/Set-AzureRmRecoveryServicesBackupProperties) | Określa właściwości magazynu kopii zapasowych w magazynie usług Recovery Services. | 
| [New-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)| Tworzy zasady ochrony, używając zasad planowania i zasad przechowywania, w magazynie usług Recovery Services. | 
| [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) | Ustawia uprawnienia w magazynie Key Vault w celu udzielenia jednostce usługi dostępu do kluczy szyfrowania. | 
| [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection) | Umożliwia utworzenie kopii zapasowej elementu za pomocą określonych zasad ochrony kopii zapasowych. | 
| [Set-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy)| Modyfikuje istniejące zasady ochrony kopii zapasowych. | 
| [Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem) | Uruchamia tworzenie kopii zapasowej chronionego elementu usługi Azure Backup, który nie jest związany z harmonogramem tworzenia kopii zapasowych. |
| [Wait-AzureRmRecoveryServicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob) | Czeka na zakończenie zadania usługi Azure Backup. | 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Usuwa grupę zasobów i wszystkie zasoby w niej zawarte. | 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

