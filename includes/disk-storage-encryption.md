---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cffb9efaf828b3793133143e97c0fc87f840df42
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72966296"
---
Usługa Azure Managed disks automatycznie szyfruje dane domyślnie, gdy są utrwalane w chmurze. Szyfrowanie po stronie serwera chroni dane i pomaga sprostać zobowiązaniom dotyczącym bezpieczeństwa i zgodności w organizacji. Dane w usłudze Azure Managed disks są szyfrowane w sposób niewidoczny dla użytkownika przy użyciu 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednego z najsilniejszych szyfrów blokowych i jest zgodny ze standardem FIPS 140-2.   

Szyfrowanie nie ma wpływu na wydajność dysków zarządzanych. Nie ma dodatkowych opłat za szyfrowanie.

Aby uzyskać więcej informacji na temat modułów kryptograficznych związanych z dyskami zarządzanymi platformy Azure, zobacz [interfejs API kryptografii: Kolejna generacja](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>Informacje o zarządzaniu kluczami szyfrowania

Możesz polegać na kluczach zarządzanych przez platformę do szyfrowania dysku zarządzanego lub można zarządzać szyfrowaniem przy użyciu własnych kluczy (publiczna wersja zapoznawcza). Jeśli zdecydujesz się na zarządzanie szyfrowaniem przy użyciu własnych kluczy, możesz określić *klucz zarządzany przez klienta* , który będzie używany do szyfrowania i odszyfrowywania wszystkich danych na dyskach zarządzanych. 

W poniższych sekcjach opisano każdą z opcji zarządzania kluczami w bardziej szczegółowy sposób.

## <a name="platform-managed-keys"></a>Klucze zarządzane przez platformę

Domyślnie dyski zarządzane korzystają z kluczy szyfrowania zarządzanych przez platformę. Od 10 czerwca 2017 wszystkie nowe dyski zarządzane, migawki, obrazy i nowe dane zapisywane na istniejących dyskach zarządzanych są automatycznie szyfrowane przy użyciu kluczy zarządzanych przez platformę. 

## <a name="customer-managed-keys-public-preview"></a>Klucze zarządzane przez klienta (publiczna wersja zapoznawcza)

Możesz zarządzać szyfrowaniem na poziomie każdego dysku zarządzanego przy użyciu własnych kluczy. Szyfrowanie po stronie serwera dla dysków zarządzanych z kluczami zarządzanymi przez klienta oferuje zintegrowane środowisko pracy z Azure Key Vault. Możesz zaimportować [klucze RSA](../articles/key-vault/key-vault-hsm-protected-keys.md) do Key Vault lub wygenerować nowe klucze rsa w Azure Key Vault. Usługa Azure Managed disks obsługuje szyfrowanie i odszyfrowywanie w pełni przejrzysty sposób przy użyciu funkcji [szyfrowania kopert](../articles/storage/common/storage-client-side-encryption.md#encryption-via-the-envelope-technique). Szyfruje ona dane przy użyciu klucza szyfrowania danych opartego na protokole [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256, który jest z kolei chroniony przy użyciu kluczy. Musisz udzielić dostępu w Key Vault, aby używać kluczy do szyfrowania i odszyfrowywania danych. Pozwala to na pełną kontrolę nad danymi i kluczami. Możesz w dowolnym momencie wyłączyć klucze lub odwołać dostęp do dysków zarządzanych. Możesz również przeprowadzić inspekcję użycia klucza szyfrowania przy użyciu monitorowania Azure Key Vault, aby upewnić się, że tylko zarządzane dyski lub inne zaufane usługi platformy Azure uzyskują dostęp do kluczy.

Na poniższym diagramie przedstawiono sposób, w jaki dyski zarządzane używają Azure Active Directory i Azure Key Vault do wykonywania żądań przy użyciu klucza zarządzanego przez klienta:

![Przepływy pracy kluczy zarządzanych przez klienta](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


Poniższa lista zawiera opis kroków w diagramie:

1. Administrator Azure Key Vault tworzy zasoby magazynu kluczy.
1. Administrator magazynu kluczy importuje klucze RSA do Key Vault lub generować nowe klucze RSA w Key Vault.
1. Administrator tworzy wystąpienie zasobu zestawu szyfrowania dysku, określając identyfikator Azure Key Vault i adres URL klucza. Zestaw szyfrowanie dysków jest nowym zasobem wprowadzonym w celu uproszczenia zarządzania kluczami dla dysków zarządzanych. 
1. Podczas tworzenia zestawu szyfrowania dysku tworzona jest [tożsamość zarządzana przypisana przez system](../articles/active-directory/managed-identities-azure-resources/overview.md) w usłudze Azure Active Directory (AD) i skojarzona z zestawem szyfrowania dysków. 
1. Następnie administrator magazynu kluczy Azure przyznaje uprawnienia zarządzanej tożsamości do wykonywania operacji w magazynie kluczy.
1. Użytkownik maszyny wirtualnej tworzy dyski przez skojarzenie ich z zestawem szyfrowania dysków. Użytkownik maszyny wirtualnej może również włączyć szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta dla istniejących zasobów przez skojarzenie ich z zestawem szyfrowanie dysków. 
1. Dyski zarządzane używają tożsamości zarządzanej do wysyłania żądań do Azure Key Vault.
1. W przypadku odczytywania lub zapisywania danych dyski zarządzane wysyłają żądania do Azure Key Vault, aby zaszyfrować (otoczyć) i odszyfrować (odwinięcie) klucz szyfrowania danych w celu szyfrowania i odszyfrowywania danych. 

Aby odwołać dostęp do kluczy zarządzanych przez klienta, zobacz [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) i [interfejs wiersza polecenia Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). Odwoływanie dostępu skutecznie blokuje dostęp do wszystkich danych na koncie magazynu, ponieważ klucz szyfrowania jest niedostępny przez usługę Azure Storage.

### <a name="supported-scenarios-and-restrictions"></a>Obsługiwane scenariusze i ograniczenia

W wersji zapoznawczej są obsługiwane tylko następujące scenariusze:

- Utwórz maszynę wirtualną z obrazu portalu Azure Marketplace i Zaszyfruj dysk systemu operacyjnego za pomocą szyfrowania po stronie serwera przy użyciu kluczy zarządzanych przez klienta.
- Utwórz obraz niestandardowy zaszyfrowany za pomocą szyfrowania po stronie serwera i kluczy zarządzanych przez klienta.
- Utwórz maszynę wirtualną na podstawie obrazu niestandardowego i Zaszyfruj dysk systemu operacyjnego przy użyciu funkcji szyfrowania po stronie serwera i kluczy zarządzanych przez klienta.
- Twórz dyski danych szyfrowane za pomocą szyfrowania po stronie serwera i kluczy zarządzanych przez klienta.
- Utwórz migawki, które są szyfrowane za pomocą szyfrowania po stronie serwera i kluczy zarządzanych przez klienta.
- Utwórz zestawy skalowania maszyn wirtualnych, które są szyfrowane za pomocą szyfrowania po stronie serwera i kluczy zarządzanych przez klienta.

Wersja zapoznawcza ma również następujące ograniczenia:

- Dostępne tylko w regionie zachodnie stany USA.
- Dyski utworzone na podstawie obrazów niestandardowych zaszyfrowanych przy użyciu szyfrowania po stronie serwera i kluczy zarządzanych przez klienta muszą być szyfrowane przy użyciu tych samych kluczy zarządzanych przez klienta i muszą znajdować się w tej samej subskrypcji.
- Migawki utworzone na podstawie dysków zaszyfrowanych przy użyciu szyfrowania po stronie serwera i kluczy zarządzanych przez klienta muszą być szyfrowane przy użyciu tych samych kluczy zarządzanych przez klienta.
- Obrazy niestandardowe szyfrowane za pomocą szyfrowania po stronie serwera i kluczy zarządzanych przez klienta nie mogą być używane w galerii obrazów udostępnionych.
- Key Vault musi znajdować się w tej samej subskrypcji i regionie co klucze zarządzane przez klienta.
- Dyski, migawki i obrazy zaszyfrowane przy użyciu kluczy zarządzanych przez klienta nie mogą zostać przeniesione do innej subskrypcji.

### <a name="setting-up-your-azure-key-vault"></a>Konfigurowanie Azure Key Vault

1.  Utwórz wystąpienie Azure Key Vault i klucza szyfrowania.

    Podczas tworzenia wystąpienia Key Vault należy włączyć opcję trwałe usuwanie i przeczyszczanie ochrony. Usuwanie nietrwałe gwarantuje, że Key Vault przechowuje usunięty klucz dla danego okresu przechowywania (wartość domyślna 90). Ochrona przed przeczyszczeniem gwarantuje, że usunięty klucz nie może zostać trwale usunięty, dopóki nie upłynie okres przechowywania. Te ustawienia chronią przed utratą danych z powodu przypadkowego usunięcia. Te ustawienia są obowiązkowe w przypadku używania Key Vault do szyfrowania dysków zarządzanych.

    ```powershell
    $keyVault = New-AzKeyVault -Name myKeyVaultName ` 
    -ResourceGroupName myRGName ` 
    -Location westcentralus ` 
    -EnableSoftDelete ` 
    -EnablePurgeProtection 
     
    $key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName ` 
    -Name myKeyName ` 
    -Destination Software `  
    ```

1.  Utwórz wystąpienie elementu DiskEncryptionSet. 
    
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName myRGName ` 
      -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateDiskEncryptionSet.json" ` 
      -diskEncryptionSetName "myDiskEncryptionSet1" ` 
      -keyVaultId "/subscriptions/mySubscriptionId/resourceGroups/myRGName/providers/Microsoft.KeyVault/vaults/myKeyVaultName" ` 
      -keyVaultKeyUrl "https://myKeyVaultName.vault.azure.net/keys/myKeyName/403445136dee4a57af7068cab08f7d42" ` 
      -region "WestCentralUS"
    ```

1.  Przyznaj zasobowi DiskEncryptionSet dostęp do magazynu kluczy.

    ```powershell
    $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
     
    Set-AzKeyVaultAccessPolicy ` 
        -VaultName $keyVault.VaultName ` 
        -ObjectId $identity.Id ` 
        -PermissionsToKeys wrapkey,unwrapkey,get 
     
    New-AzRoleAssignment ` 
        -ObjectId $identity.Id ` 
        -RoleDefinitionName "Reader" ` 
        -ResourceName $keyVault.VaultName ` 
        -ResourceType "Microsoft.KeyVault/vaults" ` 
        -ResourceGroupName myRGName `  
    ```

### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys-via-a-resource-manager-template"></a>Tworzenie maszyny wirtualnej przy użyciu obrazu z portalu Marketplace szyfrowanie dysków systemu operacyjnego i danych z kluczami zarządzanymi przez klienta za pomocą szablonu Menedżer zasobów

```
$password=ConvertTo-SecureString -String "myVMPassword" `
  -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName CMKTesting `
  -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateVMWithDisksEncryptedWithCMK.json" `
  -virtualMachineName "myVMName" `
  -adminPassword $password `
  -vmSize "Standard_DS3_V2" `
  -diskEncryptionSetId "/subscriptions/mySubscriptionId/resourceGroups/myRGName/providers/Microsoft.Compute/diskEncryptionSets/myDiskEncryptionSet1" `
  -region "westcentralus" 
```

### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Utwórz pusty dysk zaszyfrowany przy użyciu szyfrowania po stronie serwera z kluczami zarządzanymi przez klienta i dołącz go do maszyny wirtualnej

```PowerShell
$vmName = "yourVMName"
$rgName = "yourRGName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = "30"
$diskEncryptionSetId = "/subscriptions/<subscriptionID>/resourceGroups/yourRGName/providers/Microsoft.Compute/diskEncryptionSets/<yourDiskEncryptionSetName>"
$region = "westcentralus"
$diskLUN = 1

New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateEmptyDataDiskEncryptedWithSSECMK.json" `
  -diskName $diskName `
  -diskSkuName $diskSKU `
  -dataDiskSizeInGb $diskSizeinGiB `
  -diskEncryptionSetId $diskEncryptionSetId `
  -region $region 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun 1
```


> [!IMPORTANT]
> Klucze zarządzane przez klienta korzystają z zarządzanych tożsamości dla zasobów platformy Azure, funkcji Azure Active Directory (Azure AD). W przypadku konfigurowania kluczy zarządzanych przez klienta tożsamość zarządzana jest automatycznie przypisywana do zasobów w ramach okładek. Jeśli później przeniesiesz subskrypcję, grupę zasobów lub dysk zarządzany z jednego katalogu usługi Azure AD do innego, zarządzana tożsamość skojarzona z dyskami zarządzanymi nie zostanie przetransferowana do nowej dzierżawy, więc klucze zarządzane przez klienta mogą przestać działać. Aby uzyskać więcej informacji, zobacz [transfer subskrypcji między katalogami usługi Azure AD](../articles/active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Szyfrowanie po stronie serwera a usługa Azure Disk Encryption

[Azure Disk Encryption](../articles/security/fundamentals/azure-disk-encryption-vms-vmss.md) wykorzystuje funkcję [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) systemu Windows i funkcję [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) w systemie Linux do szyfrowania dysków zarządzanych z kluczami ZARZĄDZANYMI przez klienta w ramach maszyny wirtualnej gościa.  Szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta usprawnia w systemie ADE, umożliwiając korzystanie z dowolnych typów systemów operacyjnych i obrazów dla maszyn wirtualnych przez szyfrowanie danych w usłudze Storage.

## <a name="next-steps"></a>Następne kroki

- [Co to jest Azure Key Vault?](../articles/key-vault/key-vault-overview.md)
