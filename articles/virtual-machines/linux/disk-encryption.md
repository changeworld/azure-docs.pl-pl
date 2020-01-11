---
title: Szyfrowanie po stronie serwera Managed Disks platformy Azure — interfejs wiersza polecenia platformy Azure
description: Usługa Azure Storage chroni dane, szyfrując je w stanie spoczynku przed utrwalaniem ich w klastrach magazynu. Możesz polegać na kluczach zarządzanych przez firmę Microsoft w celu szyfrowania dysków zarządzanych. Możesz też użyć kluczy zarządzanych przez klienta do zarządzania szyfrowaniem przy użyciu własnych kluczy.
author: roygara
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: f5a7e9f1248f9a73786fb9c4a6ecb32691400881
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894926"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Szyfrowanie po stronie serwera dla usługi Azure Managed disks

Usługa Azure Managed disks automatycznie szyfruje dane domyślnie, gdy są utrwalane w chmurze. Szyfrowanie po stronie serwera chroni dane i pomaga sprostać zobowiązaniom dotyczącym bezpieczeństwa i zgodności w organizacji. Dane w usłudze Azure Managed disks są szyfrowane w sposób niewidoczny dla użytkownika przy użyciu 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednego z najsilniejszych szyfrów blokowych i jest zgodny ze standardem FIPS 140-2.   

Szyfrowanie nie ma wpływu na wydajność dysków zarządzanych. Nie ma dodatkowych opłat za szyfrowanie.

Aby uzyskać więcej informacji na temat modułów kryptograficznych związanych z dyskami zarządzanymi platformy Azure, zobacz [interfejs API kryptografii: Kolejna generacja](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>Informacje o zarządzaniu kluczami szyfrowania

Możesz polegać na kluczach zarządzanych przez platformę do szyfrowania dysku zarządzanego lub można zarządzać szyfrowaniem przy użyciu własnych kluczy. Jeśli zdecydujesz się na zarządzanie szyfrowaniem przy użyciu własnych kluczy, możesz określić *klucz zarządzany przez klienta* , który będzie używany do szyfrowania i odszyfrowywania wszystkich danych na dyskach zarządzanych. 

W poniższych sekcjach opisano każdą z opcji zarządzania kluczami w bardziej szczegółowy sposób.

## <a name="platform-managed-keys"></a>Klucze zarządzane przez platformę

Domyślnie dyski zarządzane korzystają z kluczy szyfrowania zarządzanych przez platformę. Od 10 czerwca 2017 wszystkie nowe dyski zarządzane, migawki, obrazy i nowe dane zapisywane na istniejących dyskach zarządzanych są automatycznie szyfrowane przy użyciu kluczy zarządzanych przez platformę. 

## <a name="customer-managed-keys"></a>Klucze zarządzane przez klienta

Możesz zarządzać szyfrowaniem na poziomie każdego dysku zarządzanego przy użyciu własnych kluczy. Szyfrowanie po stronie serwera dla dysków zarządzanych z kluczami zarządzanymi przez klienta oferuje zintegrowane środowisko pracy z Azure Key Vault. Możesz zaimportować [klucze RSA](../../key-vault/key-vault-hsm-protected-keys.md) do Key Vault lub wygenerować nowe klucze rsa w Azure Key Vault. Usługa Azure Managed disks obsługuje szyfrowanie i odszyfrowywanie w pełni przejrzysty sposób przy użyciu funkcji [szyfrowania kopert](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Szyfruje ona dane przy użyciu klucza szyfrowania danych opartego na protokole [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256, który jest z kolei chroniony przy użyciu kluczy. Musisz udzielić dostępu do dysków zarządzanych w Key Vault, aby użyć kluczy do szyfrowania i odszyfrowywania danych. Pozwala to na pełną kontrolę nad danymi i kluczami. Możesz w dowolnym momencie wyłączyć klucze lub odwołać dostęp do dysków zarządzanych. Możesz również przeprowadzić inspekcję użycia klucza szyfrowania przy użyciu monitorowania Azure Key Vault, aby upewnić się, że tylko zarządzane dyski lub inne zaufane usługi platformy Azure uzyskują dostęp do kluczy.

Na poniższym diagramie pokazano, w jaki sposób dyski zarządzane używają Azure Active Directory i Azure Key Vault do wykonywania żądań przy użyciu klucza zarządzanego przez klienta:

![Przepływy pracy kluczy zarządzanych przez klienta](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


Poniższa lista zawiera bardziej szczegółowe informacje o diagramie:

1. Administrator Azure Key Vault tworzy zasoby magazynu kluczy.
1. Administrator magazynu kluczy importuje klucze RSA do Key Vault lub generować nowe klucze RSA w Key Vault.
1. Administrator tworzy wystąpienie zasobu zestawu szyfrowania dysku, określając identyfikator Azure Key Vault i adres URL klucza. Zestaw szyfrowanie dysków jest nowym zasobem wprowadzonym w celu uproszczenia zarządzania kluczami dla dysków zarządzanych. 
1. Po utworzeniu zestawu szyfrowania dysku [zarządzana tożsamość przypisana przez system](../../active-directory/managed-identities-azure-resources/overview.md) jest tworzona w Azure Active Directory (AD) i skojarzona z zestawem szyfrowania dysków. 
1. Następnie administrator magazynu kluczy Azure przyznaje uprawnienia zarządzanej tożsamości do wykonywania operacji w magazynie kluczy.
1. Użytkownik maszyny wirtualnej tworzy dyski przez skojarzenie ich z zestawem szyfrowania dysków. Użytkownik maszyny wirtualnej może również włączyć szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta dla istniejących zasobów przez skojarzenie ich z zestawem szyfrowanie dysków. 
1. Dyski zarządzane używają tożsamości zarządzanej do wysyłania żądań do Azure Key Vault.
1. W przypadku odczytywania lub zapisywania danych dyski zarządzane wysyłają żądania do Azure Key Vault, aby zaszyfrować (otoczyć) i odszyfrować (odwinięcie) klucz szyfrowania danych w celu szyfrowania i odszyfrowywania danych. 

Aby odwołać dostęp do kluczy zarządzanych przez klienta, zobacz [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) i [interfejs wiersza polecenia Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). Odwoływanie dostępu skutecznie blokuje dostęp do wszystkich danych na koncie magazynu, ponieważ klucz szyfrowania jest niedostępny przez usługę Azure Storage.

### <a name="supported-scenarios-and-restrictions"></a>Obsługiwane scenariusze i ograniczenia

Obecnie obsługiwane są tylko następujące scenariusze:

- Utwórz maszynę wirtualną z obrazu portalu Azure Marketplace i Zaszyfruj dysk systemu operacyjnego za pomocą szyfrowania po stronie serwera przy użyciu kluczy zarządzanych przez klienta.
- Utwórz obraz niestandardowy zaszyfrowany za pomocą szyfrowania po stronie serwera i kluczy zarządzanych przez klienta.
- Utwórz maszynę wirtualną na podstawie obrazu niestandardowego i Zaszyfruj dysk systemu operacyjnego przy użyciu funkcji szyfrowania po stronie serwera i kluczy zarządzanych przez klienta.
- Twórz dyski danych szyfrowane za pomocą szyfrowania po stronie serwera i kluczy zarządzanych przez klienta.
- (Tylko interfejs wiersza polecenia/PowerShell) Utwórz migawki, które są szyfrowane za pomocą szyfrowania po stronie serwera i kluczy zarządzanych przez klienta.
- Utwórz zestawy skalowania maszyn wirtualnych, które są szyfrowane za pomocą szyfrowania po stronie serwera i kluczy zarządzanych przez klienta.

Na razie mamy również następujące ograniczenia:

- **Dostępna tylko w regionach zachodnie stany USA, Południowo-środkowe stany USA, Wschodnie stany USA 2, Wschodnie stany USA, zachodnie stany USA 2, Europa Środkowa i Europa Północna.**
- Dyski utworzone na podstawie obrazów niestandardowych zaszyfrowanych przy użyciu szyfrowania po stronie serwera i kluczy zarządzanych przez klienta muszą być szyfrowane przy użyciu tych samych kluczy zarządzanych przez klienta i muszą znajdować się w tej samej subskrypcji.
- Migawki utworzone na podstawie dysków zaszyfrowanych przy użyciu szyfrowania po stronie serwera i kluczy zarządzanych przez klienta muszą być szyfrowane przy użyciu tych samych kluczy zarządzanych przez klienta.
- Obrazy niestandardowe szyfrowane za pomocą szyfrowania po stronie serwera i kluczy zarządzanych przez klienta nie mogą być używane w galerii obrazów udostępnionych.
- Wszystkie zasoby związane z kluczami zarządzanymi przez klienta (magazyny kluczy Azure, zestawy szyfrowania dysków, maszyny wirtualne, dyski i migawki) muszą znajdować się w tej samej subskrypcji i regionie.
- Dyski, migawki i obrazy zaszyfrowane przy użyciu kluczy zarządzanych przez klienta nie mogą zostać przeniesione do innej subskrypcji.
- W przypadku tworzenia zestawu szyfrowania dysków przy użyciu witryny Azure Portal nie można teraz używać migawek.

### <a name="cli"></a>Interfejs CLI
#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Konfigurowanie Azure Key Vault i DiskEncryptionSet

1. Upewnij się, że zainstalowano najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogowano się na koncie platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index).

1. Utwórz wystąpienie Azure Key Vault i klucza szyfrowania.

    Podczas tworzenia wystąpienia Key Vault należy włączyć opcję trwałe usuwanie i przeczyszczanie ochrony. Usuwanie nietrwałe gwarantuje, że Key Vault przechowuje usunięty klucz dla danego okresu przechowywania (wartość domyślna 90). Ochrona przed przeczyszczeniem gwarantuje, że usunięty klucz nie może zostać trwale usunięty, dopóki nie upłynie okres przechowywania. Te ustawienia chronią przed utratą danych z powodu przypadkowego usunięcia. Te ustawienia są obowiązkowe w przypadku używania Key Vault do szyfrowania dysków zarządzanych.

    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=WestCentralUS
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.  Utwórz wystąpienie elementu DiskEncryptionSet. 
    
    ```azurecli
    keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)

    keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)

    az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
    ```

1.  Przyznaj zasobowi DiskEncryptionSet dostęp do magazynu kluczy. 

    > [!NOTE]
    > Utworzenie tożsamości DiskEncryptionSet w Azure Active Directory przez platformę Azure może potrwać kilka minut. Jeśli zostanie wyświetlony błąd "nie można odnaleźć Active Directory obiektu" podczas uruchamiania poniższego polecenia, poczekaj kilka minut i spróbuj ponownie.

    ```azurecli
    desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)

    az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get

    az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
    ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Tworzenie maszyny wirtualnej przy użyciu obrazu z portalu Marketplace, szyfrowanie dysków systemu operacyjnego i danych za pomocą kluczy zarządzanych przez klienta

```azurecli
rgName=yourResourceGroupName
vmName=yourVMName
location=WestCentralUS
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName -n $vmName -l $location --image $image --size $vmSize --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 128 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId


```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Utwórz pusty dysk zaszyfrowany przy użyciu szyfrowania po stronie serwera z kluczami zarządzanymi przez klienta i dołącz go do maszyny wirtualnej

```azurecli
vmName=yourVMName
rgName=yourResourceGroupName
diskName=yourDiskName
diskSkuName=Premium_LRS
diskSizeinGiB=30
location=WestCentralUS
diskLUN=2
diskEncryptionSetName=yourDiskEncryptionSetName


diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az disk create -n $diskName -g $rgName -l $location --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId --size-gb $diskSizeinGiB --sku $diskSkuName

diskId=$(az disk show -n $diskName -g $rgName --query [id] -o tsv)

az vm disk attach --vm-name $vmName --lun $diskLUN --ids $diskId 

```

> [!IMPORTANT]
> Klucze zarządzane przez klienta korzystają z zarządzanych tożsamości dla zasobów platformy Azure, funkcji Azure Active Directory (Azure AD). W przypadku konfigurowania kluczy zarządzanych przez klienta tożsamość zarządzana jest automatycznie przypisywana do zasobów w ramach okładek. Jeśli później przeniesiesz subskrypcję, grupę zasobów lub dysk zarządzany z jednego katalogu usługi Azure AD do innego, zarządzana tożsamość skojarzona z dyskami zarządzanymi nie zostanie przetransferowana do nowej dzierżawy, więc klucze zarządzane przez klienta mogą przestać działać. Aby uzyskać więcej informacji, zobacz [transfer subskrypcji między katalogami usługi Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Klucze zarządzane przez klienta korzystają z zarządzanych tożsamości dla zasobów platformy Azure, funkcji Azure Active Directory (Azure AD). W przypadku konfigurowania kluczy zarządzanych przez klienta tożsamość zarządzana jest automatycznie przypisywana do zasobów w ramach okładek. Jeśli później przeniesiesz subskrypcję, grupę zasobów lub dysk zarządzany z jednego katalogu usługi Azure AD do innego, zarządzana tożsamość skojarzona z dyskami zarządzanymi nie zostanie przetransferowana do nowej dzierżawy, więc klucze zarządzane przez klienta mogą przestać działać. Aby uzyskać więcej informacji, zobacz [transfer subskrypcji między katalogami usługi Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Szyfrowanie po stronie serwera a usługa Azure Disk Encryption

[Azure Disk Encryption dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) wykorzystuje funkcję [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) systemu Windows i funkcję [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) w systemie Linux do szyfrowania dysków zarządzanych z kluczami ZARZĄDZANYMI przez klienta w ramach maszyny wirtualnej gościa.  Szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta usprawnia w systemie ADE, umożliwiając korzystanie z dowolnych typów systemów operacyjnych i obrazów dla maszyn wirtualnych przez szyfrowanie danych w usłudze Storage.

## <a name="next-steps"></a>Następne kroki

- [Poznaj szablony Azure Resource Manager tworzenia szyfrowanych dysków przy użyciu kluczy zarządzanych przez klienta](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Co to jest usługa Azure Key Vault?](../../key-vault/key-vault-overview.md)
