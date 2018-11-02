---
title: Szyfrowanie dysków na maszynie Wirtualnej Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Jak zaszyfrować dyski wirtualne na maszynie Wirtualnej Windows w celu uzyskania zwiększonych zabezpieczeń przy użyciu programu Azure PowerShell
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
ms.openlocfilehash: 28bb4c9fd4827f534c5f7bac2bae15451e3ca16c
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50914706"
---
# <a name="how-to-encrypt-virtual-disks-on-a-windows-vm"></a>Jak zaszyfrować dyski wirtualne na maszyny Wirtualnej z systemem Windows
Rozszerzone maszynę wirtualną (VM), zabezpieczeń i zgodności mogą być szyfrowane dyski wirtualne na platformie Azure. Dyski są szyfrowane przy użyciu kluczy kryptograficznych, które są zabezpieczone w usłudze Azure Key Vault. Możesz kontrolować klucze kryptograficzne i przeprowadzać ich inspekcje ich użycie. W tym artykule szczegółowo przedstawiono sposób Szyfruj dyski wirtualne na maszynie Wirtualnej Windows przy użyciu programu Azure PowerShell. Możesz również [szyfrowanie maszyny Wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure](../linux/encrypt-disks.md).

## <a name="overview-of-disk-encryption"></a>Omówienie szyfrowania dysku

Dyski wirtualne na maszynach wirtualnych Windows są szyfrowane za pomocą funkcji Bitlocker. Nie ma opłat do szyfrowania dysków wirtualnych na platformie Azure. Klucze szyfrowania są przechowywane w usłudze Azure Key Vault przy użyciu ochrony oprogramowania lub można importować lub generować klucze w sprzętowych modułach zabezpieczeń (HSM) certyfikowane zgodnych ze standardami FIPS 140-2 poziom 2 standardy. Te klucze szyfrowania są używane do szyfrowania i odszyfrowywania dyski wirtualne dołączone do maszyny Wirtualnej. Zachowanie kontroli nad te klucze kryptograficzne i przeprowadzać ich inspekcje ich użycie. 

Proces szyfrowania maszyny Wirtualnej jest w następujący sposób:

1. Utwórz klucz kryptograficzny w usłudze Azure Key Vault.
1. Konfigurowanie klucza kryptograficznego, może być używany do szyfrowania dysków.
1. Włącz szyfrowanie dysków dla dysków wirtualnych.
1. Wymagane klucze szyfrowania są żądane w usłudze Azure Key Vault.
1. Wirtualne dyski są szyfrowane przy użyciu podanego klucza kryptograficznego.

### <a name="azure-key-vault"></a>W usłudze Azure Key Vault

Szyfrowanie dysków opiera się na [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). Usługa Key Vault jest używany do ochrony kluczy kryptograficznych i wpisów tajnych używanych dla procesu szyfrowania i odszyfrowywania dysku. 
  * Jeśli istnieje, można użyć istniejącej usługi Azure Key Vault. Nie masz możliwości szyfrowania dysków za przeznaczyć usługi Key Vault.
  * Do oddzielania granice administracyjne i widoczność kluczy, można utworzyć dedykowane usługi Key Vault.


## <a name="requirements-and-limitations"></a>Wymagania i ograniczenia

Obsługiwane scenariusze i wymagania dotyczące szyfrowania dysku:

* Włączanie szyfrowania na nowe Windows maszyn wirtualnych na podstawie obrazów portalu Azure Marketplace lub niestandardowego obrazu wirtualnego dysku twardego.
* Włączanie szyfrowania na istniejących Windows maszyn wirtualnych na platformie Azure.
* Włączanie szyfrowania na maszynach wirtualnych Windows, które są skonfigurowane przy użyciu funkcji miejsca do magazynowania.
* Wyłączenie szyfrowania systemu operacyjnego i danych dysków dla maszyn wirtualnych Windows.
* Wszystkie zasoby (na przykład usługi Key Vault, konto magazynu oraz maszyny Wirtualnej) muszą być w tym samym regionie platformy Azure i subskrypcji.
* W warstwie standardowa maszyny wirtualne, takie jak, D, DS, G i GS maszyny wirtualne z serii.

Szyfrowanie dysku nie jest obecnie obsługiwane w następujących scenariuszach:

* Warstwa podstawowa maszyn wirtualnych.
* Maszyny wirtualne utworzone przy użyciu klasycznego modelu wdrażania.
* Aktualizowanie kluczy kryptograficznych na już zaszyfrowanej maszyny Wirtualnej.
* Integracja z lokalnej usługi zarządzania kluczami.

## <a name="create-azure-key-vault-and-keys"></a>Tworzenie usługi Azure Key Vault i klucze

Przed rozpoczęciem upewnij się, że masz zainstalowaną najnowszą wersję modułu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview). W przykładach poleceń Zastąp wszystkie parametry przykład własne nazwy, lokalizacji i wartości klucza. W poniższych przykładach użyto Konwencji *myResourceGroup*, *myKeyVault*, *myVM*itp.

Pierwszym krokiem jest do utworzenia usługi Azure Key Vault do przechowywania kluczy kryptograficznych. Usługa Azure Key Vault można przechowywać klucze, wpisy tajne lub haseł, które umożliwiają bezpieczne wdrożenie ich w aplikacji i usług. Do szyfrowania dysku wirtualnego utworzysz usługę Key Vault do przechowywania klucza kryptograficznego, który służy do szyfrowania i odszyfrowywania Twoje wirtualne dyski. 

Włącz dostawcę usługi Azure Key Vault w ramach subskrypcji platformy Azure za pomocą [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider), należy utworzyć grupę zasobów za pomocą [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Poniższy przykład tworzy nazwę grupy zasobów *myResourceGroup* w *wschodnie stany USA* lokalizacji:

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzureRmResourceGroup -Location $location -Name $rgName
```

Usługi Azure Key Vault, zawierający klucze kryptograficzne i zasoby obliczeniowe skojarzone, takie jak storage i samej maszyny Wirtualnej muszą znajdować się w tym samym regionie. Tworzenie usługi Azure Key Vault przy użyciu [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) i włączyć usługi Key Vault do użytku z szyfrowania dysków. Określ unikatową nazwę usługi Key Vault *keyVaultName* w następujący sposób:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzureRmKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Można przechowywać klucze szyfrowania przy użyciu oprogramowania lub ochrona sprzętu modelu zabezpieczeń (HSM). Przy użyciu sprzętowego modułu zabezpieczeń wymaga usługi Key Vault w wersji premium. Brak dodatkowych kosztów, do tworzenia premium usługi Key Vault, a nie standardowy usługi Key Vault, który przechowuje klucze chronione programowo. Aby utworzyć premium usługi Key Vault, w poprzednim kroku należy dodać *- jednostki Sku "Premium"* parametrów. W poniższym przykładzie użyto kluczy chronionych programowo, ponieważ utworzyliśmy standardowe usługi Key Vault. 

Oba modele ochrony platformy Azure musi otrzymać dostęp do zażądać kluczy kryptograficznych w przypadku, gdy maszyna wirtualna zostanie do odszyfrowania dysków wirtualnych. Utwórz klucz kryptograficzny w usłudze Key Vault przy użyciu [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey). Poniższy przykład tworzy klucz o nazwie *klucze*:

```azurepowershell-interactive
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej
Aby przetestować proces szyfrowania, Utwórz Maszynę wirtualną za pomocą [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* przy użyciu *systemu Windows Server 2016 Datacenter* obrazu. Po wyświetleniu monitu o poświadczenia, wprowadź nazwę użytkownika i hasło do użycia dla maszyny Wirtualnej:

```azurepowershell-interactive
$cred = Get-Credential

New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-virtual-machine"></a>Szyfrowanie maszyny wirtualnej
Aby zaszyfrować dyski wirtualne, możesz Połącz poprzednie składniki:

1. Określ nazwy głównej usługi Azure Active Directory i hasło.
2. Określ usługi Key Vault do przechowywania metadanych dla zaszyfrowanych dysków.
3. Określ klucze kryptograficzne służące do rzeczywistego szyfrowania i odszyfrowywania.
4. Określ, czy chcesz zaszyfrować dysk systemu operacyjnego, dyski z danymi lub wszystkie.

Szyfrowanie maszyny Wirtualnej za pomocą [polecenia Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) przy użyciu klucza usługi Azure Key Vault i poświadczenia nazwy głównej usługi Azure Active Directory. Poniższy przykład pobiera wszystkie informacje o kluczu, a następnie szyfruje maszyny Wirtualnej o nazwie *myVM*:

```azurepowershell-interactive
$keyVault = Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Zaakceptować monit o kontynuowanie przy użyciu szyfrowania maszyny Wirtualnej. Maszyna wirtualna zostanie uruchomiony ponownie podczas procesu. Po zakończeniu procesu szyfrowania i maszyny Wirtualnej został ponownie uruchomiony, sprawdź stan szyfrowania z [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus):

```azurepowershell-interactive
Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
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
