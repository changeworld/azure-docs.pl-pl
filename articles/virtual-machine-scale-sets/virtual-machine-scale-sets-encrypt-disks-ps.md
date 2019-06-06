---
title: Szyfrowanie dysków dla zestawów skalowania platformy Azure przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak szyfrowanie wystąpień maszyn wirtualnych i dołączonych dysków w zestawie skalowania maszyn wirtualnych Windows za pomocą programu Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: cynthn
ms.openlocfilehash: a582a4787a4b215d82dcbff60be8853793f92c32
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66728365"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Szyfrowanie systemu operacyjnego i dołączonych dysków z danymi w maszynie wirtualnej zestawu skalowania przy użyciu programu Azure PowerShell

Aby chronić pliki i chronić dane magazynowane przy użyciu technologii szyfrowania standardowych w branży, zestawy skalowania maszyn wirtualnych obsługują szyfrowania dysków Azure (ADE). Szyfrowanie można włączyć dla maszyny wirtualnej Windows i Linux zestawów skalowania. Aby uzyskać więcej informacji, zobacz [Azure szyfrowania dysku dla Windows i Linux](../security/azure-security-disk-encryption.md).

Usługa Azure disk encryption jest obsługiwane:
- Skalowanie zestawów utworzonych za pomocą dysków zarządzanych i nie jest obsługiwane dla zestawów skalowania dysku natywnego (lub niezarządzanego).
- w przypadku woluminów systemu operacyjnego i danych w zestawach skalowania Windows. Wyłącz szyfrowanie jest obsługiwane w przypadku woluminów systemu operacyjnego i danych dla zestawów skalowania Windows.
- woluminy danych w zestawach skalowania systemu Linux. Szyfrowanie dysków systemu operacyjnego nie jest obsługiwana w chwili obecnej dla zestawów skalowania systemu Linux.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Tworzenie usługi Azure Key Vault umożliwia szyfrowania dysków

Usługa Azure Key Vault można przechowywać klucze, wpisy tajne lub haseł, które umożliwiają bezpieczne wdrożenie ich w aplikacji i usług. Klucze szyfrowania są przechowywane w usłudze Azure Key Vault przy użyciu ochrony oprogramowania lub można importować lub generować klucze w sprzętowych modułach zabezpieczeń (HSM) certyfikowane zgodnych ze standardami FIPS 140-2 poziom 2 standardy. Te klucze szyfrowania są używane do szyfrowania i odszyfrowywania dyski wirtualne dołączone do maszyny Wirtualnej. Zachowanie kontroli nad te klucze kryptograficzne i przeprowadzać ich inspekcje ich użycie.

Tworzenie magazynu kluczy przy użyciu [nowe AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Aby umożliwić usługi Key Vault ma być używany do szyfrowania dysku, należy ustawić *EnabledForDiskEncryption* parametru. W poniższym przykładzie zdefiniowano także zmienne dla nazwy grupy zasobów, nazwa usługi Key Vault i lokalizacji. Podaj swoją własną unikatową nazwę usługi Key Vault:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Użyj istniejącego magazynu kluczy

Ten krok jest tylko wymagane, jeśli masz istniejące usługi Key Vault, którą chcesz za pomocą szyfrowania dysków. Pomiń ten krok, jeśli magazyn kluczy został utworzony w poprzedniej sekcji.

Można włączyć istniejącego magazynu kluczy w tej samej subskrypcji i regionie jako zestaw dotyczące szyfrowania dysku przy użyciu skalowania [AzKeyVaultAccessPolicy zestaw](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy). Zdefiniuj nazwę istniejącego magazynu kluczy w *$vaultName* zmiennej w następujący sposób:


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania

Najpierw ustaw nazwę użytkownika i hasło administratora wystąpień maszyn wirtualnych przy użyciu polecenia [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Teraz utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [New-AzVmss](/powershell/module/az.compute/new-azvmss). Musisz również utworzyć moduł równoważenia obciążenia, który umożliwia kierowanie ruchu do poszczególnych wystąpień maszyn wirtualnych. Moduł równoważenia obciążenia zawiera reguły, które pozwalają kierować ruchem na porcie TCP 80 oraz korzystać z ruchu pulpitu zdalnego na porcie TCP 3389 i komunikacji zdalnej programu PowerShell na porcie TCP 5985:

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```

## <a name="enable-encryption"></a>Włącz szyfrowanie

Aby zaszyfrować wystąpień maszyn wirtualnych w zestawie skalowania, należy najpierw uzyskać pewne informacje o identyfikator URI magazynu klucz i identyfikator zasobu z [Get AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault). Te zmienne są używane do następnie rozpocznij proces szyfrowania za pomocą [AzVmssDiskEncryptionExtension zestaw](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension):


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

Po wyświetleniu monitu wpisz *y* aby kontynuować proces szyfrowania dysku na skali maszyny Wirtualnej zestawu wystąpień.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Włączenie szyfrowania za pomocą klucza KEK opakuj klucz

Umożliwia także klucz szyfrowania klucza do zwiększenia poziomu bezpieczeństwa w przypadku szyfrowania zestawu skalowania maszyn wirtualnych.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

> [!NOTE]
>  Składnia służąca do wartości parametru dysku — szyfrowanie — magazyn kluczy jest ciągiem pełny identyfikator:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> Składnia wartości parametru klucza szyfrowania jest pełny identyfikator URI do KEK, podobnie jak:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Sprawdzanie postępu szyfrowania

Aby sprawdzić stan szyfrowania dysku, należy użyć [Get AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption):


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

W przypadku wystąpień maszyn wirtualnych są szyfrowane, *EncryptionSummary* kodu raporty *powiodło się/ProvisioningState* jak pokazano w następujących przykładowych danych wyjściowych:

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="disable-encryption"></a>Wyłącz szyfrowanie

Jeśli nie chcesz już korzystać z zaszyfrowanych dysków wystąpień maszyny Wirtualnej, można wyłączyć szyfrowania przy użyciu [AzVmssDiskEncryption Wyłącz](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) w następujący sposób:


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Kolejne kroki

- W tym artykule programu Azure PowerShell jest używany do szyfrowania zestawu skalowania maszyn wirtualnych. Można również użyć [wiersza polecenia platformy Azure](virtual-machine-scale-sets-encrypt-disks-cli.md) lub szablonów dla [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) lub [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
- Jeśli chcesz użyć usługi Azure Disk Encryption stosowane po zaaprowizowaniu innego rozszerzenia, możesz użyć [sekwencjonowania rozszerzeń](virtual-machine-scale-sets-extension-sequencing.md). Możesz użyć [te przykłady](../security/azure-security-disk-encryption-extension-sequencing.md#sample-azure-templates) na rozpoczęcie pracy.
