---
title: Szyfrowanie dysków na maszynie Wirtualnej systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Jak zaszyfrować dyski wirtualne na maszynie Wirtualnej systemu Linux w celu uzyskania zwiększonych zabezpieczeń przy użyciu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 15bd3cf2ab6ea5285662610c2c0a850bb180e2f8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60731621"
---
# <a name="how-to-encrypt-a-linux-virtual-machine-in-azure"></a>Jak zaszyfrować maszyny wirtualnej z systemem Linux na platformie Azure

Rozszerzone maszynę wirtualną (VM), zabezpieczeń i zgodności mogą być szyfrowane dyski wirtualne, a samej maszyny Wirtualnej. Maszyny wirtualne są szyfrowane przy użyciu kluczy kryptograficznych, które są zabezpieczone w usłudze Azure Key Vault. Możesz kontrolować klucze kryptograficzne i przeprowadzać ich inspekcje ich użycie. Ten artykuł szczegółowo opisuje sposób szyfrowania dysków wirtualnych na Maszynę wirtualną systemu Linux przy użyciu wiersza polecenia platformy Azure. 

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w oddzielnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/bash](https://shell.azure.com/bash). Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

Jeśli zdecydujesz się zainstalować i korzystać z interfejsu wiersza polecenia lokalnie, ten artykuł wymaga, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="overview-of-disk-encryption"></a>Omówienie szyfrowania dysku
Dyski wirtualne na maszynach wirtualnych z systemem Linux są szyfrowane, gdy rest przy użyciu [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Nie ma opłat do szyfrowania dysków wirtualnych na platformie Azure. Klucze szyfrowania są przechowywane w usłudze Azure Key Vault przy użyciu ochrony oprogramowania lub można importować lub generować klucze w sprzętowych modułach zabezpieczeń (HSM) certyfikowane zgodnych ze standardami FIPS 140-2 poziom 2 standardy. Zachowanie kontroli nad te klucze kryptograficzne i przeprowadzać ich inspekcje ich użycie. Te klucze szyfrowania są używane do szyfrowania i odszyfrowywania dyski wirtualne dołączone do maszyny Wirtualnej. 

Proces szyfrowania maszyny Wirtualnej jest w następujący sposób:

1. Utwórz klucz kryptograficzny w usłudze Azure Key Vault.
1. Konfigurowanie klucza kryptograficznego, może być używany do szyfrowania dysków.
1. Włącz szyfrowanie dysków dla dysków wirtualnych.
1. Wymagane klucze szyfrowania są żądane w usłudze Azure Key Vault.
1. Wirtualne dyski są szyfrowane przy użyciu podanego klucza kryptograficznego.


## <a name="requirements-and-limitations"></a>Wymagania i ograniczenia
Obsługiwane scenariusze i wymagania dotyczące szyfrowania dysku:

* Następujące serwerze z systemem Linux jednostek SKU — Ubuntu, CentOS, SUSE i systemem SUSE Linux Enterprise Server (SLES) i Red Hat Enterprise Linux.
* Wszystkie zasoby (na przykład usługi Key Vault, konto magazynu oraz maszyny Wirtualnej) muszą być w tym samym regionie platformy Azure i subskrypcji.
* Standardowa A, D, DS, G i GS, itp., maszyny wirtualne z serii.
* Aktualizowanie kluczy kryptograficznych na już zaszyfrowane maszyny Wirtualnej systemu Linux.

Szyfrowanie dysku nie jest obecnie obsługiwane w następujących scenariuszach:

* Warstwa podstawowa maszyn wirtualnych.
* Maszyny wirtualne utworzone przy użyciu klasycznego modelu wdrażania.
* Wyłączenie szyfrowania dysku systemu operacyjnego na maszynach wirtualnych z systemem Linux.
* Korzystanie z niestandardowych obrazów systemu Linux.

Aby uzyskać więcej informacji na temat obsługiwanych scenariuszach i ograniczeniach, zobacz [usługi Azure Disk Encryption dla maszyn wirtualnych IaaS](../../security/azure-security-disk-encryption.md)


## <a name="create-an-azure-key-vault-and-keys"></a>Tworzenie usługi Azure Key Vault i klucze
W poniższych przykładach należy zastąpić własnymi wartościami przykładowe nazwy parametru. Przykładowe nazwy parametru zawierają *myResourceGroup*, *klucze*, i *myVM*.

Pierwszym krokiem jest do utworzenia usługi Azure Key Vault do przechowywania kluczy kryptograficznych. Usługa Azure Key Vault można przechowywać klucze, wpisy tajne lub haseł, które umożliwiają bezpieczne wdrożenie ich w aplikacji i usług. Do szyfrowania dysku wirtualnego użyjesz usługi Key Vault do przechowywania klucza kryptograficznego, który służy do szyfrowania i odszyfrowywania Twoje wirtualne dyski.

Włącz dostawcę usługi Azure Key Vault w ramach subskrypcji platformy Azure za pomocą [az provider register](/cli/azure/provider#az-provider-register) i Utwórz grupę zasobów za pomocą [Tworzenie grupy az](/cli/azure/group#az-group-create). Poniższy przykład tworzy nazwę grupy zasobów *myResourceGroup* w `eastus` lokalizacji:

```azurecli-interactive
az provider register -n Microsoft.KeyVault
resourcegroup="myResourceGroup"
az group create --name $resourcegroup --location eastus
```

Usługi Azure Key Vault, zawierający klucze kryptograficzne i zasoby obliczeniowe skojarzone, takie jak storage i samej maszyny Wirtualnej muszą znajdować się w tym samym regionie. Tworzenie usługi Azure Key Vault przy użyciu [tworzenie az keyvault](/cli/azure/keyvault#az-keyvault-create) i włączyć usługi Key Vault do użytku z szyfrowania dysków. Określ unikatową nazwę usługi Key Vault *keyvault_name* w następujący sposób:

```azurecli-interactive
keyvault_name=myvaultname$RANDOM
az keyvault create \
    --name $keyvault_name \
    --resource-group $resourcegroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Można przechowywać klucze szyfrowania przy użyciu oprogramowania lub ochrona sprzętu modelu zabezpieczeń (HSM). Przy użyciu sprzętowego modułu zabezpieczeń wymaga usługi Key Vault w wersji premium. Brak dodatkowych kosztów, do tworzenia premium usługi Key Vault, a nie standardowy usługi Key Vault, który przechowuje klucze chronione programowo. Aby utworzyć premium usługi Key Vault, w poprzednim kroku należy dodać `--sku Premium` do polecenia. W poniższym przykładzie użyto kluczy chronionych programowo, od momentu utworzenia standardowe usługi Key Vault.

Oba modele ochrony platformy Azure musi otrzymać dostęp do zażądać kluczy kryptograficznych w przypadku, gdy maszyna wirtualna zostanie do odszyfrowania dysków wirtualnych. Utwórz klucz kryptograficzny w usłudze Key Vault przy użyciu [tworzenie az keyvault key](/cli/azure/keyvault/key#az-keyvault-key-create). Poniższy przykład tworzy klucz o nazwie *klucze*:

```azurecli-interactive
az keyvault key create \
    --vault-name $keyvault_name \
    --name myKey \
    --protection software
```


## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Utwórz Maszynę wirtualną za pomocą [tworzenie az vm](/cli/azure/vm#az-vm-create) i dołączanie dysku danych 5 Gb. Tylko niektórych obrazów z portalu marketplace obsługuje szyfrowania dysku. Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* przy użyciu *Ubuntu 16.04 LTS* obrazu:

```azurecli-interactive
az vm create \
    --resource-group $resourcegroup \
    --name myVM \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

Protokół SSH z maszyną Wirtualną za pomocą *publicznego adresu IP* wyświetlany w danych wyjściowych poprzedniego polecenia. Utwórz partycję i system plików, a następnie zainstalować dysk danych. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z maszyny Wirtualnej z systemem Linux zainstaluj nowy dysk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Zamknij sesję SSH.


## <a name="encrypt-the-virtual-machine"></a>Szyfrowanie maszyny wirtualnej


Szyfrowanie maszyny Wirtualnej za pomocą [Włącz az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-enable):

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Zajmuje trochę czasu na ukończenie procesu szyfrowania dysku. Monitorowanie stanu procesu o [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group $resourcegroup --name myVM --query 'status'
```

Po ukończeniu dane wyjściowe będą podobne do poniższego przykładu:

```json
[
  {
    "code": "ProvisioningState/succeeded",
    "displayStatus": "Provisioning succeeded",
    "level": "Info",
    "message": "Encryption succeeded for all volumes",
    "time": null
  }
]
```


## <a name="add-additional-data-disks"></a>Dodawanie dodatkowego dysku z danymi
Po zaszyfrowanych dysków z danymi, możesz dodać dodatkowe dyski wirtualne z maszyną wirtualną i ich szyfrowania. 

Po dodaniu dysku danych do maszyny Wirtualnej, uruchom ponownie polecenie, aby zaszyfrować dyski wirtualne w następujący sposób:

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type data
```


## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać więcej informacji dotyczących zarządzania usługi Azure Key Vault, w tym usuwanie kluczy kryptograficznych i magazynów, zobacz [Zarządzanie Key Vault przy użyciu interfejsu wiersza polecenia](../../key-vault/key-vault-manage-with-cli2.md).
* Aby uzyskać więcej informacji o szyfrowaniu dysku, takich jak przygotowywanie zaszyfrowanych niestandardową maszynę Wirtualną do przekazania na platformę Azure, zobacz [usługi Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
