---
title: Szyfrowanie dysków na maszynie Wirtualnej systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Jak zaszyfrować dyski wirtualne na maszynie Wirtualnej systemu Linux w celu uzyskania zwiększonych zabezpieczeń przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0
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
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 75ec087536d6f833a9a2106b1fdf4ed1fd73ef8e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38634624"
---
# <a name="how-to-encrypt-a-linux-virtual-machine-in-azure"></a>Jak zaszyfrować maszyny wirtualnej z systemem Linux na platformie Azure
Rozszerzone maszynę wirtualną (VM), zabezpieczeń i zgodności mogą być szyfrowane dyski wirtualne, a samej maszyny Wirtualnej. Maszyny wirtualne są szyfrowane przy użyciu kluczy kryptograficznych, które są zabezpieczone w usłudze Azure Key Vault. Możesz kontrolować klucze kryptograficzne i przeprowadzać ich inspekcje ich użycie. Ten artykuł szczegółowo opisuje sposób szyfrowania dysków wirtualnych na Maszynę wirtualną systemu Linux przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i korzystać z interfejsu wiersza polecenia lokalnie, ten artykuł wymaga, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="overview-of-disk-encryption"></a>Omówienie szyfrowania dysku
Dyski wirtualne na maszynach wirtualnych z systemem Linux są szyfrowane, gdy rest przy użyciu [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Nie ma opłat do szyfrowania dysków wirtualnych na platformie Azure. Klucze szyfrowania są przechowywane w usłudze Azure Key Vault przy użyciu ochrony oprogramowania lub można importować lub generować klucze w sprzętowych modułach zabezpieczeń (HSM) certyfikowane zgodnych ze standardami FIPS 140-2 poziom 2 standardy. Zachowanie kontroli nad te klucze kryptograficzne i przeprowadzać ich inspekcje ich użycie. Te klucze szyfrowania są używane do szyfrowania i odszyfrowywania dyski wirtualne dołączone do maszyny Wirtualnej. Jednostki usługi Azure Active Directory zapewnia mechanizm bezpiecznego wydawania te klucze szyfrowania, jak maszyny wirtualne są włączone, włączać i wyłączać.

Proces szyfrowania maszyny Wirtualnej jest w następujący sposób:

1. Utwórz klucz kryptograficzny w usłudze Azure Key Vault.
2. Konfigurowanie klucza kryptograficznego, może być używany do szyfrowania dysków.
3. Aby odczytać klucz kryptograficzny z usługi Azure Key Vault, tworzenie usługi Azure Active Directory jednostki z odpowiednimi uprawnieniami.
4. Wydaj polecenie Szyfrowanie dysków wirtualnych, określanie usługi Azure Active Directory service główną i odpowiedni klucz kryptograficzny ma być używany.
5. Jednostki usługi Azure Active Directory żąda wymaganego klucza kryptograficznego z usługi Azure Key Vault.
6. Wirtualne dyski są szyfrowane przy użyciu podanego klucza kryptograficznego.

## <a name="encryption-process"></a>Proces szyfrowania
Szyfrowanie dysków opiera się na następujących dodatkowych składników:

* **Usługa Azure Key Vault** — używane do ochrony kluczy kryptograficznych i wpisów tajnych używanych dla procesu szyfrowania i odszyfrowywania dysku.
  * Jeśli istnieje, można użyć istniejącej usługi Azure Key Vault. Nie masz możliwości szyfrowania dysków za przeznaczyć usługi Key Vault.
  * Do oddzielania granice administracyjne i widoczność kluczy, można utworzyć dedykowane usługi Key Vault.
* **Usługa Azure Active Directory** — obsługuje bezpiecznej wymiany wymagane klucze szyfrowania i uwierzytelniania dla żądanej akcji.
  * Zazwyczaj można użyć istniejącego wystąpienia usługi Azure Active Directory, do przechowywania aplikacji.
  * Nazwa główna usługi zapewnia mechanizm bezpiecznego do żądania i wydawane odpowiednich kluczy kryptograficznych. Nie tworzysz aplikację rzeczywista, która integruje się z usługą Azure Active Directory.

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
az group create --name myResourceGroup --location eastus
```

Usługi Azure Key Vault, zawierający klucze kryptograficzne i zasoby obliczeniowe skojarzone, takie jak storage i samej maszyny Wirtualnej muszą znajdować się w tym samym regionie. Tworzenie usługi Azure Key Vault przy użyciu [tworzenie az keyvault](/cli/azure/keyvault#az-keyvault-create) i włączyć usługi Key Vault do użytku z szyfrowania dysków. Określ unikatową nazwę usługi Key Vault *keyvault_name* w następujący sposób:

```azurecli-interactive
keyvault_name=myuniquekeyvaultname
az keyvault create \
    --name $keyvault_name \
    --resource-group myResourceGroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Można przechowywać klucze szyfrowania przy użyciu oprogramowania lub ochrona sprzętu modelu zabezpieczeń (HSM). Przy użyciu sprzętowego modułu zabezpieczeń wymaga usługi Key Vault w wersji premium. Brak dodatkowych kosztów, do tworzenia premium usługi Key Vault, a nie standardowy usługi Key Vault, który przechowuje klucze chronione programowo. Aby utworzyć premium usługi Key Vault, w poprzednim kroku należy dodać `--sku Premium` do polecenia. W poniższym przykładzie użyto kluczy chronionych programowo, od momentu utworzenia standardowe usługi Key Vault.

Oba modele ochrony platformy Azure musi otrzymać dostęp do zażądać kluczy kryptograficznych w przypadku, gdy maszyna wirtualna zostanie do odszyfrowania dysków wirtualnych. Utwórz klucz kryptograficzny w usłudze Key Vault przy użyciu [tworzenie az keyvault key](/cli/azure/keyvault/key#az-keyvault-key-create). Poniższy przykład tworzy klucz o nazwie *klucze*:

```azurecli-interactive
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```


## <a name="create-an-azure-active-directory-service-principal"></a>Tworzenie jednostki usługi Azure Active Directory
Jeśli wirtualne dyski są szyfrowane lub odszyfrować, określasz konto, aby obsługiwać uwierzytelnianie i wymiany kluczy kryptograficznych z usługi Key Vault. To konto jednostki usługi Azure Active Directory umożliwia platformy Azure, aby zażądać odpowiednie kluczy kryptograficznych w imieniu maszyny Wirtualnej. Domyślne wystąpienie usługi Azure Active Directory jest dostępna w ramach subskrypcji, chociaż w wielu organizacjach są wyposażone w dedykowane katalogami usługi Azure Active Directory.

Tworzenie jednostki usługi przy użyciu usługi Azure Active Directory za pomocą [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac). Poniższy przykład odczytuje wartości dla nazwy głównej usługi i hasło do użycia w poleceniach nowszej:

```azurecli-interactive
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

Hasło jest wyświetlane tylko wtedy, gdy należy utworzyć jednostkę usługi. Jeśli to konieczne, Wyświetl i Zapisz hasło (`echo $sp_password`). Możesz wyświetlić listę z jednostki usługi przy użyciu [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) i wyświetlić dodatkowe informacje o określonej nazwy głównej usługi z [az ad sp show](/cli/azure/ad/sp#az-ad-sp-show).

Aby pomyślnie szyfrowania lub odszyfrowywania dysków wirtualnych, uprawnienia klucza kryptograficznego, przechowywane w usłudze Key Vault musi być równa zezwala na nazwy głównej usługi Azure Active Directory do odczytu klucze. Ustaw uprawnienia usługi Key Vault przy użyciu [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). W poniższym przykładzie identyfikator jednostki usługi jest dostarczana z poprzedniego polecenia:

```azurecli-interactive
az keyvault set-policy --name $keyvault_name --spn $sp_id \
  --key-permissions wrapKey \
  --secret-permissions set
```


## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Utwórz Maszynę wirtualną za pomocą [tworzenie az vm](/cli/azure/vm#az-vm-create) i dołączanie dysku danych 5 Gb. Tylko niektórych obrazów z portalu marketplace obsługuje szyfrowania dysku. Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* przy użyciu *Ubuntu 16.04 LTS* obrazu:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

Protokół SSH z maszyną Wirtualną za pomocą *publicznego adresu IP* wyświetlany w danych wyjściowych poprzedniego polecenia. Utwórz partycję i system plików, a następnie zainstalować dysk danych. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z maszyny Wirtualnej z systemem Linux zainstaluj nowy dysk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Zamknij sesję SSH.


## <a name="encrypt-the-virtual-machine"></a>Szyfrowanie maszyny wirtualnej
Aby zaszyfrować dyski wirtualne, możesz Połącz poprzednie składniki:

1. Określ nazwy głównej usługi Azure Active Directory i hasło.
2. Określ usługi Key Vault do przechowywania metadanych dla zaszyfrowanych dysków.
3. Określ klucze kryptograficzne służące do rzeczywistego szyfrowania i odszyfrowywania.
4. Określ, czy chcesz zaszyfrować dysk systemu operacyjnego, dyski z danymi lub wszystkie.

Szyfrowanie maszyny Wirtualnej za pomocą [Włącz az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-enable). W poniższym przykładzie użyto *$sp_id* i *$sp_password* zmiennych z poprzednim [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) polecenia:

```azurecli-interactive
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Zajmuje trochę czasu na ukończenie procesu szyfrowania dysku. Monitorowanie stanu procesu o [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group myResourceGroup --name myVM
```

Rezultat jest podobny do poniższego przykładu obcięte:

```json
[
  "dataDisk": "EncryptionInProgress",
  "osDisk": "EncryptionInProgress"
]
```

Zaczekaj, aż stan systemu operacyjnego dla dysku raporty **VMRestartPending**, uruchom ponownie maszynę Wirtualną za pomocą [ponownego uruchamiania maszyny wirtualnej az](/cli/azure/vm#az-vm-restart):

```azurecli-interactive
az vm restart --resource-group myResourceGroup --name myVM
```

Proces szyfrowania dysku jest aktualnie finalizowana podczas procesu rozruchu, więc odczekaj kilka minut przed sprawdzeniem stanu szyfrowania ponownie, używając [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group myResourceGroup --name myVM
```

Stan teraz powinien wysyłać raporty dysku systemu operacyjnego i dysk danych jako **zaszyfrowane**.


## <a name="add-additional-data-disks"></a>Dodawanie dodatkowego dysku z danymi
Gdy zaszyfrowanych dysków z danymi, można później dodać dodatkowe dyski wirtualne z maszyną wirtualną i również zaszyfrować. Na przykład pozwala dodać drugi dysk wirtualny z maszyną wirtualną w następujący sposób:

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --disk myDataDisk \
    --new \
    --size-gb 5
```

Uruchom ponownie polecenie, aby zaszyfrować dyski wirtualne w następujący sposób:

```azurecli-interactive
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```


## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać więcej informacji dotyczących zarządzania usługi Azure Key Vault, w tym usuwanie kluczy kryptograficznych i magazynów, zobacz [Zarządzanie Key Vault przy użyciu interfejsu wiersza polecenia](../../key-vault/key-vault-manage-with-cli2.md).
* Aby uzyskać więcej informacji o szyfrowaniu dysku, takich jak przygotowywanie zaszyfrowanych niestandardową maszynę Wirtualną do przekazania na platformę Azure, zobacz [usługi Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
