---
ms.assetid: ''
title: Usługa Azure Key Vault zarządzanego konta magazynu — interfejs wiersza polecenia
description: Klucze konta magazynu zapewnienie seemless integrację usługi Azure Key Vault i klucza dostępu opartej na konto usługi Azure Storage.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: prashanthyv
ms.author: pryerram
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: e9b9620c3c631a9984bc6d1d02dc792c592b6e69
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2019
ms.locfileid: "55078393"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>Usługa Azure Key Vault zarządzanego konta magazynu — interfejs wiersza polecenia

> [!NOTE]
> [Integracja usługi Azure storage z usługą Azure Active Directory (Azure AD) jest teraz dostępna w wersji zapoznawczej](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Firma Microsoft zaleca używanie programu Azure AD do uwierzytelniania i autoryzacji, co zapewnia OAuth2 opartego na tokenach dostępu do usługi Azure storage, podobnie jak usługi Azure Key Vault. Dzięki temu można:
> - Uwierzytelniania aplikacji klienckiej przy użyciu tożsamości aplikacji lub użytkownika, a nie poświadczenia konta magazynu. 
> - Użyj [tożsamości zarządzanej usługi Azure AD](/azure/active-directory/managed-identities-azure-resources/) podczas uruchamiania na platformie Azure. Zarządzane, Usuń tożsamości potrzeby uwierzytelniania klienta, które razem i zapisywanie poświadczeń na lub z aplikacją.
> - Użyj kontroli dostępu na podstawie ról (RBAC) do zarządzania autoryzacji, co jest również obsługiwana przez usługi Key Vault.

- Usługa Azure Key Vault umożliwia zarządzanie kluczami z konta magazynu platformy Azure (ASA).
    - Wewnętrznie usługa Azure Key Vault można wyświetlić listę kluczy (synchronizacja) za pomocą konta usługi Azure Storage.    
    - Usługa Azure Key Vault generuje (rotuje) okresowo kluczy.
    - Wartości kluczy nigdy nie są zwracane w odpowiedzi na obiekt wywołujący.
    - Usługa Azure Key Vault zarządza klucze kont magazynu i klasycznego konta magazynu.
    
> [!IMPORTANT]
> Dzierżawa usługi Azure AD zapewnia każdej aplikacji zarejestrowanej za pomocą  **[nazwy głównej usługi](/azure/active-directory/develop/developer-glossary#service-principal-object)**, który służy jako tożsamości aplikacji. Identyfikator aplikacji nazwy głównej usługi jest używany w przypadku nadania jej zezwolenie na dostęp do innych zasobów platformy Azure za pośrednictwem kontroli dostępu opartej na rolach (RBAC). Ponieważ usługa Key Vault jest aplikacją firmy Microsoft, jest wstępnie zarejestrowane w wszystkich dzierżaw usługi Azure AD, w tym samym Identyfikatorem aplikacji w ramach każdej chmury platformy Azure:
> - Usługa Azure dzierżaw usługi AD w chmurze platformy Azure dla instytucji rządowych użyć Identyfikatora aplikacji `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - Azure dzierżaw usługi AD w chmurze publicznej Azure oraz wszystkich innych użyć Identyfikatora aplikacji `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

<a name="prerequisites"></a>Wymagania wstępne
--------------
1. [Interfejs wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) zainstalować interfejs wiersza polecenia Azure   
2. [Tworzenie konta magazynu](https://azure.microsoft.com/services/storage/)
    - Wykonaj kroki opisane w tym [dokumentu](https://docs.microsoft.com/azure/storage/) można utworzyć konta magazynu  
    - **Wskazówki dotyczące nazewnictwa:** Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery.        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>Krok po kroku instrukcje dotyczące sposobu używania usługi Key Vault do zarządzania kluczami konta magazynu
--------------------------------------------------------------------------------
W poniższych instrukcji, przypisujemy usługi Key Vault, jako usługa musi mieć uprawnienia operatora w ramach konta magazynu

> [!NOTE]
> . Należy pamiętać, że po skonfigurowaniu usługi Azure Key Vault zarządzanego konta magazynu kluczy one powinien **nie** już można zmienić z wyjątkiem za pośrednictwem usługi Key Vault. Zarządzanego magazynu kluczy konta oznacza, że usługa Key Vault będą zarządzać, wymiana klucza konta magazynu

1. Po utworzeniu konta magazynu, uruchom następujące polecenie, aby uzyskać identyfikator zasobu konta magazynu, którymi chcesz zarządzać

    ```
    az storage account show -n storageaccountname (Copy ID field out of the result of this command)
    ```
    
2. Uzyskiwanie usługi aplikacji identyfikator z usługi Azure Key Vault dla podmiotu zabezpieczeń 

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
3. Przypisz rolę operatora klucza magazynu do usługi Azure Key Vault Identity

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ApplicationIdOfKeyVault> --scope <IdOfStorageAccount>
    ```
    
4. Tworzenie magazynu kluczy zarządzanego konta magazynu.     <br /><br />
   Poniżej ustawiamy okres ponowne generowanie 90 dni. Po upływie 90 dni Key Vault ponownie wygenerować "klucz1" i Zamień aktywnego klucza, z "klucz2" do "klucz1".
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key2 --auto-regenerate-key --regeneration-period P90D --resource-id <Resource-id-of-storage-account>
    ```
    W przypadku, gdy użytkownik nie utworzono konta magazynu i nie ma uprawnień do konta magazynu, poniższe kroki, ustaw uprawnienia dla konta upewnić się, że wszystkie uprawnienia magazynu Key Vault można zarządzać.
 > [!NOTE] 
    W przypadku, że użytkownik nie ma uprawnień do konta magazynu możemy najpierw uzyskać identyfikator obiektu użytkownika

    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```
### <a name="relavant-azure-cli-cmdlets"></a>Polecenia cmdlet interfejsu wiersza polecenia Relavant Azure
- [Polecenia cmdlet usługi Storage wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

### <a name="relevant-powershell-cmdlets"></a>Odpowiednich poleceń cmdlet programu Powershell

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="see-also"></a>Zobacz także

- [Informacje o kluczach, wpisach tajnych i certyfikatach](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog zespołu usługi Key Vault](https://blogs.technet.microsoft.com/kv/)
