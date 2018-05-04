---
title: Zestawy skalowania maszyny wirtualnej platformy Azure szyfrowania dysków | Dokumentacja firmy Microsoft
description: Informacje o szyfrowaniu dysków dołączonych w zestawy skalowania maszyny wirtualnej.
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
ms.date: 01/26/2018
ms.author: iainfou
ms.openlocfilehash: 570764ad5d657a8b1efa2425423a89ddc518451c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set"></a>Szyfrowanie systemu operacyjnego i dysków dołączonych danych w zestawie skalowania maszyn wirtualnych
Azure [zestawy skalowania maszyny wirtualnej](/azure/virtual-machine-scale-sets/) szyfrowanie dysków Azure (ADE).  Można włączyć szyfrowanie dysków Azure dla systemu Windows i Linux zestawach skali maszyn wirtualnych do ochrony i skali w zabezpieczaniu ustawia dane przechowywane przy użyciu szyfrowania standardowe technologie. Aby uzyskać więcej informacji przeczytaj maszyny wirtualne Azure dysku szyfrowanie dla systemu Windows i Linux.

> [!NOTE]
>  Szyfrowanie dysków Azure dla zestawy skalowania maszyny wirtualnej jest obecnie w publicznej wersji zapoznawczej, dostępne we wszystkich regionach publicznej Azure.

Szyfrowanie dysków Azure jest obsługiwane:
- Skala zestawów utworzonych za pomocą dysków zarządzanych i nieobsługiwane dla zestawów skalowania dysk lokalny (lub niezarządzanego).
- w przypadku woluminów systemu operacyjnego i danych w zestawach skali systemu Windows. Wyłącz szyfrowanie jest obsługiwane w woluminach systemu operacyjnego i danych dla zestawów skalowania systemu Windows.
- woluminy danych w zestawach skali systemu Linux. Szyfrowanie dysku systemu operacyjnego nie jest obsługiwane w bieżącej wersji zapoznawczej dla zestawów skalowania systemu Linux.

Zestaw maszyn wirtualnych odtworzenia z obrazu i uaktualniania operacji skalowania nie są obsługiwane w bieżącej wersji zapoznawczej. Szyfrowanie dysków Azure dla podglądu zestawy skalowania maszyny wirtualnej jest zalecane tylko w środowiskach testowych. W wersji zapoznawczej nie należy włączać szyfrowanie dysków w środowiskach produkcyjnych, w których konieczne może być uaktualnienia obrazu systemu operacyjnego w zestawie skalowania zaszyfrowane.

Przykład pliku wsadowego end-to-end szyfrowanie dysków danych zestaw skalowania Linux można znaleźć [tutaj](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat).  W tym przykładzie tworzy grupę zasobów, zestaw skali Linux, instaluje dysk z danymi 5 GB i szyfruje zestaw skali maszyny wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne
Zainstaluj najnowsze wersje [programu Azure Powershell](https://github.com/Azure/azure-powershell/releases) lub [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), które zawierają polecenia szyfrowania.

Szyfrowania dysków Azure w celu Podgląd zestawów skali maszyny wirtualnej, należy zarejestrować się samodzielnie subskrypcji przy użyciu następujących poleceń programu PowerShell: 

```powershell
Connect-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Poczekaj około 10 minut, aż do stanu "Zarejestrowanej" jest zwracany za pomocą następującego polecenia: 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Tworzenie magazynu kluczy Azure włączone szyfrowanie dysków
Utwórz nowy magazyn kluczy w tej samej subskrypcji i regionu jako skali ustawiona i ustawić zasady dostępu "EnabledForDiskEncryption".

```azurecli
rgname="linuxdatadiskencryptiontest"
VaultName="encryptionvault321"

az keyvault create --name $VaultName --resource-group $rgname --enabled-for-disk-encryption
```

Lub włączyć istniejący magazyn kluczy w tej samej subskrypcji i regionu jako zestaw szyfrowanie dysków skalowania.

```azurecli
VaultName="encryptionvault321"
az keyvault update --name $VaultName --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Włącz szyfrowanie

Następujące polecenia zaszyfrować dysk z danymi w skali uruchomionych ustawić za pomocą magazynu kluczy w tej samej grupie zasobów. Umożliwia także szablony do szyfrowania dysków w uruchomionej [zestaw skali Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) lub [zestaw skali Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"
EncryptionKeyVaultUrl="https://encryptionvaultlinuxsf.vault.azure.net"
VaultResourceId="/subscriptions/0754ecc2-d80d-426a-902c-b83f4cfbdc95/resourceGroups/linuxdatadiskencryptiontest/providers/Microsoft.KeyVault/vaults/encryptionvaultlinuxsf"

az vmss encryption enable -g $ResourceGroup -n $VmssName --disk-encryption-keyvault $VaultResourceId --volume-type DATA
az vmss update-instances -g $ResourceGroup -n $VmssName --instance-ids *
```

## <a name="check-encryption-progress"></a>Sprawdzić postęp szyfrowania

Użyj następujących poleceń w celu określenia stanu szyfrowania zestawu skali.

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"

az vmss encryption show -g $ResourceGroup -n $VmssName
```

## <a name="disable-encryption"></a>Wyłączenie szyfrowania
Wyłączenie szyfrowania w skali uruchomionej maszyny wirtualnej ustawić za pomocą następujących poleceń. Umożliwia także szablony umożliwia wyłączenie szyfrowania uruchomione [zestaw skali maszyny Wirtualnej systemu Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows) lub [zestaw skali maszyny Wirtualnej systemu Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux).

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"

az vmss encryption disable -g $ResourceGroup -n $VmssName
```

