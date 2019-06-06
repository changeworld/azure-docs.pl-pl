---
title: Zarządzanie kluczami konta magazynu przy użyciu usługi Azure Key Vault i wiersza polecenia platformy Azure
description: Klucze konta magazynu zapewniają bezproblemową integrację usługi Azure Key Vault i na podstawie klucza dostępu do konta usługi Azure storage.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 91cc3f96f9cdd231c38232c972c2628d12b9f4b3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476155"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>Zarządzanie kluczami konta magazynu przy użyciu usługi Azure Key Vault i wiersza polecenia platformy Azure 

Usługa Azure Key Vault zarządza kluczy do konta magazynu platformy Azure i klasyczne konta magazynu. Funkcja konta magazynu zarządzanego Key Vault umożliwia wykonać kilka funkcji zarządzania kluczami.

[Konta usługi Azure storage](/azure/storage/storage-create-storage-account) używa poświadczeń, który składa się z nazwy konta i klucz. Klucz jest generowana automatycznie i służy jako hasło, a nie jako klucz kryptograficzny. Usługa Key Vault zarządza kluczy konta magazynu, przechowując je jako [wpisy tajne usługi Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). Klucze są wymienione (zsynchronizowanych) przy użyciu konta usługi Azure storage, a okresowe ponowne lub _obrócony_. 

Gdy używasz funkcji klucza konta magazynu zarządzanego, należy wziąć pod uwagę następujące kwestie:

- Wartości kluczy nigdy nie są zwracane w odpowiedzi na obiekt wywołujący.
- Tylko usługi Key Vault należy zarządzać kluczami konta magazynu. Nie samodzielnie zarządzać kluczami i uniknięcie zakłócania procesów usługi Key Vault.
- Tylko jeden obiekt magazynu kluczy należy zarządzać kluczy konta magazynu. Nie zezwalaj na zarządzanie kluczami z wielu obiektów.
- Możesz poprosić o Zarządzanie kontem magazynu za pomocą nazwy głównej użytkownika, ale nie za pomocą nazwy głównej usługi Key Vault.
- Wygeneruj ponownie klucze tylko przy użyciu usługi Key Vault. Ręcznie nie Generuj ponownie klucze konta magazynu. 

> [!NOTE]
> Integracja usługi Azure Storage z usługą Azure Active Directory (Azure AD) to usługa firmy Microsoft oparte na chmurze tożsamości i dostępu do zarządzania.
> Integracja z usługą Azure AD jest dostępna dla [obiekty BLOB platformy Azure i kolejek](https://docs.microsoft.com/azure/storage/common/storage-auth-aad).
> Używaj usługi Azure AD do uwierzytelniania i autoryzacji.
> Usługa Azure AD zapewnia OAuth2 opartego na tokenach dostępu do usługi Azure Storage, podobnie jak usługi Azure Key Vault.
>
> Usługa Azure AD umożliwia uwierzytelnianie aplikacji klienckiej przy użyciu tożsamości aplikacji lub użytkownika, a nie poświadczenia konta magazynu.
> Możesz użyć [tożsamości zarządzanej usługi Azure AD](/azure/active-directory/managed-identities-azure-resources/) po uruchomieniu na platformie Azure. Zarządzanych tożsamości eliminuje potrzebę uwierzytelniania klientów i przechowywanie poświadczeń w lub za pomocą aplikacji.
> Usługa Azure AD używa kontroli dostępu opartej na rolach (RBAC), aby zarządzać autoryzacji, która jest również obsługiwana przez usługi Key Vault.

### <a name="service-principal-application-id"></a>Identyfikator aplikacji nazwy głównej usługi

Dzierżawa usługi Azure AD zapewnia każdej aplikacji zarejestrowanej za pomocą [nazwy głównej usługi](/azure/active-directory/develop/developer-glossary#service-principal-object). Jednostki usługi służy jako tożsamości aplikacji (ID). Identyfikator aplikacji jest używany podczas instalacji autoryzacji do uzyskiwania dostępu do innych zasobów platformy Azure przy użyciu RBAC.

Usługa Key Vault jest aplikacji firmy Microsoft, który wstępnie jest zarejestrowany w dzierżawach wszystkie usługi Azure AD. Key Vault jest zarejestrowany w ramach tego samego Identyfikatora aplikacji i w każdej chmurze platformy Azure.

| Dzierżaw | Chmura | Identyfikator aplikacji |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Publicznej | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Inne  | Dowolne | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>Wymagania wstępne

Zanim użyjesz usługi Key Vault do zarządzania klucz konta magazynu, należy przejrzeć wymagania wstępne:

- Zainstaluj [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).
- Tworzenie [konta usługi Azure storage](https://azure.microsoft.com/services/storage/). Postępuj zgodnie z [te kroki](https://docs.microsoft.com/azure/storage/).
- Nazwa konta magazynu należy użyć tylko małe litery i cyfry. Długość nazwy musi być od 3 do 24 znaków.        
      
## <a name="manage-storage-account-keys"></a>Zarządzanie kluczami konta magazynu

Istnieją cztery podstawowe kroki, aby zarządzać kluczy konta magazynu przy użyciu usługi Key Vault:

1. Pobierz istniejące konto magazynu.
1. Pobranie istniejącego magazynu kluczy.
1. Dodaj konto magazynu Key Vault zarządzanych w magazynie. Ustaw `key1` jako aktywnego klucza okres ponownego generowania 180 dni.
1. Użyj `key1` można ustawić kontekst magazynu, dla określonego konta magazynu.

> [!NOTE]
> Usługa Key Vault jako usługa przypisano uprawnienia operatora na swoim koncie magazynu.
> 
> Po skonfigurowaniu kluczy konta magazynu usługi Azure Key Vault zarządzane tylko zmiany kluczy za pomocą usługi Key Vault.
> Dla kluczy zarządzanego konta magazynu usługi Key Vault zarządza obrót klucza konta magazynu.

1. Po utworzeniu konta magazynu, uruchom następujące polecenie, aby uzyskać identyfikator zasobu konta magazynu do zarządzania:
    ```
    az storage account show -n storageaccountname
    ```

    Skopiuj wartość Identyfikatora zasobu z danych wyjściowych polecenia:
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    Przykładowe dane wyjściowe:
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. Przypisz rolę RBAC "Magazynu klucz Rola usługi Operator kont" do usługi Key Vault. Ta rola ogranicza zakres dostępu do konta magazynu. Dla klasycznego konta magazynu Użyj roli "Klasycznego magazynu klucz Rola usługi Operator kont".

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope 93c27d83-f79b-4cb2-8dd4-4aa716542e74
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74` jest to identyfikator obiektu usługi Key Vault w chmurze publicznej Azure. Aby uzyskać identyfikator obiektu dla usługi Key Vault w chmurze Azure Government, zobacz [identyfikator aplikacji nazwy głównej usługi](#service-principal-application-id).
    
1. Utwórz konto magazynu Key Vault zarządzana:

    Ustaw okres ponowne generowanie 90 dni. Po upływie 90 dni, usługa Key Vault generuje `key1` i zamienia aktywnego klucza z `key2` do `key1`. `key1` następnie zostanie oznaczona jako aktywnego klucza. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>Tworzenie i generowania tokenów

Można również zadawać usługi Key Vault do generowania tokenów sygnatur dostępu współdzielonego. Sygnatury dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu. Można przyznać klientom dostęp do zasobów w ramach konta magazynu bez udostępniania kluczy konta. Sygnatury dostępu współdzielonego zapewnia bezpieczną metodę udostępniania zasobów magazynu bez narażania kluczy konta.

Polecenia w tej sekcji należy wykonać następujące czynności:

- Udostępnione konta dostępu do podpisu definicji zestawu `<YourSASDefinitionName>`. Definicja jest ustawiony na koncie magazynu Key Vault zarządzane `<YourStorageAccountName>` w magazynie kluczy `<VaultName>`.
- Utwórz token sygnatury dostępu współdzielonego konta usługi Blob, plików, tabel i kolejek. Token jest tworzony dla typów zasobów, usługę kontenera i obiektu. Token jest tworzony ze wszystkich uprawnień przy użyciu protokołu https i z określonymi datami rozpoczęcia i zakończenia.
- Zestaw magazynu Key Vault zarządzane udostępnione definicji podpis dostępu w magazynie. Definicja ma szablon URI token sygnatury dostępu współdzielonego, który został utworzony. Definicja ma typ sygnatury dostępu współdzielonego `account` i jest ważny przez N dni.
- Pobieranie tokenu dostępu rzeczywiste z klucza tajnego usługi Key Vault, który odnosi się do definicji sygnatury dostępu współdzielonego.

Po wykonaniu czynności opisane w poprzedniej sekcji, uruchom następujące polecenia, aby poprosić usługi Key Vault do generowania tokenów sygnatur dostępu współdzielonego. 

1. Utwórz definicję sygnatury dostępu współdzielonego. Po utworzeniu definicja sygnatury dostępu współdzielonego, poproś usługi Key Vault, aby wygenerować bardziej udostępnionego tokenów sygnatur dostępu. Ta operacja wymaga `storage` i `setsas` uprawnienia.
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    Aby uzyskać pomoc na temat operacji, zobacz [az storage Generowanie-sygnatury dostępu współdzielonego konta](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) dokumentację referencyjną.

    Po pomyślnym uruchomieniu operacji, skopiuj dane wyjściowe.
    ```console
       "se=2020-01-01&sp=***"
    ```

1. Użyj `$sasToken` generowane przez poprzednie polecenie i Utwórz definicję sygnatury dostępu współdzielonego. Aby uzyskać więcej informacji na temat parametrów poleceń, zobacz [az keyvault magazynu definicja sygnatury dostępu współdzielonego — tworzenie](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters) dokumentację referencyjną.
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    Gdy użytkownik nie ma uprawnień do konta magazynu, należy najpierw uzyskać identyfikator obiektu użytkownika:
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>Pobieranie tokenów w kodzie

Wykonywanie operacji na koncie magazynu przez pobieranie [udostępnione tokenów sygnatur dostępu](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) z usługi Key Vault.

Istnieją trzy sposoby uwierzytelniania w usłudze Key Vault:

- Używanie tożsamości usługi zarządzanej. Zdecydowanie zaleca się tego podejścia.
- Użyj nazwy głównej usługi i certyfikatu. 
- Użyj nazwy głównej usługi i hasła. Takie podejście nie jest zalecane.

Aby uzyskać więcej informacji, zobacz [usługi Azure Key Vault: Podstawowe pojęcia](key-vault-whatis.md#basic-concepts).

Poniższy przykład pokazuje, jak można pobrać tokenów sygnatur dostępu współdzielonego. Tokenów można pobrać po utworzeniu definicja sygnatury dostępu współdzielonego. 

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

Jeśli token sygnatury dostępu współdzielonego jest wygaśnie, ponownie pobrać token sygnatury dostępu współdzielonego z usługi Key Vault i aktualizowanie kodu.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Polecenia interfejsu wiersza polecenia platformy Azure

Aby uzyskać informacje o poleceniach wiersza polecenia platformy Azure, które są istotne dla kont magazynu zarządzanego, zobacz [az keyvault magazynu](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) dokumentację referencyjną.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [kluczy, wpisów tajnych i certyfikatów](https://docs.microsoft.com/rest/api/keyvault/).
- Zapoznaj się z artykułami na [blog zespołu usługi Azure Key Vault](https://blogs.technet.microsoft.com/kv/).
