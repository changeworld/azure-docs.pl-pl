---
title: Zarządzanie kluczami konta magazynu przy użyciu Azure Key Vault i interfejsu wiersza polecenia platformy Azure
description: Klucze konta magazynu zapewniają bezproblemową integrację między Azure Key Vault i dostępem opartym na kluczach do konta usługi Azure Storage.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 251a7c21b671052a23f6ee18cb4278737464b25c
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744868"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>Zarządzanie kluczami konta magazynu przy użyciu Azure Key Vault i interfejsu wiersza polecenia platformy Azure 

Azure Key Vault zarządza kluczami dla kont usługi Azure Storage i klasycznych kont magazynu. Za pomocą funkcji zarządzanego konta magazynu Key Vault można wykonać kilka kluczowych funkcji zarządzania.

[Konto usługi Azure Storage](/azure/storage/storage-create-storage-account) używa poświadczeń, które składają się z nazwy konta i klucza. Klucz jest generowany automatycznie i służy jako hasło, a nie jako klucz kryptograficzny. Key Vault zarządza kluczami konta magazynu przez przechowywanie ich jako [Key Vault wpisów tajnych](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). Klucze są wyświetlane (zsynchronizowane) z kontem usługi Azure Storage i są okresowo ponownie generowane lub _obracane_. 

Korzystając z funkcji zarządzanego klucza konta magazynu, należy rozważyć następujące kwestie:

- Wartości klucza nigdy nie są zwracane w odpowiedzi na obiekt wywołujący.
- Tylko Key Vault powinny zarządzać kluczami konta magazynu. Nie Zarządzaj kluczami samodzielnie i unikaj zakłócania procesów Key Vault.
- Tylko jeden obiekt Key Vault powinien zarządzać kluczami konta magazynu. Nie Zezwalaj na zarządzanie kluczami z wielu obiektów.
- Możesz zażądać Key Vault, aby zarządzać kontem magazynu za pomocą podmiotu zabezpieczeń użytkownika, ale nie z jednostką usługi.
- Wygeneruj ponownie klucze, używając tylko Key Vault. Nie należy ręcznie generować ponownie kluczy konta magazynu. 

> [!NOTE]
> Integracja z usługą Azure Storage z usługą Azure Active Directory (Azure AD) to usługa zarządzania tożsamościami i dostępem opartymi na chmurze firmy Microsoft.
> Integracja z usługą Azure AD jest dostępna dla [obiektów blob i kolejek platformy Azure](../storage/common/storage-auth-aad.md).
> Uwierzytelnianie i autoryzacja przy użyciu usługi Azure AD.
> Usługa Azure AD zapewnia oparty na tokenach OAuth2 dostęp do usługi Azure Storage, podobnie jak Azure Key Vault.
>
> Usługa Azure AD umożliwia uwierzytelnianie aplikacji klienckiej przy użyciu tożsamości aplikacji lub użytkownika, a nie poświadczeń konta magazynu.
> Możesz użyć [tożsamości zarządzanej usługi Azure AD](/azure/active-directory/managed-identities-azure-resources/) podczas uruchamiania na platformie Azure. Tożsamości zarządzane usuwają potrzebę uwierzytelniania klienta i przechowywania poświadczeń w aplikacji lub w aplikacjach.
> Usługa Azure AD używa kontroli dostępu opartej na rolach (RBAC) do zarządzania autoryzacją, która również jest obsługiwana przez Key Vault.

### <a name="service-principal-application-id"></a>Identyfikator aplikacji głównej usługi

Dzierżawa usługi Azure AD udostępnia każdą zarejestrowaną aplikację z jednostką [usługi](/azure/active-directory/develop/developer-glossary#service-principal-object). Jednostka usługi służy jako tożsamość aplikacji (identyfikator). Identyfikator aplikacji jest używany podczas konfigurowania autoryzacji w celu uzyskania dostępu do innych zasobów platformy Azure za pośrednictwem RBAC.

Key Vault to aplikacja firmy Microsoft, która jest wstępnie zarejestrowana we wszystkich dzierżawach usługi Azure AD. Key Vault jest zarejestrowany w ramach tego samego identyfikatora aplikacji i w każdej chmurze platformy Azure.

| Dzierżaw | Chmura | Identyfikator aplikacji |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure — publiczna | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Inne  | Any | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem Key Vault do zarządzania kluczem konta magazynu, zapoznaj się z wymaganiami wstępnymi:

- Zainstaluj [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).
- Utwórz [konto usługi Azure Storage](https://azure.microsoft.com/services/storage/). Wykonaj [te kroki](../storage/index.yml).
- Nazwa konta magazynu musi zawierać tylko małe litery i cyfry. Długość nazwy musi wynosić od 3 do 24 znaków.        
      
## <a name="manage-storage-account-keys"></a>Zarządzanie kluczami konta magazynu

Istnieją cztery podstawowe kroki Key Vault służące do zarządzania kluczami kont magazynu:

1. Pobierz istniejące konto magazynu.
1. Pobierz istniejący magazyn kluczy.
1. Dodaj Key Vault konto magazynu zarządzanego do magazynu. Ustaw `key1` jako klucz aktywny z okresem regeneracji wynoszącym 90 dni.
1. Służy `key1` do ustawiania kontekstu magazynu dla określonego konta magazynu.

> [!NOTE]
> Key Vault jako usługa przypisuje uprawnienia operatora na Twoim koncie magazynu.
> 
> Po skonfigurowaniu Azure Key Vault zarządzanych kluczy kont magazynu należy zmienić tylko klucze przy użyciu Key Vault.
> W przypadku kluczy zarządzanego konta magazynu Key Vault służy do zarządzania obrotem klucza konta magazynu.

1. Po utworzeniu konta magazynu Uruchom następujące polecenie, aby uzyskać identyfikator zasobu konta magazynu do zarządzania:
    ```
    az storage account show -n storageaccountname
    ```

    Skopiuj wartość identyfikatora zasobu z danych wyjściowych polecenia:
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    Przykładowe dane wyjściowe:
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. Przypisz rolę "RBAC" roli usługi operatora kluczy konta magazynu do Key Vault. Ta rola ogranicza zakres dostępu do konta magazynu. W przypadku klasycznego konta magazynu należy użyć roli usługi operatora kluczy klasycznego konta magazynu.

    ```
    az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>"
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74`jest IDENTYFIKATORem obiektu dla Key Vault w chmurze publicznej platformy Azure. Aby uzyskać identyfikator obiektu dla Key Vault w chmurze Azure Government, zobacz [Identyfikator aplikacji nazwy głównej usługi](#service-principal-application-id).
    
1. Utwórz konto magazynu zarządzanego Key Vault:

    Ustaw okres regeneracji wynoszący 90 dni. Po 90 dniach Key Vault ponownie generuje `key1` i zamienia klucz aktywny z `key2` na `key1`. `key1`jest następnie oznaczany jako klucz aktywny. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>Tworzenie i generowanie tokenów

Możesz również poproszenie Key Vault o generowanie tokenów sygnatury dostępu współdzielonego. Sygnatury dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu. Możesz udzielić klientom dostępu do zasobów na koncie magazynu bez udostępniania kluczy konta. Sygnatura dostępu współdzielonego zapewnia bezpieczny sposób udostępniania zasobów magazynu bez naruszania kluczy konta.

Polecenia w tej sekcji pełnią następujące czynności:

- Ustaw definicję `<YourSASDefinitionName>`sygnatury dostępu współdzielonego konta. Definicja jest ustawiana na Key Vault zarządzanym koncie `<YourStorageAccountName>` magazynu w magazynie `<VaultName>`kluczy.
- Utwórz token sygnatury dostępu współdzielonego konta dla usług obiektów blob, plików, tabel i kolejek. Token jest tworzony dla usług, kontenerów i obiektów typu zasób. Token jest tworzony ze wszystkimi uprawnieniami, za pośrednictwem protokołu HTTPS i z określonymi datami rozpoczęcia i zakończenia.
- Ustaw Key Vault zarządzaną definicję sygnatury dostępu współdzielonego magazynu w magazynie. Definicja zawiera identyfikator URI szablonu utworzonego tokenu sygnatury dostępu współdzielonego. Definicja zawiera typ `account` sygnatury dostępu współdzielonego i jest ważna przez N dni.
- Pobierz rzeczywisty token dostępu z Key Vault tajnego, który odpowiada definicji sygnatury dostępu współdzielonego.

Po wykonaniu kroków opisanych w poprzedniej sekcji Uruchom następujące polecenia, aby poprosił Key Vault o generowanie tokenów sygnatury dostępu współdzielonego. 

1. Utwórz definicję sygnatury dostępu współdzielonego. Po utworzeniu definicji sygnatury dostępu współdzielonego podawaj Key Vault, aby generować więcej tokenów sygnatury dostępu współdzielonego. Ta operacja wymaga `storage` uprawnień i `setsas` .
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    Aby uzyskać pomoc dotyczącą operacji, zobacz sekcję [AZ Storage account Generate-SAS](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) Reference.

    Po pomyślnym uruchomieniu operacji skopiuj dane wyjściowe.
    ```console
       "se=2020-01-01&sp=***"
    ```

1. `$sasToken` Użyj wygenerowanego przez poprzednie polecenie i Utwórz definicję sygnatury dostępu współdzielonego. Aby uzyskać więcej informacji na temat parametrów poleceń, zobacz [AZ the Storage magazyn SAS-Definition Create](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters) Reference dokumentacja.
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    Jeśli użytkownik nie ma uprawnień do konta magazynu, należy najpierw uzyskać identyfikator obiektu użytkownika:
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>Pobieranie tokenów w kodzie

Wykonaj operacje na koncie magazynu, pobierając [tokeny sygnatury dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md) z Key Vault.

Istnieją trzy sposoby uwierzytelniania do Key Vault:

- Użyj tożsamości usługi zarządzanej. Takie podejście jest zdecydowanie zalecane.
- Użyj nazwy głównej usługi i certyfikatu. 
- Użyj nazwy głównej usługi i hasła. Takie podejście nie jest zalecane.

Aby uzyskać więcej informacji, [Zobacz Azure Key Vault: Podstawowe pojęcia](key-vault-whatis.md#basic-concepts).

Poniższy przykład ilustruje sposób pobierania tokenów sygnatury dostępu współdzielonego. Tokeny są pobierane po utworzeniu definicji sygnatury dostępu współdzielonego. 

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Jeśli token sygnatury dostępu współdzielonego niedługo wygaśnie, Pobierz token sygnatury dostępu współdzielonego ponownie z Key Vault i zaktualizuj kod.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Polecenia interfejsu wiersza polecenia platformy Azure

Aby uzyskać informacje na temat poleceń interfejsu wiersza polecenia platformy Azure, które są istotne dla zarządzanych kont magazynu, zobacz dokumentację dotyczącą [zapisu](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) w magazynie kluczy.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [kluczach, wpisach tajnych i certyfikatach](https://docs.microsoft.com/rest/api/keyvault/).
- Zapoznaj się z artykułami na [blogu zespołu Azure Key Vault](https://blogs.technet.microsoft.com/kv/).
