---
title: Szyfrowanie dysków na maszynie Wirtualnej systemu Windows na platformie Azure
description: Szyfrowanie dysków wirtualnych na maszynie Wirtualnej systemu Windows w celu zwiększenia bezpieczeństwa przy użyciu programu Azure PowerShell
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
ms.openlocfilehash: 096fe779c077424b01df3ead5965cb799866a03b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033528"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Szyfrowanie dysków wirtualnych na maszynie Wirtualnej systemu Windows
Aby zwiększyć bezpieczeństwo i zgodność maszyny wirtualnej, dyski wirtualne na platformie Azure mogą być szyfrowane. Dyski są szyfrowane przy użyciu kluczy kryptograficznych, które są zabezpieczone w magazynie kluczy azure. Można kontrolować te klucze kryptograficzne i można inspekcji ich użycia. W tym artykule opisano sposób szyfrowania dysków wirtualnych na maszynie Wirtualnej systemu Windows przy użyciu programu Azure PowerShell. Można również [szyfrować maszyny wirtualne Systemu Linux](../linux/disk-encryption-overview.md).

 

## <a name="overview-of-disk-encryption"></a>Omówienie szyfrowania dysku
Dyski wirtualne na maszynach wirtualnych systemu Windows są szyfrowane w spoczynku przy użyciu funkcji BitLocker. Szyfrowanie dysków wirtualnych na platformie Azure jest bezpłatne. Klucze kryptograficzne są przechowywane w magazynie kluczy platformy Azure przy użyciu ochrony oprogramowania lub można importować lub generować klucze w sprzętowych modułach zabezpieczeń (HSM) certyfikowanych zgodnie ze standardami FIPS 140-2 poziomu 2. Klucze kryptograficzne służą do szyfrowania i odszyfrowywania dysków wirtualnych dołączonych do maszyny Wirtualnej. Zachowujesz kontrolę nad tymi kluczami kryptograficznymi i możesz kontrolować ich użycie. 

Proces szyfrowania maszyny Wirtualnej jest następujący:

1. Utwórz klucz kryptograficzny w magazynie kluczy platformy Azure.
1. Skonfiguruj klucz kryptograficzny, który będzie można uzyskać do szyfrowania dysków.
1. Włącz szyfrowanie dysków wirtualnych.
1. Wymagane klucze kryptograficzne są wymagane z usługi Azure Key Vault.
1. Dyski wirtualne są szyfrowane przy użyciu dostarczonego klucza kryptograficznego.


## <a name="requirements-and-limitations"></a>Wymagania i ograniczenia

Obsługiwane scenariusze i wymagania dotyczące szyfrowania dysku:

* Włączanie szyfrowania na nowych maszynach wirtualnych systemu Windows z obrazów witryny Azure Marketplace lub niestandardowych obrazów wirtualnych.
* Włączanie szyfrowania na istniejących maszynach wirtualnych z systemem Windows na platformie Azure.
* Włączanie szyfrowania na maszynach wirtualnych systemu Windows skonfigurowanych przy użyciu funkcji Miejsca do magazynowania.
* Wyłączenie szyfrowania na systemach operacyjnych i dyskach danych dla maszyn wirtualnych z systemem Windows.
* Maszyny wirtualne warstwy standardowej, takie jak maszyny wirtualne z serii A, D, DS, G i GS.

    > [!NOTE]
    > Wszystkie zasoby (w tym konto usługi Key Vault, Storage i VM) muszą znajdować się w tym samym regionie i subskrypcji platformy Azure.

Szyfrowanie dysku nie jest obecnie obsługiwane w następujących scenariuszach:

* Maszyny wirtualne warstwy podstawowej.
* Maszyny wirtualne utworzone przy użyciu modelu wdrażania klasycznego.
* Aktualizowanie kluczy kryptograficznych na już zaszyfrowanej maszynie wirtualnej.
* Integracja z lokalną usługą zarządzania kluczami.


## <a name="create-an-azure-key-vault-and-keys"></a>Tworzenie skarbca i kluczy usługi Azure
Przed rozpoczęciem upewnij się, że zainstalowano najnowszą wersję modułu Programu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview). W poniższych przykładach polecenia zastąp wszystkie przykładowe parametry własnymi nazwami, lokalizacją i wartościami kluczy, takimi jak *myResourceGroup*, *myKeyVault*, *myVM*i tak dalej.

Pierwszym krokiem jest utworzenie usługi Azure Key Vault do przechowywania kluczy kryptograficznych. Usługi Azure Key Vaults mogą przechowywać klucze, wpisy tajne lub hasła, które umożliwiają bezpieczne implementowanie ich w aplikacjach i usługach. W przypadku szyfrowania dysków wirtualnych utworzysz magazyn kluczy do przechowywania klucza kryptograficznego używanego do szyfrowania lub odszyfrowywania dysków wirtualnych. 

Włącz dostawcę usługi Azure Key Vault w ramach subskrypcji platformy Azure za pomocą [programu Register-AzResourceProvider,](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)a następnie utwórz grupę zasobów za pomocą [programu New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) Poniższy przykład tworzy nazwę grupy zasobów *myResourceGroup* w lokalizacji *wschodnich stanów USA:*

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

Usługa Azure Key Vault przechowująca klucze kryptograficzne i skojarzone zasoby obliczeniowe, takie jak magazyn i sama maszyna wirtualna, musi znajdować się w tym samym regionie. Utwórz usługę Azure Key Vault za pomocą [funkcji New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) i włącz magazyn kluczy do użycia z szyfrowaniem dysku. Określ unikatową nazwę magazynu kluczy dla *keyVaultName* w następujący sposób:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Klucze kryptograficzne można przechowywać przy użyciu ochrony oprogramowania lub modelu zabezpieczeń sprzętowych (HSM).  Standardowy magazyn kluczy przechowuje tylko klucze chronione programem. Korzystanie z modułu HSM wymaga usługi Premium Key Vault za dodatkową opłatą. Aby utworzyć magazyn kluczy premium, w poprzednim kroku dodaj parametr *-Sku "Premium".* W poniższym przykładzie użyto kluczy chronionych programowo, ponieważ utworzyliśmy standardowy magazyn kluczy. 

W przypadku obu modeli ochrony platforma platformy Azure musi mieć dostęp do żądania kluczy kryptograficznych, gdy maszyna wirtualna zostanie uruchomiona w celu odszyfrowania dysków wirtualnych. Utwórz klucz kryptograficzny w magazynie kluczy za pomocą [funkcji Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey). Poniższy przykład tworzy klucz o nazwie *myKey:*

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Aby przetestować proces szyfrowania, utwórz maszynę wirtualną z [new-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* przy użyciu obrazu *centrum danych systemu Windows Server 2016.* Po wyświetleniu monitu o podanie poświadczeń wprowadź nazwę użytkownika i hasło, które mają być używane dla maszyny Wirtualnej:

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
Szyfruj maszynę wirtualną za pomocą [funkcji Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) przy użyciu klucza usługi Azure Key Vault. Poniższy przykład pobiera wszystkie informacje o kluczu, a następnie szyfruje maszynę wirtualną o nazwie *myVM:*

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

Zaakceptuj monit, aby kontynuować szyfrowanie maszyny Wirtualnej. Maszyna wirtualna uruchamia się ponownie podczas procesu. Po zakończeniu procesu szyfrowania i ponownym uruchomieniu maszyny Wirtualnej przejrzyj stan szyfrowania za pomocą [get-AzVmDiskEncryptionStatus:](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus)

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
* Aby uzyskać więcej informacji na temat zarządzania magazynem kluczy platformy Azure, zobacz [Konfigurowanie magazynu kluczy dla maszyn wirtualnych.](key-vault-setup.md)
* Aby uzyskać więcej informacji na temat szyfrowania dysku, takich jak przygotowywanie zaszyfrowanej niestandardowej maszyny Wirtualnej do przekazania na platformę Azure, zobacz [Szyfrowanie dysków platformy Azure](../../security/fundamentals/encryption-overview.md).
