---
title: Szyfrowanie dysków na maszynie wirtualnej z systemem Windows na platformie Azure | Microsoft Docs
description: Szyfrowanie dysków wirtualnych na maszynie wirtualnej z systemem Windows w celu zwiększenia bezpieczeństwa przy użyciu Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 87777d3a6abfeaeac74fd69126cc3e71e11be825
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597850"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Szyfrowanie dysków wirtualnych na maszynie wirtualnej z systemem Windows
W celu zapewnienia bezpieczeństwa i zgodności z ulepszoną maszyną wirtualną można zaszyfrować dyski wirtualne na platformie Azure. Dyski są szyfrowane przy użyciu kluczy kryptograficznych zabezpieczonych w Azure Key Vault. Można kontrolować te klucze kryptograficzne i przeprowadzać inspekcję ich użycia. W tym artykule opisano sposób szyfrowania dysków wirtualnych na maszynie wirtualnej z systemem Windows przy użyciu Azure PowerShell. Można także [szyfrować maszyny wirtualne z systemem Linux](../linux/disk-encryption-overview.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="overview-of-disk-encryption"></a>Omówienie szyfrowania dysków
Dyski wirtualne na maszynach wirtualnych z systemem Windows są szyfrowane przy użyciu funkcji BitLocker. Nie jest naliczana opłata za szyfrowanie dysków wirtualnych na platformie Azure. Klucze kryptograficzne są przechowywane w Azure Key Vault za pomocą ochrony oprogramowania lub można importować lub generować klucze w sprzętowych modułach zabezpieczeń (sprzętowych modułów zabezpieczeń) z certyfikatem standardu FIPS 140-2 Level 2. Klucze kryptograficzne są używane do szyfrowania i odszyfrowywania dysków wirtualnych dołączonych do maszyny wirtualnej. Zachowujesz kontrolę nad tymi kluczami kryptograficznymi i możesz przeprowadzać inspekcję ich użycia. 

Proces szyfrowania maszyny wirtualnej jest następujący:

1. Utwórz klucz kryptograficzny w Azure Key Vault.
1. Skonfiguruj klucz kryptograficzny, aby można go było używać na potrzeby szyfrowania dysków.
1. Włącz szyfrowanie dysków dla dysków wirtualnych.
1. Wymagane klucze kryptograficzne są żądane z Azure Key Vault.
1. Dyski wirtualne są szyfrowane przy użyciu podanego klucza kryptograficznego.


## <a name="requirements-and-limitations"></a>Wymagania i ograniczenia

Obsługiwane scenariusze i wymagania dotyczące szyfrowania dysków:

* Włączanie szyfrowania na nowych maszynach wirtualnych z systemem Windows z obrazów witryny Azure Marketplace lub niestandardowych obrazów dysków VHD.
* Włączanie szyfrowania na istniejących maszynach wirtualnych z systemem Windows na platformie Azure.
* Włączanie szyfrowania na maszynach wirtualnych z systemem Windows skonfigurowanych przy użyciu funkcji miejsca do magazynowania.
* Wyłączanie szyfrowania na dyskach systemu operacyjnego i danych dla maszyn wirtualnych z systemem Windows.
* Maszyny wirtualne w warstwie Standardowa, takie jak maszyny wirtualne z serii A, D, DS, G i GS.

    > [!NOTE]
    > Wszystkie zasoby (w tym Key Vault, konto magazynu i maszyny wirtualne) muszą znajdować się w tym samym regionie i subskrypcji platformy Azure.

Szyfrowanie dysków nie jest obecnie obsługiwane w następujących scenariuszach:

* Maszyny wirtualne w warstwie Podstawowa.
* Maszyny wirtualne utworzone przy użyciu klasycznego modelu wdrażania.
* Aktualizowanie kluczy kryptograficznych na już zaszyfrowanej maszynie wirtualnej.
* Integracja z lokalną usługą zarządzania kluczami.


## <a name="create-an-azure-key-vault-and-keys"></a>Tworzenie Azure Key Vault i kluczy
Przed rozpoczęciem upewnij się, że zainstalowano najnowszą wersję modułu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview). W poniższych przykładach poleceń Zastąp wszystkie przykładowe parametry własnymi nazwami, lokalizacją i wartościami klucza, takimi *jak* *myKeyVault*, *myVM*i tak dalej.

Pierwszym krokiem jest utworzenie Azure Key Vault do przechowywania kluczy kryptograficznych. Magazyny kluczy Azure mogą przechowywać klucze, wpisy tajne lub hasła, które umożliwiają bezpieczne wdrażanie ich w aplikacjach i usługach. W przypadku szyfrowania dysków wirtualnych utworzysz Key Vault do przechowywania klucza kryptograficznego, który jest używany do szyfrowania lub odszyfrowywania dysków wirtualnych. 

Włącz dostawcę Azure Key Vault w ramach subskrypcji platformy Azure za pomocą usługi [register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider), a następnie utwórz grupę zasobów za pomocą polecenie [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład umożliwia utworzenie grupy *zasobów nazwa zasobu* w lokalizacji *Wschodnie stany USA* :

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

Azure Key Vault przechowywania kluczy kryptograficznych i skojarzonych zasobów obliczeniowych, takich jak Storage i sama maszyna wirtualna, muszą znajdować się w tym samym regionie. Utwórz Azure Key Vault przy użyciu elementu [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) i Włącz Key Vault do użycia z szyfrowaniem dysków. Określ unikatową nazwę Key Vault dla nazwy *magazynu* kluczy w następujący sposób:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Klucze kryptograficzne można przechowywać przy użyciu ochrony oprogramowania lub sprzętowego modelu zabezpieczeń (HSM).  W standardzie Key Vault są przechowywane tylko klucze chronione przez oprogramowanie. Korzystanie z modułu HSM wymaga Key Vault Premium z dodatkowym kosztem. Aby utworzyć Key Vault w warstwie Premium, w poprzednim kroku Dodaj parametr *-SKU "Premium"* . W poniższym przykładzie użyto kluczy chronionych przez oprogramowanie od czasu utworzenia standardowej Key Vault. 

W przypadku obu modeli ochrony platforma Azure musi mieć udzielony dostęp, aby zażądać kluczy kryptograficznych podczas uruchamiania maszyny wirtualnej w celu odszyfrowania dysków wirtualnych. Utwórz klucz kryptograficzny w Key Vault za pomocą elementu [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey). Poniższy przykład tworzy klucz o nazwie *klucze*:

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Aby przetestować proces szyfrowania, Utwórz maszynę wirtualną przy użyciu polecenie [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* przy użyciu obrazu *systemu Windows Server 2016 Datacenter* . Gdy zostanie wyświetlony monit o podanie poświadczeń, wprowadź nazwę użytkownika i hasło, które mają być używane dla maszyny wirtualnej:

```azurepowershell-interactive
$cred = Get-Credential

New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-a-virtual-machine"></a>Szyfrowanie maszyny wirtualnej
Zaszyfruj maszynę wirtualną za pomocą polecenia [Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) przy użyciu klucza Azure Key Vault. Poniższy przykład pobiera wszystkie informacje o kluczu, a następnie szyfruje maszynę wirtualną o nazwie *myVM*:

```azurepowershell-interactive
$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Zaakceptuj monit, aby kontynuować szyfrowanie maszyny wirtualnej. Maszyna wirtualna jest uruchamiana ponownie w trakcie procesu. Po zakończeniu procesu szyfrowania i ponownym uruchomieniu maszyny wirtualnej Sprawdź stan szyfrowania za pomocą [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus):

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

Dane wyjściowe są podobne do poniższego przykładu:

```azurepowershell-interactive
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać więcej informacji na temat zarządzania Azure Key Vault, zobacz [konfigurowanie Key Vault dla maszyn wirtualnych](key-vault-setup.md).
* Aby uzyskać więcej informacji na temat szyfrowania dysków, takich jak przygotowanie zaszyfrowanej niestandardowej maszyny wirtualnej do przekazania do platformy Azure, zobacz [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).
