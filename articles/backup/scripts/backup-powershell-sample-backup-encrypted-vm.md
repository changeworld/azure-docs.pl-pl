---
title: Azure przykładowy skrypt programu PowerShell — tworzenie kopii zapasowej maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Azure przykładowy skrypt programu PowerShell — tworzenie kopii zapasowej maszyny wirtualnej platformy Azure
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
tags: ''
ms.assetid: ''
ms.service: backup
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/07/2017
ms.author: markgal
ms.custom: mvc
ms.openlocfilehash: 4376add4a2e51806bd5db228ad2fe2afcf2e4f57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23842650"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Tworzenie kopii zapasowej zaszyfrowanego maszyny wirtualnej platformy Azure przy użyciu programu PowerShell

Ten skrypt tworzy magazyn usług odzyskiwania z magazynu geograficznie nadmiarowego (GRS) dla zaszyfrowanych maszyny wirtualnej platformy Azure. Domyślne zasady ochrony jest zastosowane do magazynu. Zasada generuje tworzenia codziennej kopii zapasowej maszyny wirtualnej i zachowuje każdej kopii zapasowej przez 30 dni. Skrypt również wyzwala początkowego punktu odzyskiwania dla maszyny wirtualnej i przechowuje tego punktu odzyskiwania 365 dni. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszyny Wirtualnej i wszystkie powiązane zasoby.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Wyjaśnienie skryptu

Ten skrypt używa następujących poleceń w celu utworzenia wdrożenia. Każdy element w tabeli łącza do dokumentacji określonego polecenia.


| Polecenie | Uwagi | 
|---|---| 
| [Nowe AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Tworzy grupę zasobów, w którym przechowywane są wszystkie zasoby. | 
| [Nowe AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/New-AzureRmRecoveryServicesVault) | Tworzy magazyn usług odzyskiwania do przechowywania kopii zapasowych. | 
| [Zestaw AzureRmRecoveryServicesBackupProperties](/powershell/module/azurerm.recoveryservices/Set-AzureRmRecoveryServicesBackupProperties) | Zestawy kopii zapasowych właściwości magazynu na magazyn usług odzyskiwania. | 
| [Nowe AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)| Tworzy zasady ochrony za pomocą zasad harmonogramu i zasady przechowywania w magazynie usług odzyskiwania. | 
| [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) | Ustawia uprawnienia Key Vault w celu udzielenia dostępu główną usługi do kluczy szyfrowania. | 
| [Włącz AzureRmRecoveryServicesBackupProtection](/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection) | Umożliwia tworzenie kopii zapasowej dla elementu z określonym zasady ochrony dla kopii zapasowej. | 
| [Zestaw AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy)| Modyfikuje istniejące zasady ochrony kopii zapasowej. | 
| [AzureRmRecoveryServicesBackupItem kopii zapasowej](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem) | Uruchamia kopii zapasowej dla chronionego elementu kopia zapasowa Azure, która nie jest powiązany z harmonogramem tworzenia kopii zapasowej. |
| [AzureRmRecoveryServicesBackupJob oczekiwania](/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob) | Czeka na zakończenie zadania tworzenia kopii zapasowej Azure. | 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Usuwa grupę zasobów i wszystkie zasoby zawarte w ciągu. | 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących modułu Azure PowerShell, zobacz [dokumentacji programu Azure PowerShell](/powershell/azure/overview).

