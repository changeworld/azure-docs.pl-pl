---
title: Tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym przewodniku Szybki start dowiesz się, jak używać interfejsu wiersza polecenia platformy Azure do tworzenia i szyfrowania maszyny wirtualnej systemu Linux
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 1362844de0a6b5d8cee4555c3d24833affe71640
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385150"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>Szybki start: tworzenie i szyfrowanie maszyny Wirtualnej systemu Linux za pomocą interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. Ten przewodnik Szybki start pokazuje, jak używać interfejsu wiersza polecenia platformy Azure do tworzenia i szyfrowania maszyny wirtualnej systemu Linux (VM).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia platformy Azure lokalnie, ten szybki start wymaga, aby uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus:*

```azurecli-interactive
az group create --name "myResourceGroup" --location "eastus"
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). W poniższym przykładzie utworzono maszynę wirtualną o nazwie *myVM*.

```azurecli-interactive
az vm create \
    --resource-group "myResourceGroup" \
    --name "myVM" \
    --image "Canonical:UbuntuServer:16.04-LTS:latest" \
    --size "Standard_D2S_V3"\
    --generate-ssh-keys
```

Utworzenie maszyny wirtualnej i zasobów pomocniczych potrwa kilka minut. Następujące przykładowe dane wyjściowe pokazują, że operacja utworzenia maszyny wirtualnej zakończyła się pomyślnie.

```
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Tworzenie magazynu kluczy skonfigurowanych dla kluczy szyfrowania

Szyfrowanie dysków platformy Azure przechowuje swój klucz szyfrowania w magazynie azure key vault. Utwórz magazyn kluczy za pomocą [programu az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create). Aby włączyć magazyn kluczy do przechowywania kluczy szyfrowania, należy użyć parametru --enabled-for-disk-encryption.

> [!Important]
> Każdy magazyn kluczy musi mieć nazwę, która jest unikatowa na platformie Azure. W poniższych przykładach zastąp <> nazwa unikatowa-keyvault na wybraną nazwę.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Szyfruj maszynę wirtualną

Zaszyfruj [maszynę wirtualną za pomocą szyfrowania az vm,](/cli/azure/vm/encryption?view=azure-cli-latest)podając unikatową nazwę usługi Key Vault parametrowi --disk-encryption-keyvault.

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>"
```

Po chwili proces powróci, "Żądanie szyfrowania zostało zaakceptowane. Użyj polecenia 'show', aby monitorować postęp.". Polecenie "show" to [az vm show](/cli/azure/vm/encryption#az-vm-encryption-show).

```azurecli-interactive
az vm show --name "myVM" -g "MyResourceGroup"
```

Gdy szyfrowanie jest włączone, na zwróconym wyjściu zostaną wyświetlone następujące informacje:

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, można użyć polecenia [delete grupy az,](/cli/azure/group) aby usunąć grupę zasobów, maszynę wirtualną i magazyn kluczy. 

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono maszynę wirtualną, utworzono magazyn kluczy, który był włączony dla kluczy szyfrowania, i zaszyfrowano maszynę wirtualną.  Przejdź do następnego artykułu, aby dowiedzieć się więcej o bardziej szyfrowaniu dysków platformy Azure dla maszyn wirtualnych z systemem Linux.

> [!div class="nextstepaction"]
> [Omówienie szyfrowania dysków platformy Azure](disk-encryption-overview.md)