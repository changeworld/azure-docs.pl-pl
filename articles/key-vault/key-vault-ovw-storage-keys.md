---
ms.assetid: ''
title: Klucze konta magazynu usługi Azure Key Vault
description: Klucze konta magazynu zapewnienie seemless integrację usługi Azure Key Vault i klucza dostępu opartej na konto usługi Azure Storage.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: b5b30f7f5ffc7fcbef918162bc736c1f0a888d1b
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49067741"
---
# <a name="azure-key-vault-storage-account-keys"></a>Klucze konta magazynu usługi Azure Key Vault

> [!NOTE]
> [Usługa Azure storage obsługuje teraz autoryzacji AAD](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Zalecamy używanie usługi Azure Active Directory do uwierzytelniania i autoryzacji do magazynu, ponieważ użytkownicy nie mają już martwić się o rotacji kluczy konta magazynu, ich.

- Usługa Azure Key Vault umożliwia zarządzanie kluczami z konta magazynu platformy Azure (ASA).
    - Wewnętrznie usługa Azure Key Vault można wyświetlić listę kluczy (synchronizacja) za pomocą konta usługi Azure Storage.    
    - Usługa Azure Key Vault generuje (rotuje) okresowo kluczy.
    - Wartości kluczy nigdy nie są zwracane w odpowiedzi na obiekt wywołujący.
    - Usługa Azure Key Vault zarządza klucze kont magazynu i klasycznego konta magazynu.

<a name="prerequisites"></a>Wymagania wstępne
--------------
1. [Interfejs wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) zainstalować interfejs wiersza polecenia Azure   
2. [Tworzenie konta magazynu](https://azure.microsoft.com/services/storage/)
    - Wykonaj czynności opisane w tym [dokumentu](https://docs.microsoft.com/azure/storage/) można utworzyć konta magazynu  
    - **Wskazówki dotyczące nazewnictwa:** nazwy kont magazynu muszą należeć do zakresu od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.        
      
<a name="step-by-step-instructions"></a>Krok po kroku instrukcje
-------------------------

1. Pobierz identyfikator zasobu konta magazynu platformy Azure, którymi chcesz zarządzać.
    a. Gdy utworzymy konto magazynu 
    ```
    az storage account show -n storageaccountname (Copy ID out of the result of this command)
    ```
2. Pobierz identyfikator zasobu konta magazynu platformy Azure, którymi chcesz zarządzać.
    ```
    az storage account show -n storageaccountname (Take ID out of this)
    ```
3. Uzyskiwanie usługi aplikacji identyfikator z usługi Azure Key Vault dla podmiotu zabezpieczeń 
    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
4. Przypisz rolę operatora klucza magazynu do usługi Azure Key Vault Identity
    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id hhjkh --scope idofthestorageaccount
    ```
5. Tworzenie magazynu kluczy zarządzanego konta magazynu.     <br /><br />
   Poniższe polecenie pyta, czy usługi Key Vault w celu ponownego wygenerowania klucza co 90 dni.
   Poniższe polecenie pyta, czy usługi Key Vault można regenerować kluczy dostępu do usługi storage okresowo okres ponownego generowania. Poniżej ustawiamy okres ponowne generowanie 90 dni. Po upływie 90 dni Key Vault ponownie wygenerować "klucz1" i Zamień aktywnego klucza, z "klucz2" do "klucz1".
   ### <a name="key-regeneration"></a>Ponowne generowanie klucza
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key2 --auto-generate-key --regeneration-period P90D --resource-id <Resource-id-of-storage-account>
    ```
    W przypadku, gdy użytkownik nie utworzono konta magazynu i nie ma uprawnień do konta magazynu, poniższe kroki, ustaw uprawnienia dla konta upewnić się, że wszystkie uprawnienia magazynu Key Vault można zarządzać.
    [!NOTE] W przypadku, że użytkownik nie nie uprawnienia do konta magazynu, który możemy najpierw uzyskać identyfikator obiektu użytkownika

    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover purge restore set setsas update
    ```

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
