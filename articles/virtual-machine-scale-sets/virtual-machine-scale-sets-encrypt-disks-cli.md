---
title: Szyfrowanie dysków dla zestawów skalowania Azure z wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać 2.0 interfejsu wiersza polecenia platformy Azure do szyfrowania wystąpień maszyn wirtualnych i dołączonych dysków w zestawie skalowania maszyn wirtualnych systemu Linux
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
ms.openlocfilehash: 22d3c763317def137b4e0beb155f28585d7c6ae1
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli-20-preview"></a>Szyfrowanie systemu operacyjnego i dysków dołączonych danych w skali maszyny wirtualnej ustawić 2.0 interfejsu wiersza polecenia platformy Azure (wersja zapoznawcza)

Aby chronić pliki i ochrony danych magazynowanych przy użyciu technologii szyfrowania standardowego w branży, zestawy skalowania maszyny wirtualnej obsługuje szyfrowanie dysków Azure (ADE). Szyfrowanie można włączyć dla maszyny wirtualnej systemu Linux i Windows skalowanie zestawów. Aby uzyskać więcej informacji, zobacz [szyfrowania dysków Azure dla systemów Linux i Windows](../security/azure-security-disk-encryption.md).

> [!NOTE]
>  Szyfrowanie dysków Azure dla zestawy skalowania maszyny wirtualnej jest obecnie w publicznej wersji zapoznawczej, dostępne we wszystkich regionach publicznej Azure.

Szyfrowanie dysków Azure jest obsługiwane:
- Skala zestawów utworzonych za pomocą dysków zarządzanych i nieobsługiwane dla zestawów skalowania dysk lokalny (lub niezarządzanego).
- w przypadku woluminów systemu operacyjnego i danych w zestawach skali systemu Windows. Wyłącz szyfrowanie jest obsługiwane w woluminach systemu operacyjnego i danych dla zestawów skalowania systemu Windows.
- woluminy danych w zestawach skali systemu Linux. Szyfrowanie dysku systemu operacyjnego nie jest obsługiwane w bieżącej wersji zapoznawczej dla zestawów skalowania systemu Linux.

Zestaw maszyn wirtualnych odtworzenia z obrazu i uaktualniania operacji skalowania nie są obsługiwane w bieżącej wersji zapoznawczej. Szyfrowanie dysków Azure dla podglądu zestawy skalowania maszyny wirtualnej jest zalecane tylko w środowiskach testowych. W wersji zapoznawczej nie należy włączać szyfrowanie dysków w środowiskach produkcyjnych, w których konieczne może być uaktualnienia obrazu systemu operacyjnego w zestawie skalowania zaszyfrowane.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli wybierzesz do zainstalowania i używania interfejsu wiersza polecenia lokalnie, w tym samouczku wymaga używasz interfejsu wiersza polecenia Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="register-for-disk-encryption-preview"></a>Zarejestruj w wersji zapoznawczej szyfrowania dysku

Szyfrowanie dysków Azure w wersji zapoznawczej zestawach skali maszyn wirtualnych wymaga własnym rejestracji subskrypcji z [az funkcji rejestru](/cli/azure/feature#az_feature_register). Musisz użycie tej funkcji podglądu dysku po raz pierwszy należy wykonać następujące czynności:

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

Może potrwać do 10 minut na propagację żądania rejestracji. Możesz sprawdzić stan rejestracji z [az funkcji Pokaż](/cli/azure/feature#az_feature_show). Gdy `State` raporty *zarejestrowanej*, ponownie zarejestrować *Mirosoft.Compute* dostawcy z [az dostawcy rejestru](/cli/azure/provider#az_provider_register):

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania

Zanim będzie można utworzyć zestaw skalowania, utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Teraz utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [az vmss create](/cli/azure/vmss#az_vmss_create). Poniższy przykład tworzy zestaw skalowania o nazwie *myScaleSet*, który jest skonfigurowany do automatycznej aktualizacji w miarę stosowania zmian, a następnie generuje klucze SSH, jeśli nie istnieją, w lokalizacji *~/.ssh/id_rsa*. Dysk z danymi 32Gb jest dołączony do każdego wystąpienia maszyny Wirtualnej i platformy Azure [niestandardowe rozszerzenie skryptu](../virtual-machines/linux/extensions-customscript.md) służy do przygotowywania dysków z danymi z [az vmss rozszerzenia zestawu](/cli/azure/vmss/extension#az_vmss_extension_set):

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

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Tworzenie magazynu kluczy Azure włączone szyfrowanie dysków

Usługa Azure Key Vault można przechowywać kluczy, kluczy tajnych lub haseł, które umożliwiają bezpieczne wdrożenie ich w aplikacji i usług. Klucze szyfrowania są przechowywane w usługi Azure Key Vault przy użyciu ochrony oprogramowania, lub możesz zaimportować lub wygenerować klucze w sprzętowych modułach zabezpieczeń (HSM) certyfikowane do FIPS 140-2 poziom 2 standardów. Te klucze szyfrowania są używane do szyfrowania i odszyfrowywania wirtualnych dysków dołączonych do maszyny Wirtualnej. Zachowanie kontroli nad tych kluczy kryptograficznych i przeprowadzić inspekcję ich używania.

Zdefiniuj własny unikatowy *keyvault_name*. Następnie utwórz KeyVault z [utworzyć az keyvault](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) w tej samej subskrypcji i regionu skali ustawić i *— włączone dla dysk szyfrowania* zasady dostępu.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Użyj istniejącego magazynu kluczy

Ten krok jest tylko wymagane, jeśli masz istniejący magazyn kluczy, którego chcesz użyć z szyfrowania dysków. Pomiń ten krok, jeśli magazyn kluczy został utworzony w poprzedniej sekcji.

Zdefiniuj własny unikatowy *keyvault_name*. Następnie, zaktualizowane z KeyVault z [az keyvault aktualizacji](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) i ustaw *— włączone dla dysk szyfrowania* zasady dostępu.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Włącz szyfrowanie

Aby zaszyfrować wystąpień maszyny Wirtualnej w zestawie skalowania, należy najpierw pobrać niektóre informacje na identyfikatorze zasobu usługi Key Vault z [Pokaż keyvault az](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). Te zmienne są używane do następnie rozpocznij proces szyfrowania z [włączyć szyfrowanie vmss az](/cli/azure/vmss/encryption#az-vmss-encryption-enable):

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

Może potrwać minutę lub dwie można uruchomić procesu szyfrowania.

Jako zestaw skalowania jest zasad uaktualniania na skali ustawić utworzony w poprzednim kroku ma ustawioną *automatyczne*, wystąpień maszyny Wirtualnej automatycznie rozpocząć proces szyfrowania. W przypadku zasad uaktualniania na ręczny zestawy skalowania, uruchom Zasady szyfrowania na wystąpień maszyn wirtualnych z [vmss az update wystąpienia](/cli/azure/vmss#az-vmss-update-instances).

## <a name="check-encryption-progress"></a>Sprawdzić postęp szyfrowania

Aby sprawdzić stan szyfrowania dysku, należy użyć [az vmss szyfrowania Pokaż](/cli/azure/vmss/encryption#az-vmss-encryption-show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Gdy wystąpień maszyn wirtualnych są szyfrowane, kod stanu raporty *EncryptionState/zaszyfrowane*, jak pokazano w poniższym przykładzie danych wyjściowych:

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

## <a name="disable-encryption"></a>Wyłączenie szyfrowania

Jeśli nie chcesz już używać zaszyfrowanych dysków wystąpień maszyny Wirtualnej, można wyłączyć szyfrowania z [wyłączyć szyfrowanie vmss az](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) w następujący sposób:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule Azure CLI 2.0 jest używany do szyfrowania zestaw skali maszyny wirtualnej. Można również użyć [programu Azure PowerShell](virtual-machine-scale-sets-encrypt-disks-ps.md) lub szablonów dla [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) lub [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).

Przykład pliku wsadowego end-to-end szyfrowanie dysków danych zestaw skalowania Linux można znaleźć [tutaj](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). W tym przykładzie tworzy grupę zasobów, zestaw skali Linux, instaluje dysk z danymi 5 GB i szyfruje zestaw skali maszyny wirtualnej.