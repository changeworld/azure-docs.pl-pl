---
title: Szyfruj dyski dla zestawów skalowania platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak używać programu Azure PowerShell do szyfrowania wystąpień maszyn wirtualnych i dołączonych dysków w zestawie skalowania maszyny wirtualnej systemu Windows
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mbaldwin
ms.openlocfilehash: 557d5c023acbc7987d58c9e78bfe11e25f314879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279080"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Szyfruj system operacyjny i dołączone dyski danych w zestawie skalowania maszyny wirtualnej za pomocą interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. Ten przewodnik Szybki start pokazuje, jak używać interfejsu wiersza polecenia platformy Azure do tworzenia i szyfrowania zestawu skalowania maszyny wirtualnej. Aby uzyskać więcej informacji na temat stosowania szyfrowania dysku Azure do zestawu skalowania maszyny wirtualnej, zobacz [Szyfrowanie dysków platformy Azure dla zestawów skalowania maszyn wirtualnych](disk-encryption-overview.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia lokalnie, ten samouczek wymaga, aby uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania

Zanim będzie można utworzyć zestaw skalowania, utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Teraz utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [az vmss create](/cli/azure/vmss). Poniższy przykład tworzy zestaw skalowania o nazwie *myScaleSet*, który jest skonfigurowany do automatycznej aktualizacji w miarę stosowania zmian, a następnie generuje klucze SSH, jeśli nie istnieją, w lokalizacji *~/.ssh/id_rsa*. Dysk danych o 32 Gb jest dołączony do każdego wystąpienia maszyny Wirtualnej, a [niestandardowe rozszerzenie skryptu](../virtual-machines/linux/extensions-customscript.md) platformy Azure służy do przygotowania dysków danych z [zestawem rozszerzeń az vmss:](/cli/azure/vmss/extension)

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Tworzenie magazynu kluczy platformy Azure włączonego do szyfrowania dysku

Usługa Azure Key Vault może przechowywać klucze, wpisy tajne lub hasła, które umożliwiają bezpieczne implementowanie ich w aplikacjach i usługach. Klucze kryptograficzne są przechowywane w usłudze Azure Key Vault przy użyciu ochrony oprogramowania lub można importować lub generować klucze w sprzętowych modułach zabezpieczeń (HSM) certyfikowanych zgodnie ze standardami FIPS 140-2 poziomu 2. Te klucze kryptograficzne są używane do szyfrowania i odszyfrowywania dysków wirtualnych dołączonych do maszyny Wirtualnej. Zachowujesz kontrolę nad tymi kluczami kryptograficznymi i możesz kontrolować ich użycie.

Zdefiniuj własne unikatowe *keyvault_name*. Następnie utwórz keyvault z [az keyvault utworzyć](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) w tej samej subskrypcji i regionu co zestaw skalowania i ustawić *--enabled-for-disk-szyfrowanie* zasady dostępu.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Korzystanie z istniejącego magazynu kluczy

Ten krok jest wymagany tylko wtedy, gdy masz istniejącą przechowalnię kluczy, której chcesz używać z szyfrowaniem dysku. Pomiń ten krok, jeśli w poprzedniej sekcji utworzono magazyn kluczy.

Zdefiniuj własne unikatowe *keyvault_name*. Następnie zaktualizuj funkcję KeyVault za pomocą [aktualizacji az keyvault](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) i ustaw zasady dostępu do *szyfrowania --enabled-for-disk-encryption.*

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Włącz szyfrowanie

Aby zaszyfrować wystąpienia maszyn wirtualnych w zestawie skalowania, najpierw uzyskaj informacje o identyfikatorze zasobu usługi Key Vault za pomocą [programu AZ keyvault show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). Zmienne te są używane do rozpoczęcia procesu szyfrowania z [szyfrowaniem az vmss umożliwiają:](/cli/azure/vmss/encryption#az-vmss-encryption-enable)

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

Rozpoczęcie procesu szyfrowania może potrwać minutę lub dwie.

Ponieważ zestaw skalowania jest zasadą uaktualniania w zestawie skalowania utworzonym we wcześniejszym kroku jest ustawiona na *automatyczną,* wystąpienia maszyny Wirtualnej automatycznie rozpoczynają proces szyfrowania. W przypadku zestawów skalowania, w których zasady uaktualniania mają być ręczne, uruchom zasady szyfrowania w wystąpieniach maszyn wirtualnych z [wystąpieniami aktualizacji az vmss](/cli/azure/vmss#az-vmss-update-instances).

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Włącz szyfrowanie przy użyciu KEK do zawijania klucza

Można również użyć klucza szyfrowania klucza dla zwiększenia zabezpieczeń podczas szyfrowania zestawu skalowania maszyny wirtualnej.

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --key-encryption-key myKEK \
    --key-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

> [!NOTE]
>  Składnią wartości parametru disk-encryption-keyvault jest pełny ciąg identyfikatora:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> Składnia wartości parametru klucza szyfrowania klucza jest pełnym identyfikatorem URI do KEK, jak w:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Sprawdzanie postępu szyfrowania

Aby sprawdzić stan szyfrowania dysku, użyj [programu szyfrowania az vmss:](/cli/azure/vmss/encryption#az-vmss-encryption-show)

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Gdy wystąpienia maszyn wirtualnych są szyfrowane, kod stanu raportuje *EncryptionState/encrypted,* jak pokazano w poniższym przykładzie danych wyjściowych:

```bash
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>Wyłącz szyfrowanie

Jeśli nie chcesz już używać zaszyfrowanych dysków wystąpień maszyn wirtualnych, możesz wyłączyć szyfrowanie za pomocą [szyfrowania az vmss, które wyłącza się](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) w następujący sposób:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Następne kroki

- W tym artykule użyto interfejsu wiersza polecenia platformy Azure do szyfrowania zestawu skalowania maszyny wirtualnej. Można również użyć [szablonów](disk-encryption-azure-resource-manager.md) [programu Azure PowerShell](disk-encryption-powershell.md) lub Usługi Azure Resource Manager .
- Jeśli chcesz, aby szyfrowanie dysków platformy Azure było stosowane po zainicjowaniu obsługi administracyjnej innego rozszerzenia, możesz użyć [sekwencjonowania rozszerzeń](virtual-machine-scale-sets-extension-sequencing.md). 
- Przykład kompleksowego pliku wsadowego dla szyfrowania dysku danych zestawu skalowania linuksa można znaleźć [tutaj](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). W tym przykładzie tworzy grupę zasobów, zestaw skalowania systemu Linux, instaluje dysk danych 5 GB i szyfruje zestaw skalowania maszyny wirtualnej.
