---
title: Tworzenie i szyfrowanie maszyny Wirtualnej systemu Linux za pomocą programu Azure Powershell
description: W tym przewodniku Szybki start dowiesz się, jak używać programu Azure Powershell do tworzenia i szyfrowania maszyny wirtualnej systemu Linux
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: a2cb8919ac0752c42f22e064d6201c7120fbc9b6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78970547"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-in-azure-with-azure-powershell"></a>Szybki start: tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux na platformie Azure za pomocą programu Azure PowerShell

Moduł Azure PowerShell umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi za pomocą wiersza polecenia programu PowerShell lub skryptów. Ten przewodnik Szybki start pokazuje, jak używać modułu programu Azure PowerShell do tworzenia maszyny wirtualnej systemu Linux (VM), tworzenia magazynu kluczy do przechowywania kluczy szyfrowania i szyfrowania maszyny wirtualnej. Ten szybki start używa obrazu rynku Ubuntu 16.04 LTS firmy Canonical i Standard_D2S_V3 rozmiaru maszyny Wirtualnej. 

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi:

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną platformy Azure z [new-AzVM](/powershell/module/az.compute/new-azvm), przekazując do niej obiekt konfiguracji maszyny Wirtualnej utworzony powyżej.

```powershell-interactive
$cred = = Get-Credential

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image Canonical:UbuntuServer:16.04-LTS:latest -Size Standard_D2S_V3
```

Wdrożenie maszyny wirtualnej potrwa kilka minut. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Tworzenie magazynu kluczy skonfigurowanych dla kluczy szyfrowania

Szyfrowanie dysków platformy Azure przechowuje swój klucz szyfrowania w magazynie azure key vault. Utwórz magazyn kluczy za pomocą [programu New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Aby włączyć magazyn kluczy do przechowywania kluczy szyfrowania, należy użyć parametru -EnabledForDiskEncryption.

> [!Important]
> Każdy magazyn kluczy musi mieć nazwę, która jest unikatowa na platformie Azure. W poniższych przykładach zastąp <> nazwa unikatowa-keyvault na wybraną nazwę.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>Szyfruj maszynę wirtualną

Zaszyfruj maszynę wirtualną za pomocą [funkcji Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Set-AzVmDiskEncryptionExtension wymaga pewnych wartości z obiektu Przechowalnia kluczy. Wartości te można uzyskać, przekazując unikatową nazwę magazynu kluczy do [programu Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```azurepowershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -SkipVmBackup -VolumeType All
```

Po kilku minutach proces zwróci następujące elementy:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

Proces szyfrowania można zweryfikować, uruchamiając [get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus).

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Gdy szyfrowanie jest włączone, na zwróconym wyjściu zostaną wyświetlone następujące informacje:

```
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono maszynę wirtualną, utworzono magazyn kluczy, który był włączony dla kluczy szyfrowania, i zaszyfrowano maszynę wirtualną.  Przejdź do następnego artykułu, aby dowiedzieć się więcej o szyfrowaniu dysków platformy Azure dla maszyn wirtualnych z systemem Linux.

> [!div class="nextstepaction"]
> [Omówienie szyfrowania dysków platformy Azure](disk-encryption-overview.md)