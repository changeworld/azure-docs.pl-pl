---
ms.assetid: ''
title: Usługa Azure Key Vault zarządzanego konta magazynu — interfejs wiersza polecenia
description: Klucze konta magazynu zapewniają bezproblemową integrację usługi Azure Key Vault i klucza dostępu opartej na konto magazynu platformy Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: prashanthyv
ms.author: prashanthyv
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 99b37a9b12c4b66e9b254156dfe4b59c7ab6594c
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526276"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>Usługa Azure Key Vault zarządzanego konta magazynu — interfejs wiersza polecenia

> [!NOTE]
> [Integracja usługi Azure storage z usługą Azure Active Directory (Azure AD) jest teraz dostępna w wersji zapoznawczej](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Firma Microsoft zaleca używanie programu Azure AD do uwierzytelniania i autoryzacji, co zapewnia OAuth2 opartego na tokenach dostępu do usługi Azure storage, podobnie jak usługi Azure Key Vault. Dzięki temu można:
> - Uwierzytelniania aplikacji klienckiej przy użyciu tożsamości aplikacji lub użytkownika, a nie poświadczenia konta magazynu. 
> - Użyj [tożsamości zarządzanej usługi Azure AD](/azure/active-directory/managed-identities-azure-resources/) podczas uruchamiania na platformie Azure. Zarządzane, Usuń tożsamości potrzeby uwierzytelniania klienta, które razem i zapisywanie poświadczeń na lub z aplikacją.
> - Użyj kontroli dostępu na podstawie ról (RBAC) do zarządzania autoryzacji, co jest również obsługiwana przez usługi Key Vault.

[Konta usługi Azure storage](/azure/storage/storage-create-storage-account) używa poświadczeń, który składa się z nazwy konta i klucz. Klucz jest generowana automatycznie i służy tylko jako "password" w przeciwieństwie do klucza kryptograficznego. Usługa Key Vault można zarządzać te klucze konta magazynu, przechowując je jako [wpisy tajne usługi Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

## <a name="overview"></a>Omówienie

Magazynu kluczy zarządzanego konta magazynu, że funkcja wykonuje kilka funkcji zarządzania w Twoim imieniu:

- Klucze listy (synchronizacje) za pomocą konta usługi Azure storage.
- Ponownie generuje (rotuje) okresowo kluczy.
- Zarządza klucze kont magazynu i klasycznego konta magazynu.
- Wartości kluczy nigdy nie są zwracane w odpowiedzi na obiekt wywołujący.

Kiedy używasz funkcji klucza konta magazynu zarządzanego:

- **Zezwalaj tylko na usłudze Key Vault do zarządzania kluczami konta magazynu.** Nie podejmuj próby zarządzać samodzielnie, jak będzie zakłócać procesów usługi Key Vault.
- **Nie zezwalaj na klucze konta magazynu, które będą zarządzane przez więcej niż jeden obiekt usługi Key Vault**.
- **Ręcznie nie Generuj ponownie klucze konta magazynu**. Firma Microsoft zaleca regenerować je za pośrednictwem usługi Key Vault.
- Pytaniem Zarządzanie kontem magazynu Key Vault może odbywać się przez podmiot zabezpieczeń użytkownika teraz, a nie nazwy głównej usługi

Poniższy przykład pokazuje, jak umożliwić usługi Key Vault do zarządzania kluczami konta magazynu.

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
Koncepcyjnie listę czynności, które są przestrzegane są
- Najpierw uzyskujemy (istniejące) konto magazynu
- Następnie możemy pobrać (istniejące) usługi key vault
- Następnie dodamy konto magazynu zarządzanego magazynu kluczy w magazynie ustawienie klucz1 jako aktywnego klucza i ponownego generowania okres 180 dni
- Na koniec możemy ustawić kontekst magazynu, dla określonego konta magazynu, za pomocą klucz1

W poniższych instrukcji, przypisujemy usługi Key Vault, jako usługa musi mieć uprawnienia operatora w ramach konta magazynu

> [!NOTE]
> . Należy pamiętać, że po skonfigurowaniu usługi Azure Key Vault zarządzanego konta magazynu kluczy one powinien **nie** już można zmienić z wyjątkiem za pośrednictwem usługi Key Vault. Zarządzanego magazynu kluczy konta oznacza, że usługa Key Vault będą zarządzać, wymiana klucza konta magazynu

> [!IMPORTANT]
> Dzierżawa usługi Azure AD zapewnia każdej aplikacji zarejestrowanej za pomocą  **[nazwy głównej usługi](/azure/active-directory/develop/developer-glossary#service-principal-object)**, który służy jako tożsamości aplikacji. Identyfikator aplikacji nazwy głównej usługi jest używany w przypadku nadania jej zezwolenie na dostęp do innych zasobów platformy Azure za pośrednictwem kontroli dostępu opartej na rolach (RBAC). Ponieważ usługa Key Vault jest aplikacją firmy Microsoft, jest wstępnie zarejestrowane w wszystkich dzierżaw usługi Azure AD, w tym samym Identyfikatorem aplikacji w ramach każdej chmury platformy Azure:
> - Usługa Azure dzierżaw usługi AD w chmurze platformy Azure dla instytucji rządowych użyć Identyfikatora aplikacji `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - Azure dzierżaw usługi AD w chmurze publicznej Azure oraz wszystkich innych użyć Identyfikatora aplikacji `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

> - Obecnie można użyć głównej nazwy użytkownika o usłudze Key Vault, aby zarządzać kontem magazynu, a nie nazwy głównej usługi


1. Po utworzeniu konta magazynu, uruchom następujące polecenie, aby uzyskać identyfikator zasobu konta magazynu, którymi chcesz zarządzać

    ```
    az storage account show -n storageaccountname 
    ```
    Skopiuj identyfikator pola z wynikiem powyższego polecenia, który wygląda jak poniżej
    ```
    /subscriptions/0xxxxxx-4310-48d9-b5ca-0xxxxxxxxxx/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```
            "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    
2. Przypisz rolę RBAC "Magazynu klucz Rola usługi Operator kont" do usługi Key Vault, ograniczając zakres dostępu do konta magazynu. Dla klasycznego konta magazynu Użyj "Klasyczne konto klucz Rola usługi Operator magazynu."
    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope 93c27d83-f79b-4cb2-8dd4-4aa716542e74
    ```
    
    "93c27d83-f79b-4cb2-8dd4-4aa716542e74" to identyfikator obiektu dla usługi Key Vault w chmurze publicznej. Aby uzyskać identyfikator obiektu dla usługi Key Vault w chmurach narodowych Zobacz powyższej sekcji ważne
    
3. Tworzenie magazynu kluczy zarządzanego konta magazynu.     <br /><br />
   Poniżej ustawiamy okres ponowne generowanie 90 dni. Po upływie 90 dni Key Vault ponownie wygenerować "klucz1" i Zamień aktywnego klucza, z "klucz2" do "klucz1". Go spowoduje oznaczenie klucz1 jako aktywnego klucza teraz. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<a name="step-by-step-instructions-on-how-to-use-key-vault-to-create-and-generate-sas-tokens"></a>Krok po kroku instrukcje dotyczące sposobu używania usługi Key Vault do tworzenia i generowanie tokenów sygnatur dostępu Współdzielonego
--------------------------------------------------------------------------------
Można również zadawać usługi Key Vault do generowania tokenów SAS (Shared Access Signature). Sygnatury dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu. Za pomocą sygnatury dostępu Współdzielonego można przyznać klientom dostęp do zasobów w ramach konta magazynu bez udostępniania kluczy konta. Jest to najbardziej istotna kwestia związana z używaniem sygnatur dostępu współdzielonego w aplikacjach — są one bezpiecznym sposobem udostępniania zasobów magazynu bez narażania kluczy konta.

Po wykonaniu kroków opisanych powyżej można uruchomić następujące polecenia, aby poprosić usługi Key Vault do generowania tokenów sygnatur dostępu Współdzielonego dla Ciebie. 

Lista rzeczy, które będą realizowane w następujące czynności są
- Ustawia konta definicja sygnatury dostępu Współdzielonego o nazwie `<YourSASDefinitionName>` na koncie magazynu zarządzanej usługi KeyVault `<YourStorageAccountName>` w magazynie `<VaultName>`. 
- Tworzy token sygnatury dostępu Współdzielonego konta usługi Blob, plik, tabela i kolejka, dla typów zasobów usługi, kontenerów i obiektów, ze wszystkich uprawnień przy użyciu protokołu https i z określonymi datami rozpoczęcia i zakończenia
- Ustawia magazynu zarządzanej usługi KeyVault definicja sygnatury dostępu Współdzielonego w magazynie o identyfikatorze uri szablonu jako token sygnatury dostępu Współdzielonego utworzony powyżej, sygnatury dostępu Współdzielonego typu "konto" i prawidłową dla N dni
- Pobiera token dostępu rzeczywiste z wpisu tajnego magazynu kluczy, odpowiadający definicja sygnatury dostępu Współdzielonego

1. W tym kroku utworzymy definicja sygnatury dostępu Współdzielonego. Po utworzeniu tej definicji sygnatury dostępu Współdzielonego, możesz poprosić usługi Key Vault, aby wygenerować więcej tokeny sygnatur dostępu Współdzielonego dla Ciebie. Ta operacja wymaga uprawnień do magazynu/setsas.

```
$sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
```
Można zobaczyć więcej informacji na temat powyższych operacji [tutaj](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas)

Gdy ta operacja zostanie wykonana pomyślnie, powinien pojawić się dane wyjściowe podobne do tego, jak pokazano poniżej. Kopiuj to

```console
   "se=2020-01-01&sp=***"
```

1. W tym kroku użyjemy dane wyjściowe ($sasToken) generowane powyżej, aby utworzyć definicję sygnatury dostępu Współdzielonego. Aby uzyskać więcej dokumentacji przeczytaj [tutaj](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters)   

```
az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
```
                        

 > [!NOTE] 
 > W przypadku, że użytkownik nie ma uprawnień do konta magazynu możemy najpierw uzyskać identyfikator obiektu użytkownika

 ```
 az ad user show --upn-or-object-id "developer@contoso.com"

 az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
 ```
    
## <a name="fetch-sas-tokens-in-code"></a>Pobieranie tokenów sygnatur dostępu Współdzielonego w kodzie

W tej sekcji omówimy, jak operacje na koncie magazynu przez pobieranie [tokeny sygnatur dostępu Współdzielonego](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) z usługi Key Vault

W poniżej sekcji pokażemy, jak można pobrać tokeny sygnatur dostępu Współdzielonego, po utworzeniu definicja sygnatury dostępu Współdzielonego, jak pokazano powyżej.

> [!NOTE]
>   Istnieją 3 sposoby uwierzytelnienia do usługi Key Vault, ponieważ możesz przeczytać w [podstawowe pojęcia](key-vault-whatis.md#basic-concepts)
> - Przy użyciu tożsamości usługi zarządzanej (zdecydowanie zalecane)
> - Za pomocą jednostki usługi i certyfikatu 
> - Przy użyciu nazwy głównej usługi i hasła (niezalecane)

```cs
// Once you have a security token from one of the above methods, then create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault. SecretUri is of the format https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Jeśli Twój token SAS jest wygaśnie, następnie będzie ponownie pobrać tokenu sygnatury dostępu Współdzielonego z usługi Key Vault i aktualizowanie kodu

```cs
// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

### <a name="relevant-azure-cli-commands"></a>Odpowiednie polecenia wiersza polecenia platformy Azure

[Polecenia usługi Azure Storage interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

## <a name="see-also"></a>Zobacz także

- [Informacje o kluczach, wpisach tajnych i certyfikatach](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog zespołu usługi Key Vault](https://blogs.technet.microsoft.com/kv/)
