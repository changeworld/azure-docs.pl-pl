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
ms.date: 04/30/2018
ms.author: cynthn
ms.openlocfilehash: cc1405d2dd972aff6091a9d5b60ff9da18185286
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978106"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell-preview"></a>Szyfrowanie systemu operacyjnego i dołączonych dysków z danymi w maszynie wirtualnej zestawu skalowania przy użyciu programu Azure PowerShell (wersja zapoznawcza)

Aby chronić pliki i chronić dane magazynowane przy użyciu technologii szyfrowania standardowych w branży, zestawy skalowania maszyn wirtualnych obsługują szyfrowania dysków Azure (ADE). Szyfrowanie można włączyć dla maszyny wirtualnej Windows i Linux zestawów skalowania. Aby uzyskać więcej informacji, zobacz [Azure szyfrowania dysku dla Windows i Linux](../security/azure-security-disk-encryption.md).

> [!NOTE]
>  Usługa Azure disk encryption dla zestawów skalowania maszyn wirtualnych jest obecnie w publicznej wersji zapoznawczej dostępne we wszystkich publicznych regionach platformy Azure.

Usługa Azure disk encryption jest obsługiwane:
- Skalowanie zestawów utworzonych za pomocą dysków zarządzanych i nie jest obsługiwane dla zestawów skalowania dysku natywnego (lub niezarządzanego).
- w przypadku woluminów systemu operacyjnego i danych w zestawach skalowania Windows. Wyłącz szyfrowanie jest obsługiwane w przypadku woluminów systemu operacyjnego i danych dla zestawów skalowania Windows.
- woluminy danych w zestawach skalowania systemu Linux. Szyfrowanie dysku systemu operacyjnego nie jest obsługiwane w bieżącej wersji zapoznawczej dla zestawów skalowania systemu Linux.

Zestaw maszyn wirtualnych odtworzenia z obrazu i uaktualniania, operacje skalowania nie są obsługiwane w bieżącej wersji zapoznawczej. Usługa Azure disk encryption dla wersji zapoznawczej zestawów skalowania maszyn wirtualnych jest zalecane tylko w środowiskach testowych. W wersji zapoznawczej nie włączaj szyfrowania dysków w środowiskach produkcyjnych, w których konieczne może być uaktualnienia obrazu systemu operacyjnego w zestawie skalowania zaszyfrowane.

[!INCLUDE [updated-for-az-vm.md](../../includes/updated-for-az-vm.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]


## <a name="register-for-disk-encryption-preview"></a>Rejestrowanie na potrzeby szyfrowania dysku w wersji zapoznawczej

Usługa Azure disk encryption dla zestawów skalowania maszyn wirtualnych w wersji zapoznawczej, musisz zarejestrować się samodzielnie subskrypcji za pomocą [AzProviderFeature rejestru](/powershell/module/az.resources/register-azproviderfeature). Wystarczy użycie funkcji w wersji zapoznawczej szyfrowania dysku po raz pierwszy należy wykonać następujące czynności:

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```


Może upłynąć do 10 minut dla żądania rejestrowania do propagowania. Można sprawdzić stanu rejestracji za pomocą [Get AzProviderFeature](/powershell/module/az.resources/Get-AzProviderFeature). Gdy `RegistrationState` raporty *zarejestrowane*, ponownie zarejestrować *Microsoft.Compute* dostawcy z [AzResourceProvider rejestru](/powershell/module/az.resources/Register-AzResourceProvider):


```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

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

Teraz tworzenie maszyny wirtualnej zestawu skalowania z [New AzVmss](/powershell/module/az.compute/new-azvmss). Musisz również utworzyć moduł równoważenia obciążenia, który umożliwia kierowanie ruchu do poszczególnych wystąpień maszyn wirtualnych. Moduł równoważenia obciążenia zawiera reguły, które pozwalają kierować ruchem na porcie TCP 80 oraz korzystać z ruchu pulpitu zdalnego na porcie TCP 3389 i komunikacji zdalnej programu PowerShell na porcie TCP 5985:

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

W tym artykule programu Azure PowerShell jest używany do szyfrowania zestawu skalowania maszyn wirtualnych. Można również użyć [wiersza polecenia platformy Azure](virtual-machine-scale-sets-encrypt-disks-cli.md) lub szablonów dla [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) lub [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
