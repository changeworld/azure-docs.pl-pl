---
title: Szyfrowanie dysków na maszynie Wirtualnej Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Szyfrowanie dysków wirtualnych na maszynie Wirtualnej Windows w celu uzyskania zwiększonych zabezpieczeń przy użyciu programu Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 4ef485bb91fe52e138b805f347e729fc4097fc7c
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431093"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Szyfrowanie dysków wirtualnych w maszyny Wirtualnej z systemem Windows
Rozszerzone maszynę wirtualną (VM), zabezpieczeń i zgodności mogą być szyfrowane dyski wirtualne na platformie Azure. Dyski są szyfrowane przy użyciu kluczy kryptograficznych, które są zabezpieczone w usłudze Azure Key Vault. Możesz kontrolować klucze kryptograficzne i przeprowadzać ich inspekcje ich użycie. W tym artykule opisano sposób szyfrowania dysków wirtualnych w maszyny Wirtualnej z systemem Windows przy użyciu programu Azure PowerShell. Możesz również [szyfrowania maszyny Wirtualnej z systemem Linux przy użyciu wiersza polecenia platformy Azure](../linux/encrypt-disks.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="overview-of-disk-encryption"></a>Omówienie szyfrowania dysku
Dyski wirtualne na maszynach wirtualnych Windows są szyfrowane w stanie spoczynku przy użyciu funkcji BitLocker. Nie ma opłat do szyfrowania dysków wirtualnych na platformie Azure. Klucze szyfrowania są przechowywane w usłudze Azure Key Vault przy użyciu ochrony oprogramowania lub można importować lub generować klucze w sprzętowych modułach zabezpieczeń (HSM) certyfikowane zgodnych ze standardami FIPS 140-2 poziom 2 standardy. Klucze szyfrowania są używane do szyfrowania i odszyfrowywania dyski wirtualne dołączone do maszyny Wirtualnej. Sprawowanie kontroli nad te klucze kryptograficzne i przeprowadzać ich inspekcje ich użycie. 

Proces szyfrowania maszyny Wirtualnej jest w następujący sposób:

1. Utwórz klucz kryptograficzny w usłudze Azure Key Vault.
1. Konfigurowanie klucza kryptograficznego, może być używany do szyfrowania dysków.
1. Włącz szyfrowanie dysków dla dysków wirtualnych.
1. Wymagane klucze szyfrowania są żądane w usłudze Azure Key Vault.
1. Wirtualne dyski są szyfrowane przy użyciu podanego klucza kryptograficznego.


## <a name="requirements-and-limitations"></a>Wymagania i ograniczenia

Obsługiwane scenariusze i wymagania dotyczące szyfrowania dysku:

* Włączanie szyfrowania na nowe Windows maszyn wirtualnych na podstawie obrazów portalu Azure Marketplace lub niestandardowych obrazów wirtualnego dysku twardego.
* Włączanie szyfrowania na istniejących Windows maszyn wirtualnych na platformie Azure.
* Włączanie szyfrowania na maszynach wirtualnych Windows, które są konfigurowane przy użyciu funkcji miejsca do magazynowania.
* Wyłączenie szyfrowania systemu operacyjnego i danych dysków dla maszyn wirtualnych Windows.
* W warstwie standardowa maszyny wirtualne, takie jak, D, DS, G i GS maszyny wirtualne z serii.

    > [!NOTE]
    > Wszystkie zasoby (w tym usługi Key Vault, konto magazynu i maszyn wirtualnych) musi być w tym samym regionie platformy Azure i subskrypcji.

Szyfrowanie dysków nie jest obecnie obsługiwane w następujących scenariuszach:

* Warstwa podstawowa maszyn wirtualnych.
* Maszyny wirtualne tworzone za pomocą klasycznego modelu wdrażania.
* Aktualizowanie kluczy kryptograficznych na już zaszyfrowanej maszyny Wirtualnej.
* Integracja z lokalną usługą zarządzania kluczami.


## <a name="create-an-azure-key-vault-and-keys"></a>Tworzenie usługi Azure Key Vault i klucze
Przed rozpoczęciem upewnij się, że zainstalowano najnowszą wersję modułu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview). W poniższych przykładach poleceń Zastąp wszystkie parametry przykład własne nazwy, lokalizacji i wartości kluczy, takie jak *myResourceGroup*, *myKeyVault*, *myVM*, i itd.

Pierwszym krokiem jest do utworzenia usługi Azure Key Vault do przechowywania kluczy kryptograficznych. Usługa Azure Key Vault można przechowywać klucze, wpisy tajne lub haseł, które umożliwiają bezpieczne wdrożenie ich w aplikacji i usług. Do szyfrowania dysku wirtualnego utworzysz usługę Key Vault do przechowywania klucza kryptograficznego, który służy do szyfrowania i odszyfrowywania Twoje wirtualne dyski. 

Włącz dostawcę usługi Azure Key Vault w ramach subskrypcji platformy Azure za pomocą [AzResourceProvider rejestru](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider), następnie utwórz grupę zasobów za pomocą [AzResourceGroup nowy](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład tworzy nazwę grupy zasobów *myResourceGroup* w *wschodnie stany USA* lokalizacji:

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

Usługi Azure Key Vault, klucze kryptograficzne i zasoby obliczeniowe skojarzone, takie jak storage i samej maszyny Wirtualnej musi być w tym samym regionie. Tworzenie usługi Azure Key Vault przy użyciu [New AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) i włączyć usługi Key Vault do użytku z szyfrowania dysków. Określ unikatową nazwę usługi Key Vault *keyVaultName* w następujący sposób:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Można przechowywać klucze kryptograficzne przy użyciu oprogramowania lub ochrona sprzętu modelu zabezpieczeń (HSM).  Standardowa usługi Key Vault przechowuje tylko klucze chronione programowo. Korzystanie z modułu HSM wymaga premium usługi Key Vault bez dodatkowych kosztów. Aby utworzyć premium usługi Key Vault, w poprzednim kroku należy dodać *- jednostki Sku "Premium"* parametru. W poniższym przykładzie użyto kluczy chronionych programowo, ponieważ utworzyliśmy standardowe usługi Key Vault. 

Oba modele ochrony platformy Azure musi otrzymać dostęp do zażądać kluczy kryptograficznych w przypadku, gdy maszyna wirtualna zostanie do odszyfrowania dysków wirtualnych. Utwórz klucz kryptograficzny w usłudze Key Vault przy użyciu [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey). Poniższy przykład tworzy klucz o nazwie *klucze*:

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Aby przetestować proces szyfrowania, Utwórz Maszynę wirtualną za pomocą [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* przy użyciu *systemu Windows Server 2016 Datacenter* obrazu. Po wyświetleniu monitu o poświadczenia, wprowadź nazwę użytkownika i hasło do użycia dla maszyny Wirtualnej:

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
Szyfrowanie maszyny Wirtualnej za pomocą [AzVMDiskEncryptionExtension zestaw](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) za pomocą klucza usługi Azure Key Vault. Poniższy przykład pobiera wszystkie informacje o kluczu, a następnie szyfruje maszyny Wirtualnej o nazwie *myVM*:

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

Zaakceptować monit o kontynuowanie przy użyciu szyfrowania maszyny Wirtualnej. Maszyna wirtualna zostanie uruchomiony ponownie podczas procesu. Po zakończeniu procesu szyfrowania i maszyny Wirtualnej został ponownie uruchomiony, sprawdź stan szyfrowania z [Get AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus):

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

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać więcej informacji na temat zarządzania usługi Azure Key Vault, zobacz [Konfigurowanie usługi Key Vault dla maszyn wirtualnych](key-vault-setup.md).
* Aby uzyskać więcej informacji o szyfrowaniu dysku, takich jak przygotowywanie zaszyfrowanych niestandardową maszynę Wirtualną do przekazania na platformę Azure, zobacz [usługi Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
