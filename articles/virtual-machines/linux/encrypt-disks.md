---
title: Szyfrowanie dysków na Maszynę wirtualną systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Jak zaszyfrować dysków wirtualnych w maszyny Wirtualnej systemu Linux, aby zwiększyć bezpieczeństwo, za pomocą 2.0 interfejsu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: iainfou
ms.openlocfilehash: c35cd220eab26300404a039467e1a0b35592f23d
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824782"
---
# <a name="how-to-encrypt-virtual-disks-on-a-linux-vm"></a>Jak zaszyfrować dyski wirtualne na maszynie Wirtualnej systemu Linux
Rozszerzone maszyny wirtualnej (VM) zabezpieczeń i zgodności mogą być szyfrowane dyski wirtualne i samej maszyny Wirtualnej. Maszyny wirtualne są szyfrowane za pomocą kluczy kryptograficznych, które są już zabezpieczone w usłudze Azure Key Vault. Kontrolowanie tych kluczy kryptograficznych i przeprowadzić inspekcję ich używania. Ten artykuł zawiera szczegóły dotyczące sposobu szyfrowania dysków wirtualnych na Maszynę wirtualną systemu Linux przy użyciu 2.0 interfejsu wiersza polecenia platformy Azure. Czynności te można również wykonać przy użyciu [interfejsu wiersza polecenia platformy Azure w wersji 1.0](encrypt-disks-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> Upewnij się, że sterownik vfat włączone w Twojej maszyny Wirtualnej systemu Linux. Pewne zabezpieczenia rozwiązań, takich jak poproś wyłączyć sterownik vfat testów porównawczych konfiguracji (ci). Sterownik ten jest wymagane szyfrowanie działało, po zakończeniu procesu.

## <a name="quick-commands"></a>Szybkie polecenia
Jeśli chcesz szybko wykonać zadanie, następujące szczegóły sekcji bazie polecenia do szyfrowania dysków wirtualnych w maszynie Wirtualnej. Bardziej szczegółowe informacje i kontekst dla każdego kroku można znaleźć pozostałej części dokumentu, [uruchamiania tutaj](#overview-of-disk-encryption).

Należy najnowszej [Azure CLI 2.0](/cli/azure/install-az-cli2) zainstalowane i zalogowany do konta platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login). W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametru zawierają *myResourceGroup*, *klucze*, i *myVM*.

Najpierw włączyć dostawcy usługi Azure Key Vault w ramach Twojej subskrypcji platformy Azure z [az dostawcy rejestru](/cli/azure/provider#az_provider_register) i utworzyć nową grupę zasobów o [Tworzenie grupy az](/cli/azure/group#az_group_create). Poniższy przykład tworzy nazwę grupy zasobów *myResourceGroup* w *eastus* lokalizacji:

```azurecli
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location eastus
```

Tworzenie usługi Azure Key Vault z [az keyvault utworzyć](/cli/azure/keyvault#az_keyvault_create) i włączyć usługi Key Vault służących do szyfrowania dysku. Określ unikatową nazwę usługi Key Vault *keyvault_name* w następujący sposób:

```azurecli
keyvault_name=myuniquekeyvaultname
az keyvault create \
    --name $keyvault_name \
    --resource-group myResourceGroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Tworzenie klucza kryptograficznego w magazyn kluczy o [Utwórz klucz keyvault az](/cli/azure/keyvault/key#az_keyvault_key_create). W poniższym przykładzie jest tworzony klucz o nazwie *klucze*:

```azurecli
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```

Tworzenie nazwy głównej usługi za pomocą usługi Azure Active Directory z [az ad sp utworzyć do rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac). Nazwy głównej usługi obsługuje uwierzytelnianie i wymiany kluczy kryptograficznych z magazynu kluczy. Poniższy przykład odczytuje wartości Identyfikatora podmiotu zabezpieczeń usługi i hasło do użycia w późniejszym polecenia:

```azurecli
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

Hasło jest tylko dane wyjściowe w przypadku tworzenia nazwy głównej usługi. W razie potrzeby można wyświetlać i Zapisz hasło (`echo $sp_password`). Możesz wyświetlić listę Twojej nazwy główne usług z [listy sp ad az](/cli/azure/ad/sp#az_ad_sp_list) i wyświetlić dodatkowe informacje o określonej usługi głównej z [az ad sp Pokaż](/cli/azure/ad/sp#az_ad_sp_show).

Ustaw uprawnienia na magazyn kluczy o [keyvault az set-policy](/cli/azure/keyvault#az_keyvault_set_policy). W poniższym przykładzie identyfikator podmiotu zabezpieczeń usługi jest dostarczana przez poprzednie polecenie:

```azurecli
az keyvault set-policy --name $keyvault_name --spn $sp_id \
    --key-permissions wrapKey \
    --secret-permissions set
```

Utwórz maszynę Wirtualną z [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) i dołączenie dysku danych 5 Gb. Niektóre obrazy marketplace obsługuje szyfrowanie dysków. Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* przy użyciu *CentOS 7.2n* obrazu:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image OpenLogic:CentOS:7.2n:7.2.20160629 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

SSH przy użyciu maszyny Wirtualnej *publicznego adresu IP* wyświetlany w danych wyjściowych poprzednie polecenie. Tworzenie partycji i systemu plików, a następnie zainstalować dysk z danymi. Aby uzyskać więcej informacji, zobacz [Połącz z maszyny Wirtualnej systemu Linux, aby zainstalować nowy dysk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Zamknij sesję SSH.

Szyfrowanie maszyny Wirtualnej z [włączyć szyfrowanie maszyny wirtualnej az](/cli/azure/vm/encryption#az_vm_encryption_enable). W poniższym przykładzie użyto *$sp_id* i *$sp_password* zmienne z poprzednim `ad sp create-for-rbac` polecenie:

```azurecli
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Trwa pewien czas na ukończenie procesu szyfrowania dysku. Monitorowanie stanu procesu z [Pokaż szyfrowania maszyny wirtualnej az](/cli/azure/vm/encryption#az_vm_encryption_show):

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

Pokazuje stan **EncryptionInProgress**. Poczekaj, aż stan systemu operacyjnego na dysku raporty **VMRestartPending**, ponowne uruchomienie maszyny Wirtualnej z [ponownego uruchomienia maszyny wirtualnej az](/cli/azure/vm#az_vm_restart):

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

Proces szyfrowania dysku zostanie sfinalizowana podczas rozruchu, więc odczekaj kilka minut przed sprawdzeniem stanu szyfrowania ponownie, podając [Pokaż szyfrowania maszyny wirtualnej az](/cli/azure/vm/encryption#az_vm_encryption_show):

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

Stan Zgłoś teraz dysk systemu operacyjnego i dysku danych jako **zaszyfrowane**.


## <a name="overview-of-disk-encryption"></a>Omówienie szyfrowania dysków
Dyski wirtualne na maszynach wirtualnych systemu Linux są szyfrowane za pomocą rest [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Jest bezpłatna dla szyfrowania dysków wirtualnych na platformie Azure. Klucze szyfrowania są przechowywane w usługi Azure Key Vault przy użyciu ochrony oprogramowania, lub możesz zaimportować lub wygenerować klucze w sprzętowych modułach zabezpieczeń (HSM) certyfikowane do FIPS 140-2 poziom 2 standardów. Zachowanie kontroli nad tych kluczy kryptograficznych i przeprowadzić inspekcję ich używania. Te klucze szyfrowania są używane do szyfrowania i odszyfrowywania wirtualnych dysków dołączonych do maszyny Wirtualnej. Nazwy głównej usługi Azure Active Directory zapewnia mechanizm bezpiecznego do wystawiania tych kluczy kryptograficznych, ponieważ maszyny wirtualne są zasilane i wyłączanie.

Proces szyfrowania maszyny Wirtualnej jest następujący:

1. Tworzenie klucza kryptograficznego w usłudze Azure Key Vault.
2. Skonfiguruj klucza kryptograficznego, który może być używany do szyfrowania dysków.
3. Aby odczytać klucza kryptograficznego z usługi Azure Key Vault, utworzyć usługę Azure Active Directory podmiot z odpowiednimi uprawnieniami.
4. Wydaj polecenie wirtualnych dysków, określając usługi Azure Active Directory usługa podmiotu zabezpieczeń i odpowiednie klucza kryptograficznego używanego do szyfrowania.
5. Nazwy głównej usługi Azure Active Directory żądań wymaganego klucza kryptograficznego z usługi Azure Key Vault.
6. Dyski wirtualne są szyfrowane za pomocą podanego klucza kryptograficznego.

## <a name="encryption-process"></a>Proces szyfrowania
Szyfrowanie dysków opiera się na następujące dodatkowe składniki:

* **Usługa Azure Key Vault** — używane do ochrony kluczy kryptograficznych i kluczy tajnych używanych przez proces szyfrowania i odszyfrowywania dysku.
  * Jeśli istnieje, można użyć istniejącego magazynu kluczy Azure. Nie trzeba przeznaczyć magazyn kluczy szyfrowania dysków.
  * Aby rozdzielić granice administracyjne i widoczności klucza, możesz utworzyć dedykowany magazyn kluczy.
* **Usługa Azure Active Directory** — obsługuje bezpiecznej wymiany wymagane klucze szyfrowania i uwierzytelniania dla żądanej akcji.
  * Zazwyczaj można użyć istniejącego wystąpienia usługi Azure Active Directory, do przechowywania aplikacji.
  * Nazwy głównej usługi zapewnia mechanizm bezpiecznego do żądania i wydawane odpowiednich kluczy kryptograficznych. Nie opracowujesz aplikację rzeczywista, która integruje się z usługą Azure Active Directory.

## <a name="requirements-and-limitations"></a>Wymagania i ograniczenia
Obsługiwane scenariusze i wymagania dotyczące szyfrowania dysków:

* Następujący serwer z systemem Linux jednostki SKU - Ubuntu, CentOS, SUSE i SUSE Linux Enterprise Server (SLES) i Red Hat Enterprise Linux.
* Wszystkie zasoby (na przykład usługi Key Vault, konta magazynu i maszyny Wirtualnej) muszą być w tym samym regionie Azure i subskrypcji.
* Standardowa A, D, DS, G, GS, itp., serii maszyn wirtualnych.
* Aktualizowanie kluczy kryptograficznych w już zaszyfrowany maszyny Wirtualnej systemu Linux.
* Sterownik VFAT jest włączony na maszynie wirtualnej systemu Linux.

Szyfrowanie dysku nie jest obecnie obsługiwane w następujących scenariuszach:

* Maszyny wirtualne warstwy podstawowa.
* Maszyny wirtualne utworzone przy użyciu klasycznego modelu wdrożenia.
* Wyłączenie szyfrowania dysku systemu operacyjnego na maszynach wirtualnych systemu Linux.
* Użyj niestandardowych obrazów systemu Linux.

Aby uzyskać więcej informacji o obsługiwanych scenariuszach i ograniczeniach, zobacz [szyfrowania dysków Azure dla maszyn wirtualnych IaaS](../../security/azure-security-disk-encryption.md)


## <a name="create-azure-key-vault-and-keys"></a>Tworzenie usługi Azure Key Vault i kluczy
Należy najnowszej [Azure CLI 2.0](/cli/azure/install-az-cli2) zainstalowane i zalogowany do konta platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login). W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametru zawierają *myResourceGroup*, *klucze*, i *myVM*.

Pierwszym krokiem jest do utworzenia magazynu kluczy Azure do przechowywania kluczy kryptograficznych. Usługa Azure Key Vault można przechowywać kluczy, kluczy tajnych lub haseł, które umożliwiają bezpieczne wdrożenie ich w aplikacji i usług. Szyfrowanie dysków wirtualnych Key Vault służy do przechowywania klucza kryptograficznego używanego do szyfrowania lub odszyfrowywania dysków wirtualnych.

Włącz dostawcy usługi Azure Key Vault w ramach Twojej subskrypcji platformy Azure z [az dostawcy rejestru](/cli/azure/provider#az_provider_register) i utworzyć nową grupę zasobów o [Tworzenie grupy az](/cli/azure/group#az_group_create). Poniższy przykład tworzy nazwę grupy zasobów *myResourceGroup* w `eastus` lokalizacji:

```azurecli
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location eastus
```

Usługa Azure Key Vault zawierający klucze kryptograficzne i zasoby obliczeniowe skojarzone, takie jak magazyn i samej maszyny Wirtualnej musi znajdować się w tym samym regionie. Tworzenie usługi Azure Key Vault z [az keyvault utworzyć](/cli/azure/keyvault#az_keyvault_create) i włączyć usługi Key Vault służących do szyfrowania dysku. Określ unikatową nazwę usługi Key Vault *keyvault_name* w następujący sposób:

```azurecli
keyvault_name=myuniquekeyvaultname
az keyvault create \
    --name $keyvault_name \
    --resource-group myResourceGroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Mogą być przechowywane przy użyciu oprogramowania lub sprzętu modelu zabezpieczeń (HSM) ochronę kluczy kryptograficznych. Użycie modułów HSM wymaga premium magazynu kluczy. Brak dodatkowych kosztów tworzenia premium magazynu kluczy, a nie standardowy magazyn kluczy, którego przechowuje klucze chronione przez oprogramowanie. Aby utworzyć premium magazynu kluczy, w poprzednim kroku Dodaj `--sku Premium` do polecenia. W poniższym przykładzie użyto klucze chronione przez oprogramowanie, od momentu utworzenia standardowy magazyn kluczy.

W przypadku obu modeli ochrony platformy Azure musi otrzymać dostęp do żądania kluczy kryptograficznych, podczas rozruchu maszyny Wirtualnej do odszyfrowania dysków wirtualnych. Tworzenie klucza kryptograficznego w magazyn kluczy o [Utwórz klucz keyvault az](/cli/azure/keyvault/key#az_keyvault_key_create). W poniższym przykładzie jest tworzony klucz o nazwie *klucze*:

```azurecli
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```


## <a name="create-the-azure-active-directory-service-principal"></a>Tworzenie nazwy głównej usługi Azure Active Directory
Podczas dyski wirtualne są zaszyfrowanie lub odszyfrowanie, należy określić konto w celu obsługi uwierzytelniania i wymiany kluczy kryptograficznych z magazynu kluczy. To konto, nazwy głównej usługi Azure Active Directory umożliwia platformy Azure zażądać odpowiednich kluczy kryptograficznych w imieniu maszyny Wirtualnej. Domyślne wystąpienie usługi Azure Active Directory jest dostępne w Twojej subskrypcji, chociaż w wielu organizacjach są wyposażone w dedykowane katalogów usługi Azure Active Directory.

Tworzenie nazwy głównej usługi za pomocą usługi Azure Active Directory z [az ad sp utworzyć do rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac). Poniższy przykład odczytuje wartości dla nazwy głównej usługi Id i hasło do wykorzystania w późniejszym polecenia:

```azurecli
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

Hasło jest wyświetlane tylko, tworząc usługę podmiotu zabezpieczeń. W razie potrzeby można wyświetlać i Zapisz hasło (`echo $sp_password`). Możesz wyświetlić listę Twojej nazwy główne usług z [listy sp ad az](/cli/azure/ad/sp#az_ad_sp_list) i wyświetlić dodatkowe informacje o określonej usługi głównej z [az ad sp Pokaż](/cli/azure/ad/sp#az_ad_sp_show).

Pomyślnie szyfrowania lub odszyfrowywania dysków wirtualnych, uprawnienia do klucza kryptograficznego, przechowywane w magazynie klucz musi mieć ustawioną zezwolenie na nazwę główną usługi Azure Active Directory do odczytu kluczy. Ustaw uprawnienia na magazyn kluczy o [keyvault az set-policy](/cli/azure/keyvault#az_keyvault_set_policy). W poniższym przykładzie identyfikator podmiotu zabezpieczeń usługi jest dostarczana przez poprzednie polecenie:

```azurecli
az keyvault set-policy --name $keyvault_name --spn $sp_id \
  --key-permissions wrapKey \
  --secret-permissions set
```


## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej
Utwórz maszynę Wirtualną do szyfrowania z [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) i dołączenie dysku danych 5 Gb. Niektóre obrazy marketplace obsługuje szyfrowanie dysków. Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* przy użyciu *CentOS 7.2n* obrazu:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image OpenLogic:CentOS:7.2n:7.2.20160629 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

SSH przy użyciu maszyny Wirtualnej *publicznego adresu IP* wyświetlany w danych wyjściowych poprzednie polecenie. Tworzenie partycji i systemu plików, a następnie zainstalować dysk z danymi. Aby uzyskać więcej informacji, zobacz [Połącz z maszyny Wirtualnej systemu Linux, aby zainstalować nowy dysk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Zamknij sesję SSH.


## <a name="encrypt-virtual-machine"></a>Szyfrowanie maszyny wirtualnej
Do szyfrowania dysków wirtualnych, można przenosić ze sobą wszystkie poprzednie składniki:

1. Podaj nazwę główną usługi Azure Active Directory i hasło.
2. Określ Key Vault w celu przechowywania metadanych dla zaszyfrowanych dysków.
3. Określ klucze kryptograficzne służące do rzeczywistego szyfrowania i odszyfrowywania.
4. Określ, czy chcesz zaszyfrować dysk systemu operacyjnego, dyski danych lub all.

Szyfrowanie maszyny Wirtualnej z [włączyć szyfrowanie maszyny wirtualnej az](/cli/azure/vm/encryption#az_vm_encryption_enable). W poniższym przykładzie użyto *$sp_id* i *$sp_password* zmienne z poprzednim [az ad sp utworzyć do rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) polecenia:

```azurecli
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Trwa pewien czas na ukończenie procesu szyfrowania dysku. Monitorowanie stanu procesu z [Pokaż szyfrowania maszyny wirtualnej az](/cli/azure/vm/encryption#az_vm_encryption_show):

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

Wynik jest podobny do poniższego przykładu skrócona:

```json
[
  "dataDisk": "EncryptionInProgress",
  "osDisk": "EncryptionInProgress",
]
```

Poczekaj, aż stan systemu operacyjnego na dysku raporty **VMRestartPending**, ponowne uruchomienie maszyny Wirtualnej z [ponownego uruchomienia maszyny wirtualnej az](/cli/azure/vm#az_vm_restart):

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

Proces szyfrowania dysku zostanie sfinalizowana podczas rozruchu, więc odczekaj kilka minut przed sprawdzeniem stanu szyfrowania ponownie, podając **Pokaż szyfrowania maszyny wirtualnej az**:

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

Stan Zgłoś teraz dysk systemu operacyjnego i dysku danych jako **zaszyfrowane**.


## <a name="add-additional-data-disks"></a>Dodania dodatkowego dysku z danymi
Po zaszyfrowanych dysków z danymi, możesz później dodać dodatkowych dysków wirtualnych do maszyny Wirtualnej i również ich szyfrowania. Na przykład pozwala dodać drugi wirtualny dysk do maszyny Wirtualnej w następujący sposób:

```azurecli
az vm disk attach-new --resource-group myResourceGroup --vm-name myVM --size-in-gb 5
```

Ponownie uruchom polecenie do szyfrowania dysków wirtualnych w następujący sposób:

```azurecli
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
* Aby uzyskać więcej informacji na temat zarządzania usługą Azure Key Vault, łącznie z usunięciem kluczy kryptograficznych i magazyny, zobacz [Zarządzanie Key Vault przy użyciu interfejsu wiersza polecenia](../../key-vault/key-vault-manage-with-cli2.md).
* Aby uzyskać więcej informacji o szyfrowaniu dysków, takich jak przygotowywanie zaszyfrowanego niestandardowego maszyny Wirtualnej do przekazania do platformy Azure, zobacz [szyfrowania dysków Azure](../../security/azure-security-disk-encryption.md).
