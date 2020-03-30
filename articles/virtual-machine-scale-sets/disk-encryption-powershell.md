---
title: Szyfrowanie dysków dla zestawów skalowania platformy Azure za pomocą programu Azure PowerShell
description: Dowiedz się, jak używać programu Azure PowerShell do szyfrowania wystąpień maszyn wirtualnych i dołączonych dysków w zestawie skalowania maszyny wirtualnej systemu Windows
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mbaldwin
ms.openlocfilehash: bd7f92c104e06896f4b3c8bb2adef45983cf5d4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279000"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Szyfrowanie systemu operacyjnego i dołączonych dysków danych w zestawie skalowania maszyny wirtualnej za pomocą programu Azure PowerShell

Moduł Azure PowerShell umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi za pomocą wiersza polecenia programu PowerShell lub skryptów.  W tym artykule pokazano, jak używać programu Azure PowerShell do tworzenia i szyfrowania zestawu skalowania maszyny wirtualnej. Aby uzyskać więcej informacji na temat stosowania szyfrowania dysków platformy Azure do zestawu skalowania maszyny wirtualnej, zobacz [Szyfrowanie dysków platformy Azure dla zestawów skalowania maszyn wirtualnych](disk-encryption-overview.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Tworzenie usługi Azure Key Vault włączonej do szyfrowania dysku

Usługa Azure Key Vault może przechowywać klucze, wpisy tajne lub hasła, które umożliwiają bezpieczne implementowanie ich w aplikacjach i usługach. Klucze kryptograficzne są przechowywane w usłudze Azure Key Vault przy użyciu ochrony oprogramowania lub można importować lub generować klucze w sprzętowych modułach zabezpieczeń (HSM) certyfikowanych zgodnie ze standardami FIPS 140-2 poziomu 2. Te klucze kryptograficzne są używane do szyfrowania i odszyfrowywania dysków wirtualnych dołączonych do maszyny Wirtualnej. Zachowujesz kontrolę nad tymi kluczami kryptograficznymi i możesz kontrolować ich użycie.

Utwórz magazyn kluczy za pomocą [programu New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Aby zezwolić na użycie magazynu kluczy do szyfrowania dysku, należy ustawić parametr *EnabledForDiskEncryption.* Poniższy przykład definiuje również zmienne dla nazwy grupy zasobów, nazwy magazynu kluczy i lokalizacji. Podaj własną unikalną nazwę Magazynu kluczy:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Korzystanie z istniejącego magazynu kluczy

Ten krok jest wymagany tylko wtedy, gdy masz istniejącą przechowalnię kluczy, której chcesz używać z szyfrowaniem dysku. Pomiń ten krok, jeśli w poprzedniej sekcji utworzono magazyn kluczy.

Istniejącą usługę Key Vault można włączyć w tej samej subskrypcji i regionie co zestaw skalowania dla szyfrowania dysku za pomocą [funkcji Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy). Zdefiniuj nazwę istniejącego magazynu kluczy w *zmiennej $vaultName* w następujący sposób:


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

Aby zaszyfrować wystąpienia maszyn wirtualnych w zestawie skalowania, najpierw uzyskaj informacje o identyfikatorze URI usługi Key Vault i identyfikatorie zasobu za pomocą [funkcji Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault). Zmienne te są używane do rozpoczęcia procesu szyfrowania z [Set-AzVmssDiskEncryptionExtension:](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension)


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

Po wyświetleniu monitu wpisz *y,* aby kontynuować proces szyfrowania dysku w wystąpieniach maszyny Wirtualnej zestawu skalowania.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Włącz szyfrowanie przy użyciu KEK do zawijania klucza

Można również użyć klucza szyfrowania klucza dla zwiększenia zabezpieczeń podczas szyfrowania zestawu skalowania maszyny wirtualnej.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

> [!NOTE]
>  Składnią wartości parametru disk-encryption-keyvault jest pełny ciąg identyfikatora:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> Składnia wartości parametru klucza szyfrowania klucza jest pełnym identyfikatorem URI do KEK, jak w:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Sprawdzanie postępu szyfrowania

Aby sprawdzić stan szyfrowania dysku, użyj [get-AzVmssDiskEncryption:](/powershell/module/az.compute/Get-AzVmssDiskEncryption)


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Gdy wystąpienia maszyn wirtualnych są szyfrowane, *kod szyfrowaniapomary* raportuje *apropozycjęState/zakończyło się pomyślnie,* jak pokazano w poniższym przykładowym wyjściu:

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

Jeśli nie chcesz już używać zaszyfrowanych dysków wystąpień maszyn wirtualnych, możesz wyłączyć szyfrowanie za pomocą [disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) w następujący sposób:


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Następne kroki

- W tym artykule użyto programu Azure PowerShell do szyfrowania zestawu skalowania maszyny wirtualnej. Można również użyć [szablonów](disk-encryption-azure-resource-manager.md) [interfejsu wiersza polecenia](disk-encryption-cli.md) platformy Azure lub usługi Azure Resource Manager .
- Jeśli chcesz, aby szyfrowanie dysków platformy Azure było stosowane po zainicjowaniu obsługi administracyjnej innego rozszerzenia, możesz użyć [sekwencjonowania rozszerzeń](virtual-machine-scale-sets-extension-sequencing.md).
