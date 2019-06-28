---
title: Tworzenie i szyfrowanie maszyny Wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure
description: W tym przewodniku Szybki Start dowiesz się, jak tworzenie i szyfrowanie maszyny wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: c0ae2f6588ede0305e02edf1942ff196aeb807bb
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331582"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>Szybki start: Tworzenie i szyfrowanie maszyny Wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. Ten przewodnik Szybki Start pokazano, jak tworzenie i szyfrowanie maszyny wirtualnej systemu Linux (VM) przy użyciu wiersza polecenia platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). W poniższym przykładzie utworzono maszynę wirtualną o nazwie *myVM*.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --size Standard_D2S_V3
```

Utworzenie maszyny wirtualnej i zasobów pomocniczych potrwa kilka minut. Następujące przykładowe dane wyjściowe pokazują, że operacja utworzenia maszyny wirtualnej zakończyła się pomyślnie.

```azurecli-interactive
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

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Tworzenie usługi Key Vault skonfigurowane dla kluczy szyfrowania

Usługa Azure disk encryption przechowuje klucz szyfrowania w usłudze Azure Key Vault. Tworzenie usługi Key Vault przy użyciu [tworzenie az keyvault](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create). Aby włączyć usługi Key Vault do przechowywania kluczy szyfrowania, użyj parametru--włączone for szyfrowania dysków.

> [!Important]
> Każda usługa Key Vault musi mieć unikatową nazwę. Poniższy przykład obejmuje tworzenie usługi Key Vault o nazwie *myKV*, ale musisz nazwać należy do Ciebie coś innego.

```azurecli
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Szyfrowanie maszyny wirtualnej

Szyfrowanie maszyny Wirtualnej za pomocą [az vm encryption](/cli/azure/vm/encryption?view=azure-cli-latest), podając unikatową magazyn kluczy nazwę parametru--dysku — szyfrowanie — magazyn kluczy.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

Po chwili, który zwróci proces "szyfrowania żądanie zostało zaakceptowane. Użyj "Pokaż" polecenie, aby monitorować postęp. ". Polecenie "Pokaż" jest [az vm show](/cli/azure/vm/encryption#az-vm-encryption-show).

```azurecli-interactive
az vm show --name MyVM -g MyResourceGroup
```

Gdy jest włączone szyfrowanie, zobaczysz następujące dane wyjściowe:

```azurecli-interactive
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie będą już potrzebne, możesz użyć [usunięcie grupy az](/cli/azure/group) polecenie, aby usunąć grupę zasobów, maszyny Wirtualnej i usługi Key Vault. 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start utworzono maszynę wirtualną, utworzyć magazyn kluczy, którego zostały włączone dla kluczy szyfrowania, a następnie zaszyfrowanych maszyn wirtualnych.  Przejdź do następnego artykułu, aby dowiedzieć się więcej na temat wymagań wstępnych usługi Azure Disk Encryption dotyczących maszyn wirtualnych IaaS.

> [!div class="nextstepaction"]
> [Wymagania wstępne usługi Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)
