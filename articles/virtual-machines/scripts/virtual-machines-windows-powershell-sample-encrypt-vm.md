---
title: Przykładowy skrypt programu Azure PowerShell — szyfrowanie maszyny wirtualnej z systemem Windows | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell — szyfrowanie maszyny wirtualnej z systemem Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: iainfou
ms.openlocfilehash: f405cdaf61d6aaafa8568a9d7f21614071285c17
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="encrypt-a-windows-virtual-machine-with-azure-powershell"></a>Szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu programu Azure PowerShell

Ten skrypt tworzy bezpieczny magazyn Azure Key Vault, klucze szyfrowania, jednostkę usługi Azure Active Directory i maszynę wirtualną z systemem Windows. Maszyna wirtualna jest następnie szyfrowana przy użyciu klucza szyfrowania z magazynu Key Vault i poświadczeń jednostki usługi.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/virtual-machine/encrypt-vm/encrypt-windows-vm.ps1 "Encrypt VM disks")]

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
| [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) | Tworzy magazyn Azure Key Vault do przechowywania zabezpieczonych danych, takich jak klucze szyfrowania. |
| [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) | Tworzy klucz szyfrowania w magazynie Key Vault. |
| [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) | Tworzy jednostkę usługi Azure Active Directory używaną do bezpiecznego uwierzytelniania i sterowania dostępem do kluczy szyfrowania. |
| [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) | Ustawia uprawnienia w magazynie Key Vault w celu udzielenia jednostce usługi dostępu do kluczy szyfrowania. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Tworzy maszynę wirtualną i łączy ją z kartą sieciową, siecią wirtualną, podsiecią i sieciową grupą zabezpieczeń. To polecenie otwiera również port 80 i ustawia poświadczenia administracyjne. |
| [Get-AzureRmKeyVault](/powershell/module/azurerm.keyvault/get-azurermkeyvault) | Pobiera wymagane informacje dotyczące magazynu Key Vault |
| [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) | Włącza szyfrowanie na maszynie wirtualnej przy użyciu poświadczeń jednostki usługi i klucza szyfrowania. |
| [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) | Wyświetla stan procesu szyfrowania maszyny wirtualnej. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Usuwa grupę zasobów i wszystkie zasoby w niej zawarte. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell na potrzeby maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
