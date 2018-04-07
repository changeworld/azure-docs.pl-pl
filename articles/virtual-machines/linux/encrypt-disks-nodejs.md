---
title: Szyfrowanie dysków na Maszynę wirtualną systemu Linux z interfejsu wiersza polecenia platformy Azure w wersji 1.0 | Dokumentacja firmy Microsoft
description: Jak zaszyfrować dysków na Maszynę wirtualną systemu Linux przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure i modelu wdrażania usługi Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2017
ms.author: iainfou
ms.openlocfilehash: d1704ba37b1d2acc595cb0c354b22bfcf1c57036
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="encrypt-disks-on-a-linux-vm-using-the-azure-cli-10"></a>Szyfrowanie dysków na Maszynę wirtualną systemu Linux przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure
Ulepszone maszyny wirtualnej (VM) zabezpieczeń i zgodności dyski wirtualne na platformie Azure mogą być szyfrowane w stanie spoczynku. Dyski są szyfrowane za pomocą kluczy kryptograficznych, które są już zabezpieczone w usłudze Azure Key Vault. Kontrolowanie tych kluczy kryptograficznych i przeprowadzić inspekcję ich używania. Ten artykuł zawiera szczegóły dotyczące sposobu szyfrowania dysków wirtualnych na Maszynę wirtualną systemu Linux przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure i modelu wdrażania usługi Resource Manager.

## <a name="cli-versions-to-complete-the-task"></a>Wersje interfejsu wiersza polecenia umożliwiające wykonanie zadania
Zadanie można wykonać przy użyciu jednej z następujących wersji interfejsu wiersza polecenia:

- [Azure CLI 1.0](#quick-commands) — nasze interfejsu wiersza polecenia dla klasycznego i zasobów zarządzania wdrażania modeli (w tym artykule)
- [Interfejs wiersza polecenia platformy Azure w wersji 2.0](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — nasz interfejs wiersza polecenia nowej generacji dla modelu wdrażania na potrzeby zarządzania zasobami

## <a name="quick-commands"></a>Szybkie polecenia
Jeśli chcesz szybko wykonać zadanie, następujące szczegóły sekcji bazie polecenia do szyfrowania dysków wirtualnych w maszynie Wirtualnej. Bardziej szczegółowe informacje i kontekst dla każdego kroku można znaleźć pozostałej części dokumentu, [uruchamiania tutaj](#overview-of-disk-encryption).

Należy [najnowsze Azure CLI 1.0](../../xplat-cli-install.md) zainstalowane i zarejestrowane w trybie Menedżera zasobów w następujący sposób:

```azurecli
azure config mode arm
```

W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametru zawierają `myResourceGroup`, `myKeyVault`, i `myVM`.

Najpierw należy włączyć dostawcy usługi Azure Key Vault w ramach Twojej subskrypcji platformy Azure i Utwórz grupę zasobów. Poniższy przykład tworzy nazwę grupy zasobów `myResourceGroup` w `WestUS` lokalizacji:

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Tworzenie usługi Azure Key Vault. Poniższy przykład tworzy magazyn kluczy o nazwie `myKeyVault`:

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Tworzenie klucza kryptograficznego w magazyn kluczy i Włącz szyfrowanie dysków. W poniższym przykładzie jest tworzony klucz o nazwie `myKey`:

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```

Tworzenie punktu końcowego za pomocą usługi Azure Active Directory do obsługi uwierzytelniania i wymiany kluczy kryptograficznych z magazynu kluczy. `--home-page` i `--identifier-uris` musi być rzeczywiste adresów obsługi routingu. W przypadku najwyższy poziom zabezpieczeń kluczy tajnych klientów należy użyć zamiast hasła. Interfejsu wiersza polecenia Azure aktualnie nie można wygenerować kluczy tajnych klientów. Kluczy tajnych klientów mogą być generowane tylko w portalu Azure. Poniższy przykład tworzy punkt końcowy usługi Azure Active Directory o nazwie `myAADApp` i używa hasła `myPassword`. Określ własnego hasła w następujący sposób:

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Uwaga `applicationId` wyświetlany w danych wyjściowych z poprzedniego polecenia. Ten identyfikator aplikacji jest używany w poniższych krokach:

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```

Dodaj dysk danych do istniejącej maszyny Wirtualnej. W poniższym przykładzie dodano dysk z danymi do maszyny Wirtualnej o nazwie `myVM`:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Przejrzyj szczegóły magazyn kluczy i klucza, który został utworzony. Potrzebny jest identyfikator magazynu kluczy, identyfikator URI i klucz adresu URL w ostatnim kroku. Poniższy przykład przegląda szczegóły dla usługi Key Vault o nazwie `myKeyVault` i klucz o nazwie `myKey`:

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Szyfrowanie dysków następujące wprowadzania własne nazwy parametru w całym:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Azure CLI nie zapewnia pełne błędy podczas procesu szyfrowania. Aby uzyskać dodatkowe informacje dotyczące rozwiązywania problemów, przejrzyj `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`. Poprzednie polecenie ma wiele zmiennych i nie może pobrać dużo wskazania przyczyny niepowodzenia procesu, przykład pełne polecenie będzie następujące:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Na koniec sprawdź stan szyfrowania ponownie, aby potwierdzić, że teraz został zaszyfrowany dysków wirtualnych. Poniższy przykład umożliwia sprawdzenie stanu maszyny wirtualnej o nazwie `myVM` w `myResourceGroup` grupy zasobów:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```

## <a name="overview-of-disk-encryption"></a>Omówienie szyfrowania dysków
Dyski wirtualne na maszynach wirtualnych systemu Linux są szyfrowane za pomocą rest [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Jest bezpłatna dla szyfrowania dysków wirtualnych na platformie Azure. Klucze szyfrowania są przechowywane w usługi Azure Key Vault przy użyciu ochrony oprogramowania, lub możesz zaimportować lub wygenerować klucze w sprzętowych modułach zabezpieczeń (HSM) certyfikowane do FIPS 140-2 poziom 2 standardów. Zachowanie kontroli nad tych kluczy kryptograficznych i przeprowadzić inspekcję ich używania. Te klucze szyfrowania są używane do szyfrowania i odszyfrowywania wirtualnych dysków dołączonych do maszyny Wirtualnej. Punkt końcowy usługi Azure Active Directory zapewnia mechanizm bezpiecznego do wystawiania tych kluczy kryptograficznych, ponieważ maszyny wirtualne są zasilane i wyłączanie.

Proces szyfrowania maszyny Wirtualnej jest następujący:

1. Tworzenie klucza kryptograficznego w usłudze Azure Key Vault.
2. Skonfiguruj klucza kryptograficznego, który może być używany do szyfrowania dysków.
3. Aby odczytać klucza kryptograficznego z usługi Azure Key Vault, utworzyć punktu końcowego za pomocą usługi Azure Active Directory z odpowiednimi uprawnieniami.
4. Wydaj polecenie dyski wirtualne, określenie punktu końcowego usługi Azure Active Directory i odpowiedniego klucza kryptograficznego używanego do szyfrowania.
5. Punkt końcowy usługi Azure Active Directory żądań wymaganego klucza kryptograficznego z usługi Azure Key Vault.
6. Dyski wirtualne są szyfrowane za pomocą podanego klucza kryptograficznego.

## <a name="supporting-services-and-encryption-process"></a>Obsługa usług i proces szyfrowania
Szyfrowanie dysków opiera się na następujące dodatkowe składniki:

* **Usługa Azure Key Vault** — używane do ochrony kluczy kryptograficznych i kluczy tajnych używanych przez proces szyfrowania i odszyfrowywania dysku.
  * Jeśli istnieje, można użyć istniejącego magazynu kluczy Azure. Nie trzeba przeznaczyć magazyn kluczy szyfrowania dysków.
  * Aby rozdzielić granice administracyjne i widoczności klucza, możesz utworzyć dedykowany magazyn kluczy.
* **Usługa Azure Active Directory** — obsługuje bezpiecznej wymiany wymagane klucze szyfrowania i uwierzytelniania dla żądanej akcji.
  * Zazwyczaj można użyć istniejącego wystąpienia usługi Azure Active Directory, do przechowywania aplikacji.
  * Aplikacja jest jeden punkt końcowy usługi Magazyn kluczy i maszyny wirtualnej do żądania i pobrać wystawiony odpowiednich kluczy kryptograficznych. Nie opracowujesz aplikację rzeczywista, która integruje się z usługą Azure Active Directory.

## <a name="requirements-and-limitations"></a>Wymagania i ograniczenia
Obsługiwane scenariusze i wymagania dotyczące szyfrowania dysków:

* Następujący serwer z systemem Linux jednostki SKU - Ubuntu, CentOS, SUSE i SUSE Linux Enterprise Server (SLES) i Red Hat Enterprise Linux.
* Wszystkie zasoby (na przykład usługi Key Vault, konta magazynu i maszyny Wirtualnej) muszą być w tym samym regionie Azure i subskrypcji.
* Standardowa A, D DS, G i GS seria maszyn wirtualnych.

Szyfrowanie dysku nie jest obecnie obsługiwane w następujących scenariuszach:

* Maszyny wirtualne warstwy podstawowa.
* Maszyny wirtualne utworzone przy użyciu klasycznego modelu wdrożenia.
* Wyłączenie szyfrowania dysku systemu operacyjnego na maszynach wirtualnych systemu Linux.
* Aktualizowanie kluczy kryptograficznych w już zaszyfrowany maszyny Wirtualnej systemu Linux.

## <a name="create-the-azure-key-vault-and-keys"></a>Tworzenie usługi Azure Key Vault i kluczy
Aby zakończyć w dalszej części tego podręcznika, należy [najnowsze Azure CLI 1.0](../../xplat-cli-install.md) zainstalowane i zarejestrowane w trybie Menedżera zasobów w następujący sposób:

```azurecli
azure config mode arm
```

W przykładach poleceń Zastąp wszystkie parametry przykład własne nazwy, lokalizacji i wartości klucza. W poniższych przykładach użyto Konwencji `myResourceGroup`, `myKeyVault`, `myAADApp`itp.

Pierwszym krokiem jest do utworzenia magazynu kluczy Azure do przechowywania kluczy kryptograficznych. Usługa Azure Key Vault można przechowywać kluczy, kluczy tajnych lub haseł, które umożliwiają bezpieczne wdrożenie ich w aplikacji i usług. Szyfrowanie dysków wirtualnych Key Vault służy do przechowywania klucza kryptograficznego używanego do szyfrowania lub odszyfrowywania dysków wirtualnych.

Włącz dostawcy usługi Azure Key Vault w Twojej subskrypcji platformy Azure, a następnie utwórz grupę zasobów. Poniższy przykład tworzy grupę zasobów o nazwie `myResourceGroup` w `WestUS` lokalizacji:

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Usługa Azure Key Vault zawierający klucze kryptograficzne i zasoby obliczeniowe skojarzone, takie jak magazyn i samej maszyny Wirtualnej musi znajdować się w tym samym regionie. Poniższy przykład tworzy magazynu kluczy Azure o nazwie `myKeyVault`:

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Mogą być przechowywane przy użyciu oprogramowania lub sprzętu modelu zabezpieczeń (HSM) ochronę kluczy kryptograficznych. Użycie modułów HSM wymaga premium magazynu kluczy. Brak dodatkowych kosztów tworzenia premium magazynu kluczy, a nie standardowy magazyn kluczy, którego przechowuje klucze chronione przez oprogramowanie. Aby utworzyć premium magazynu kluczy, w poprzednim kroku Dodaj `--sku Premium` do polecenia. W poniższym przykładzie użyto klucze chronione przez oprogramowanie, ponieważ utworzono standardowe magazynu kluczy.

W przypadku obu modeli ochrony platformy Azure musi otrzymać dostęp do żądania kluczy kryptograficznych, podczas rozruchu maszyny Wirtualnej do odszyfrowania dysków wirtualnych. Tworzenie klucza szyfrowania w ramach magazyn kluczy, a następnie włącz go do użytku z szyfrowania dysku wirtualnego. W poniższym przykładzie jest tworzony klucz o nazwie `myKey` i umożliwia szyfrowanie dysków:

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```


## <a name="create-the-azure-active-directory-application"></a>Utwórz aplikację usługi Azure Active Directory
Gdy dyski wirtualne są szyfrowane lub odszyfrować, służy do obsługi uwierzytelniania i wymiany kluczy kryptograficznych z usługi Key Vault punktu końcowego. Ten punkt końcowy, aplikację usługi Azure Active Directory umożliwia platformy Azure zażądać odpowiednich kluczy kryptograficznych w imieniu maszyny Wirtualnej. Domyślne wystąpienie usługi Azure Active Directory jest dostępne w Twojej subskrypcji, chociaż w wielu organizacjach są wyposażone w dedykowane katalogów usługi Azure Active Directory.

Jak Pełna aplikacji usługi Azure Active Directory, nie są tworzone `--home-page` i `--identifier-uris` parametrów w poniższym przykładzie musi być rzeczywiste adresów obsługi routingu. W poniższym przykładzie określa również klucz tajny opartego na hasłach zamiast generowania kluczy z w portalu Azure. Teraz Generowanie kluczy nie można wykonać z wiersza polecenia platformy Azure.

Tworzenie aplikacji usługi Azure Active Directory. Poniższy przykład tworzy aplikację o nazwie `myAADApp` i używa hasła `myPassword`. Określ własnego hasła w następujący sposób:

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Zanotuj `applicationId` który jest zwracany w danych wyjściowych z poprzedniego polecenia. Ten identyfikator aplikacji jest używany w niektórych z pozostałych kroków. Następnie należy utworzyć główną nazwę usługi (SPN), aby aplikacja jest dostępna w danym środowisku. Pomyślnie szyfrowania lub odszyfrowywania dysków wirtualnych, uprawnienia do klucza kryptograficznego, przechowywane w magazynie kluczy należy wybrać opcję zezwalania na stosowanie usługi Azure Active Directory do odczytu klucze.

Utwórz nazwę SPN i ustawić odpowiednie uprawnienia w następujący sposób:

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```


## <a name="add-a-virtual-disk-and-review-encryption-status"></a>Dodaj dysk wirtualny i sprawdzać stan szyfrowania
Do szyfrowania faktycznie niektóre dyski wirtualne, pozwala dodać dysk do istniejącej maszyny Wirtualnej. Dodaj dysk danych 5Gb do istniejącej maszyny Wirtualnej w następujący sposób:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Dyski wirtualne nie są obecnie szyfrowane. Sprawdź bieżący stan szyfrowania maszyny wirtualnej w następujący sposób:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="encrypt-virtual-disks"></a>Szyfruj dyski wirtualne
Teraz szyfrowania dysków wirtualnych, można przenosić ze sobą wszystkie poprzednie składniki:

1. Określ aplikację usługi Azure Active Directory i hasło.
2. Określ Key Vault w celu przechowywania metadanych dla zaszyfrowanych dysków.
3. Określ klucze kryptograficzne służące do rzeczywistego szyfrowania i odszyfrowywania.
4. Określ, czy chcesz zaszyfrować dysk systemu operacyjnego, dyski danych lub all.

Pozwala, przejrzyj szczegóły magazyn kluczy Azure i klawisz, który został utworzony, potrzebny jest identyfikator magazynu kluczy, identyfikator URI, a następnie klucza adresu URL w ostatnim kroku:

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Szyfrowanie dysków wirtualnych przy użyciu dane wyjściowe z `azure keyvault show` i `azure keyvault key show` polecenia w następujący sposób:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Poprzednie polecenie zawiera wiele zmiennych, poniższy przykład jest pełne polecenie odwołania:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Azure CLI nie zapewnia pełne błędy podczas procesu szyfrowania. Aby uzyskać dodatkowe informacje dotyczące rozwiązywania problemów, przejrzyj `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log` na Maszynie wirtualnej jest szyfrowany.

Na koniec pozwala sprawdzać stan szyfrowania ponownie, aby potwierdzić, że teraz zostały zaszyfrowane dyski wirtualne:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="add-additional-data-disks"></a>Dodania dodatkowego dysku z danymi
Po zaszyfrowanych dysków z danymi, możesz później dodać dodatkowych dysków wirtualnych do maszyny Wirtualnej i również ich szyfrowania. Po uruchomieniu `azure vm enable-disk-encryption` polecenia, zwiększyć przy użyciu wersji sekwencji `--sequence-version` parametru. Ten parametr wersji sekwencji umożliwia wykonywanie operacji powtarzane na tej samej maszyny Wirtualnej.

Na przykład pozwala dodać drugi wirtualny dysk do maszyny Wirtualnej w następujący sposób:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Ponownie uruchom polecenie do szyfrowania dysków wirtualnych, dodawanie tego czasu `--sequence-version` parametr i zwiększając wartość z naszym pierwszym uruchomieniu w następujący sposób:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
  --sequence-version 2
```


## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać więcej informacji na temat zarządzania usługą Azure Key Vault, łącznie z usunięciem kluczy kryptograficznych i magazyny, zobacz [Zarządzanie Key Vault przy użyciu interfejsu wiersza polecenia](../../key-vault/key-vault-manage-with-cli2.md).
* Aby uzyskać więcej informacji o szyfrowaniu dysków, takich jak przygotowywanie zaszyfrowanego niestandardowego maszyny Wirtualnej do przekazania do platformy Azure, zobacz [szyfrowania dysków Azure](../../security/azure-security-disk-encryption.md).
