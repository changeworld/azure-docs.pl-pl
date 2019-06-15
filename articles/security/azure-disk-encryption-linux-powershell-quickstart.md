---
title: Tworzenie i szyfrowanie maszyny Wirtualnej systemu Linux przy użyciu programu Azure Powershell
description: W tym przewodniku Szybki Start dowiesz się, jak używać programu Azure Powershell do tworzenia i szyfrowanie maszyny wirtualnej systemu Linux
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 7c44ffefccd4d78713d29aa2f9ab6006b8ba819e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081483"
---
# <a name="quickstart-create-and-encrypt-a-linux-virtual-machine-in-azure-with-powershell"></a>Szybki start: Tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu programu PowerShell

Moduł Azure PowerShell umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi za pomocą wiersza polecenia programu PowerShell lub skryptów. Ten przewodnik Szybki Start dowiesz się, jak utworzyć maszynę wirtualną systemu Linux (VM), tworzenie usługi Key Vault do przechowywania kluczy szyfrowania i szyfrowanie maszyny Wirtualnej za pomocą modułu Azure PowerShell. Ten przewodnik Szybki Start używa obrazu portalu marketplace Ubuntu 16.04 LTS od firmy Canonical i rozmiar Standard_D2S_V3 maszyny Wirtualnej. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Tworzenie maszyny wirtualnej platformy Azure za pomocą [New-AzVM](/powershell/module/az.compute/new-azvm), przekazując do niej konfiguracji maszyny Wirtualnej utworzoną powyżej obiektu.

```powershell
$securePassword = ConvertTo-SecureString 'AZUREuserPA$$W0RD' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image Canonical:UbuntuServer:16.04-LTS:latest  -Size Standard_D2S_V3
```

Wdrożenie maszyny wirtualnej potrwa kilka minut. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Tworzenie usługi Key Vault skonfigurowane dla kluczy szyfrowania

Usługa Azure disk encryption przechowuje klucz szyfrowania w usłudze Azure Key Vault. Tworzenie magazynu kluczy przy użyciu [nowe AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Aby włączyć usługi Key Vault do przechowywania kluczy szyfrowania, użyj parametru - EnabledForDiskEncryption.

> [!Important]
> Każda usługa Key Vault musi mieć unikatową nazwę. Poniższy przykład obejmuje tworzenie usługi Key Vault o nazwie *myKV*, ale musisz nazwać należy do Ciebie coś innego.

```powershell
New-AzKeyvault -name MyKV -ResourceGroupName myResourceGroup -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>Szyfrowanie maszyny wirtualnej

Szyfrowanie maszyny Wirtualnej za pomocą [AzVmDiskEncryptionExtension zestaw](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Zestaw AzVmDiskEncryptionExtension wymaga ustawienia niektórych wartości z obiektu usługi Key Vault. Te wartości można uzyskać przez przekazanie unikatowa nazwa magazynu kluczy w celu [Get AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```powershell
$KeyVault = Get-AzKeyVault -VaultName MyKV -ResourceGroupName MyResourceGroup

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName MyVM -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -SkipVmBackup -VolumeType All
```

Po kilku minutach proces zwróci następujące wartości:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

Proces szyfrowania można sprawdzić, uruchamiając [Get AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus).

```powershell
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Gdy jest włączone szyfrowanie, zobaczysz następujące dane wyjściowe:

```
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```powershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start utworzono maszynę wirtualną, utworzyć magazyn kluczy, którego zostały włączone dla kluczy szyfrowania, a następnie zaszyfrowanych maszyn wirtualnych.  Przejdź do następnego artykułu, aby dowiedzieć się więcej na temat wymagań wstępnych usługi Azure Disk Encryption dotyczących maszyn wirtualnych IaaS.

> [!div class="nextstepaction"]
> [Wymagania wstępne usługi Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)