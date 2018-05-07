---
title: Szyfrowanie dysków dla zestawach skali Azure przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak szyfrowanie wystąpień maszyn wirtualnych i dołączonych dysków w zestawie skalowania maszyn wirtualnych systemu Windows przy użyciu programu Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 91138ffad0fd906061e0b0ce5cdb251f402d3341
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell-preview"></a>Szyfrowanie systemu operacyjnego i dysków dołączonych danych w skali maszyny wirtualnej ustawić przy użyciu programu Azure PowerShell (wersja zapoznawcza)

Aby chronić pliki i ochrony danych magazynowanych przy użyciu technologii szyfrowania standardowego w branży, zestawy skalowania maszyny wirtualnej obsługuje szyfrowanie dysków Azure (ADE). Szyfrowanie można włączyć dla maszyny wirtualnej systemu Windows i Linux skalowanie zestawów. Aby uzyskać więcej informacji, zobacz [Azure dysku szyfrowanie dla systemu Windows i Linux](../security/azure-security-disk-encryption.md).

> [!NOTE]
>  Szyfrowanie dysków Azure dla zestawy skalowania maszyny wirtualnej jest obecnie w publicznej wersji zapoznawczej, dostępne we wszystkich regionach publicznej Azure.

Szyfrowanie dysków Azure jest obsługiwane:
- Skala zestawów utworzonych za pomocą dysków zarządzanych i nieobsługiwane dla zestawów skalowania dysk lokalny (lub niezarządzanego).
- w przypadku woluminów systemu operacyjnego i danych w zestawach skali systemu Windows. Wyłącz szyfrowanie jest obsługiwane w woluminach systemu operacyjnego i danych dla zestawów skalowania systemu Windows.
- woluminy danych w zestawach skali systemu Linux. Szyfrowanie dysku systemu operacyjnego nie jest obsługiwane w bieżącej wersji zapoznawczej dla zestawów skalowania systemu Linux.

Zestaw maszyn wirtualnych odtworzenia z obrazu i uaktualniania operacji skalowania nie są obsługiwane w bieżącej wersji zapoznawczej. Szyfrowanie dysków Azure dla podglądu zestawy skalowania maszyny wirtualnej jest zalecane tylko w środowiskach testowych. W wersji zapoznawczej nie należy włączać szyfrowanie dysków w środowiskach produkcyjnych, w których konieczne może być uaktualnienia obrazu systemu operacyjnego w zestawie skalowania zaszyfrowane.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli wybierzesz do zainstalowania i używania programu PowerShell lokalnie, ten samouczek wymaga programu Azure PowerShell w wersji modułu 5.7.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="register-for-disk-encryption-preview"></a>Zarejestruj w wersji zapoznawczej szyfrowania dysku

Szyfrowanie dysków Azure w wersji zapoznawczej zestawach skali maszyn wirtualnych wymaga własnym rejestracji subskrypcji z [AzureRmProviderFeature rejestru](/powershell/module/azurerm.resources/register-azurermproviderfeature). Musisz użycie tej funkcji podglądu dysku po raz pierwszy należy wykonać następujące czynności:

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Może potrwać do 10 minut na propagację żądania rejestracji. Możesz sprawdzić stan rejestracji z [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Gdy `RegistrationState` raporty *zarejestrowanej*, ponownie zarejestrować *Mirosoft.Compute* dostawcy z [AzureRmResourceProvider rejestru](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider):

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Utwórz włączone szyfrowanie dysków Azure Key Vault

Usługa Azure Key Vault można przechowywać kluczy, kluczy tajnych lub haseł, które umożliwiają bezpieczne wdrożenie ich w aplikacji i usług. Klucze szyfrowania są przechowywane w usługi Azure Key Vault przy użyciu ochrony oprogramowania, lub możesz zaimportować lub wygenerować klucze w sprzętowych modułach zabezpieczeń (HSM) certyfikowane do FIPS 140-2 poziom 2 standardów. Te klucze szyfrowania są używane do szyfrowania i odszyfrowywania wirtualnych dysków dołączonych do maszyny Wirtualnej. Zachowanie kontroli nad tych kluczy kryptograficznych i przeprowadzić inspekcję ich używania.

Tworzenie magazynu kluczy o [nowy AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). Aby zezwolić na magazyn kluczy do szyfrowania dysku, *EnabledForDiskEncryption* parametru. W poniższym przykładzie zdefiniowano także zmienne dla nazwy grupy zasobów, klucz magazynu nazwy i lokalizacji. Podaj własną unikatową nazwę usługi Key Vault:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzureRmResourceGroup -Name $rgName -Location $location
New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Użyj istniejącego magazynu kluczy

Ten krok jest tylko wymagane, jeśli masz istniejący magazyn kluczy, którego chcesz użyć z szyfrowania dysków. Pomiń ten krok, jeśli magazyn kluczy został utworzony w poprzedniej sekcji.

Można włączyć istniejącego magazynu kluczy w tej samej subskrypcji i regionu jako zestaw szyfrowanie dysków z skalowania [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/AzureRM.KeyVault/Set-AzureRmKeyVaultAccessPolicy). Zdefiniuj nazwę istniejącego magazynu kluczy, w *$vaultName* zmiennej w następujący sposób:

```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania

Najpierw ustaw nazwę użytkownika i hasło administratora wystąpień maszyn wirtualnych przy użyciu polecenia [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Teraz utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Musisz również utworzyć moduł równoważenia obciążenia, który umożliwia kierowanie ruchu do poszczególnych wystąpień maszyn wirtualnych. Moduł równoważenia obciążenia zawiera reguły, które pozwalają kierować ruchem na porcie TCP 80 oraz korzystać z ruchu pulpitu zdalnego na porcie TCP 3389 i komunikacji zdalnej programu PowerShell na porcie TCP 5985:

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzureRmVmss `
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

Aby zaszyfrować wystąpień maszyny Wirtualnej w zestawie skalowania, należy najpierw pobrać niektóre informacje w identyfikatorze URI magazynu kluczy i zasobów z [Get-AzureRmKeyVault](/powershell/module/AzureRM.KeyVault/Get-AzureRmKeyVault). Te zmienne są używane do następnie rozpocznij proces szyfrowania z [AzureRmVmssDiskEncryptionExtension zestaw](/powershell/module/AzureRM.Compute/Set-AzureRmVmssDiskEncryptionExtension):

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

Po wyświetleniu monitu wpisz *y* aby kontynuować proces szyfrowania dysku na skali ustawić wirtualna wystąpienia.

## <a name="check-encryption-progress"></a>Sprawdzić postęp szyfrowania

Aby sprawdzić stan szyfrowania dysku, należy użyć [Get-AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Get-AzureRmVmssDiskEncryption):

```azurepowershell-interactive
Get-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Gdy wystąpień maszyn wirtualnych są szyfrowane, *EncryptionSummary* kodu raporty *powiodło się/ProvisioningState* jak pokazano w poniższym przykładzie danych wyjściowych:

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

## <a name="disable-encryption"></a>Wyłączenie szyfrowania

Jeśli nie chcesz już używać zaszyfrowanych dysków wystąpień maszyny Wirtualnej, można wyłączyć szyfrowania z [AzureRmVmssDiskEncryption Wyłącz](/powershell/module/AzureRM.Compute/Disable-AzureRmVmssDiskEncryption) w następujący sposób:

```azurepowershell-interactive
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule programu Azure PowerShell jest używany do szyfrowania zestaw skali maszyny wirtualnej. Można również użyć [Azure CLI 2.0](virtual-machine-scale-sets-encrypt-disks-cli.md) lub szablonów dla [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) lub [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
