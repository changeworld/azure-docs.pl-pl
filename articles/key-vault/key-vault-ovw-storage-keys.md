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
ms.openlocfilehash: 02fffe7c4a3acff6ce6d68046eee4286003b1766
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232226"
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
    - Wykonaj kroki opisane w tym [dokumentu](https://docs.microsoft.com/azure/storage/) można utworzyć konta magazynu  
    - **Wskazówki dotyczące nazewnictwa:** nazwy kont magazynu muszą należeć do zakresu od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.        
      
<a name="step-by-step-instructions"></a>Krok po kroku instrukcje
-------------------------
W poniższych instrukcji, przypisujemy usługi Key Vault, jako usługa musi mieć uprawnienia operatora w ramach konta magazynu

1. Po utworzeniu konta magazynu, uruchom następujące polecenie, aby uzyskać identyfikator zasobu konta magazynu, którymi chcesz zarządzać

    ```
    az storage account show -n storageaccountname (Copy ID out of the result of this command)
    ```
    
2. Uzyskiwanie usługi aplikacji identyfikator z usługi Azure Key Vault dla podmiotu zabezpieczeń 

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
3. Przypisz rolę operatora klucza magazynu do usługi Azure Key Vault Identity

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id hhjkh --scope idofthestorageaccount
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
