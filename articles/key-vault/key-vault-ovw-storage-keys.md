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
ms.date: 08/21/2017
ms.openlocfilehash: 7545a035541a4e464a6c82acb9fa9de18cf8e86d
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44304326"
---
# <a name="azure-key-vault-storage-account-keys"></a>Klucze konta magazynu usługi Azure Key Vault

Przed Azure Key Vault Storage Account Keys deweloperów musiał zarządzać własne klucze konta magazynu platformy Azure (ASA) i Obróć je ręcznie lub za pomocą narzędzie automatyzacji. Teraz, Key Vault Storage Account Keys są implementowane jako [wpisy tajne usługi Key Vault](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets) uwierzytelniania za pomocą konta usługi Azure Storage.

Kluczową funkcją konta magazynu platformy Azure (ASA) zarządza obrotu wpisu tajnego dla Ciebie. Usuwa również potrzebę bezpośredniego kontaktu z kluczem ASA, zapewniając dostęp do sygnatur Współdzielonego jako metodę.

Aby uzyskać bardziej ogólne informacje na temat kont usługi Azure Storage, zobacz [kontach magazynu Azure o](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

## <a name="supporting-interfaces"></a>Obsługa interfejsów

Pełną listę i linki do naszych interfejsów programowania i obsługi skryptów w znajdziesz [Key Vault Developer's Guide](key-vault-developers-guide.md#coding-with-key-vault).


## <a name="what-key-vault-manages"></a>Zarządza usługi Key Vault

Usługa Key Vault wykonuje kilka wewnętrznych funkcji zarządzania w Twoim imieniu, korzystając z zarządzanych kluczy konta magazynu.

- Usługa Azure Key Vault umożliwia zarządzanie kluczami z konta magazynu platformy Azure (ASA).
    - Wewnętrznie usługa Azure Key Vault można wyświetlić listę kluczy (synchronizacja) za pomocą konta usługi Azure Storage.
    - Usługa Azure Key Vault generuje (rotuje) okresowo kluczy.
    - Wartości kluczy nigdy nie są zwracane w odpowiedzi na obiekt wywołujący.
    - Usługa Azure Key Vault zarządza klucze kont magazynu i klasycznego konta magazynu.
- Usługa Azure Key Vault pozwala, właściciel magazynu/obiektu, aby utworzyć definicje sygnatur dostępu Współdzielonego (sygnatura dostępu współdzielonego, konta lub sygnatury dostępu Współdzielonego usługi).
    - Wartość sygnatury dostępu Współdzielonego utworzonych przy użyciu sygnatury dostępu Współdzielonego definicji, jest zwracana jako klucz tajny w za pośrednictwem ścieżka identyfikatora URI REST. Aby uzyskać więcej informacji, zobacz operacje definicja sygnatury dostępu Współdzielonego w [odwołania API REST usługi Azure Key Vault](/rest/api/keyvault).

## <a name="naming-guidance"></a>Wskazówki dotyczące nazewnictwa

- Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery.
- Nazwa definicji sygnatury dostępu Współdzielonego musi być 1 102 znaków zawierający tylko 0-9, a – z, A-Z.

## <a name="developer-experience"></a>Środowisko programistyczne

### <a name="before-azure-key-vault-storage-keys"></a>Przed kluczy magazynu usługi Azure Key Vault

Deweloperom umożliwia należy wykonać następujące rozwiązania przy użyciu klucza konta magazynu do uzyskania dostępu do usługi Azure storage.
1. Store parametrów połączenia lub tokenu sygnatury dostępu Współdzielonego w ustawieniach aplikacji usługi Azure App Service lub inny magazyn.
1. Podczas rozruchu aplikacji Pobierz parametry połączenia lub tokenu sygnatury dostępu Współdzielonego.
1. Tworzenie [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) do interakcji z magazynem.

```cs
// The Connection string is being fetched from App Service application settings
var connectionStringOrSasToken = CloudConfigurationManager.GetSetting("StorageConnectionString");
var storageAccount = CloudStorageAccount.Parse(connectionStringOrSasToken);
var blobClient = storageAccount.CreateCloudBlobClient();
 ```

### <a name="after-azure-key-vault-storage-keys"></a>Po klucze magazynu usługi Azure Key Vault

Deweloperom tworzenie [KeyVaultClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault.keyvaultclient) i wykorzystać, aby uzyskać token sygnatury dostępu Współdzielonego z ich magazynem. Następnie utwórz [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) z tym tokenem.

```cs
// Create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();

// Use the blobClientWithSas
...

// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>Wskazówki dla deweloperów

- Zezwalaj tylko na usłudze Key Vault, zarządzać kluczami ASA. Nie należy próbować zarządzać samodzielnie, będzie zakłócać procesów usługi Key Vault.
- Nie zezwalaj na ASA klucze mają być zarządzane przez więcej niż jeden obiekt usługi Key Vault.
- Jeśli musisz ręcznie ponownie wygenerować klucze ASA, firma Microsoft zaleca regenerować je za pośrednictwem usługi Key Vault.

## <a name="getting-started"></a>Wprowadzenie

### <a name="give-key-vault-access-to-your-storage-account"></a>Udzielić dostępu do magazynu kluczy do konta magazynu 

Podobnie jak wiele aplikacji usługi Key Vault jest zarejestrowany z usługą Azure AD w celu dostępu do innych usług za pomocą uwierzytelniania OAuth. Podczas rejestracji [nazwy głównej usługi](/azure/active-directory/develop/app-objects-and-service-principals) obiekt zostanie utworzony, która jest używana do reprezentowania tożsamości aplikacji w czasie wykonywania. Nazwa główna usługi umożliwia również zezwolić tożsamości aplikacji dostęp do innego zasobu za pośrednictwem kontroli dostępu opartej na rolach (RBAC).

Tożsamość aplikacji usługi Azure Key Vault wymaga uprawnień do *listy* i *ponownie wygenerować* klucze konta magazynu. Skonfiguruj te uprawnienia wykonując następujące czynności:

```powershell
# Get the resource ID of the Azure Storage Account you want to manage.
# Below, we are fetching a storage account using Azure Resource Manager
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get Application ID of Azure Key Vault's service principal
$servicePrincipal = Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093

# Assign Storage Key Operator role to Azure Key Vault Identity
New-AzureRmRoleAssignment -ObjectId $servicePrincipal.Id -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storage.Id
```

    >[!NOTE]
    > For a classic account type, set the role parameter to *"Classic Storage Account Key Operator Service Role."*

## <a name="working-example"></a>Działający przykład

W poniższym przykładzie pokazano tworzenie magazynu kluczy zarządzanego konta usługi Azure Storage i skojarzone definicje sygnatur dostępu Współdzielonego.

### <a name="prerequisite"></a>Wymagania wstępne

Upewnij się, że zostały wykonane [Konfigurowanie uprawnień kontroli dostępu opartej na rolach](#setup-for-role-based-access-control-rbac-permissions).

### <a name="setup"></a>Konfigurowanie

```powershell
# This is the name of our Key Vault
$keyVaultName = "mykeyVault"

# Fetching all the storage account object, of the ASA we want to manage with KeyVault
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure KeyVault Identity service principal
$servicePrincipalId = $(Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

Następnym etapem jest skonfigurowanie uprawnień dla **konta** aby upewnić się, że wszystkie uprawnienia magazynu Key Vault można zarządzać. W poniższym przykładzie nasze konto platformy Azure jest _developer@contoso.com_.

```powershell
# Searching our Azure Active Directory for our account's ObjectId
$userPrincipalId = $(Get-AzureRmADUser -SearchString "developer@contoso.com").Id

# We use the ObjectId we found to setting permissions on the vault
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $userPrincipalId -PermissionsToStorage all
```

### <a name="create-a-key-vault-managed-storage-account"></a>Tworzenie magazynu kluczy zarządzanego konta magazynu

Teraz Utwórz konto magazynu zarządzanego w usłudze Azure Key Vault i Użyj klucza dostępu z poziomu konta magazynu do utworzenia tokenów sygnatur dostępu Współdzielonego.
- `-ActiveKeyName` używa "klucz2" do generowania tokenów sygnatur dostępu Współdzielonego.
- `-AccountName` Służy do identyfikowania swojego konta magazynu zarządzanego. Poniżej użyto nazwy konta magazynu w celu uproszczenia, ale może być dowolną nazwę.
- `-DisableAutoRegenerateKey` Określa nie ponowne generowanie kluczy konta magazynu.

```powershell
# Adds your storage account to be managed by Key Vault and will use the access key, key2
Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storage.StorageAccountName -AccountResourceId $storage.Id -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="key-regeneration"></a>Ponowne generowanie klucza

Jeśli chcesz, aby okresowo ponownie wygenerować klucze dostępu do usługi magazynu Key Vault, możesz ustawić okres ponownego generowania. Poniżej ustawiamy okres ponownego generowania, wynosi 3 dni. Po 3 dniach usługi Key Vault będą ponownie wygenerować klucz "1", a następnie zamienić aktywnego klucza, z "klucz2" do "klucz1".

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
$accountName = $storage.StorageAccountName

Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $accountName -AccountResourceId $storage.Id -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>Zestaw definicje sygnatur dostępu Współdzielonego

Sygnatura dostępu Współdzielonego konta daje dostęp do usługi obiektów blob z różnymi uprawnieniami.
Ustaw definicje sygnatur dostępu Współdzielonego w usłudze Key Vault dla konta magazynu zarządzanego.
- `-AccountName` jest nazwą konta magazynu zarządzanego w usłudze Key Vault.
- `-Name` jest to identyfikator dla tokenu sygnatury dostępu Współdzielonego w magazynie.
- `-ValidityPeriod` Ustawia datę wygaśnięcia wygenerowanych tokenu sygnatury dostępu Współdzielonego.

```powershell
$validityPeriod = [System.Timespan]::FromDays(1)
$readSasName = "readBlobSas"
$writeSasName = "writeBlobSas"

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName $keyVaultName -AccountName $accountName -Name $readSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName $keyVaultName -AccountName $accountName -Name $writeSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List,Write
```

### <a name="get-sas-tokens"></a>Uzyskiwanie tokenów sygnatur dostępu Współdzielonego

Uzyskaj odpowiednie tokeny sygnatur dostępu Współdzielonego i skierujmy wywołania do magazynu. `-SecretName` jest tworzona przy użyciu danych wejściowych z `AccountName` i `Name` parametry, gdy zostanie wykonane [AzureKeyVaultManagedStorageSasDefinition zestaw](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition).

```powershell
$readSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$readSasName").SecretValueText
$writeSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$writeSasName").SecretValueText
```

### <a name="create-storage"></a>Utwórz magazyn

Należy zauważyć, że próba dostępu za pomocą *$readSasToken* zakończy się niepowodzeniem, ale, że można uzyskać dostęp za pomocą *$writeSasToken*.

```powershell
$context1 = New-AzureStorageContext -SasToken $readSasToken -StorageAccountName $storage.StorageAccountName
$context2 = New-AzureStorageContext -SasToken $writeSasToken -StorageAccountName $storage.StorageAccountName

# Ensure the txt file in command exists in local path mentioned
Set-AzureStorageBlobContent -Container containertest1 -File "./abc.txt" -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "./file.txt" -Context $context2
```

Jesteś, aby uzyskać dostęp do zawartości przy użyciu tokenu sygnatury dostępu Współdzielonego, który ma dostęp do zapisu obiektu blob magazynu.

### <a name="relevant-powershell-cmdlets"></a>Odpowiednich poleceń cmdlet programu Powershell

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="storage-account-onboarding"></a>Dołączanie konta magazynu

Przykład: jako właściciel obiektu usługi Key Vault, dodawany obiekt konta magazynu do usługi Azure Key Vault do dołączanie konta magazynu.

Zestawu dokumentacji podczas dołączania, musi sprawdzić, czy tożsamość konta dołączania ma uprawnienia do usługi Key Vault *listy* i *ponownie wygenerować* kluczy magazynu. Aby sprawdzić te uprawnienia, usługa Key Vault pobiera OBO (na klawiaturze imieniu z) token z usługi uwierzytelniania odbiorców, ustaw do usługi Azure Resource Manager i sprawia, że *listy* klucza wywołań do usługi Azure Storage. Jeśli *listy* połączenie nie powiedzie się, tworzenie obiektu kończy się niepowodzeniem z kodem stanu HTTP w usłudze Key Vault *zabronione*. Klucze wymienione w ten sposób są buforowane przy użyciu magazynu jednostki usługi key vault.

Usługa Key Vault musi sprawdzić, czy tożsamość *ponownie wygenerować* uprawnienia, zanim go może zająć własności ponowne generowanie kluczy. Aby sprawdzić, czy tożsamość, za pośrednictwem OBO token, a także tożsamości innych firm z pierwszej usługi Key Vault ma te uprawnienia:

- Usługa Key Vault Wyświetla listę uprawnień RBAC na zasób konta magazynu.
- Usługa Key Vault weryfikuje odpowiedzi za pomocą wyrażenia regularnego dopasowania akcji i innych działań.

Znajdź przykłady obsługi na [usługi Key Vault — zarządzane przykłady klucze konta magazynu](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=).

Jeśli tożsamość nie ma *ponownie wygenerować* uprawnienia lub jeśli nie ma usługi Key Vault pierwszy tożsamości innych firm *listy* lub *ponownie wygenerować* uprawnień, a następnie procesu dołączania żądanie kończy się niepowodzeniem, zwracając kod odpowiedni komunikat o błędzie i komunikatu.

OBO token będzie działać tylko w sytuacji, gdy używasz firmy Microsoft, natywne aplikacje klienckie programu PowerShell lub interfejsu wiersza polecenia.

## <a name="other-applications"></a>Inne aplikacje

- Tokeny sygnatur dostępu Współdzielonego, tworzony przy użyciu kluczy konta magazynu Key Vault, zapewniają bardziej kontrolowany dostęp do konta usługi Azure storage. Aby uzyskać więcej informacji, zobacz [używanie sygnatury dostępu współdzielonego](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1).

## <a name="see-also"></a>Zobacz także

- [Informacje o kluczach, wpisach tajnych i certyfikatach](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog zespołu usługi Key Vault](https://blogs.technet.microsoft.com/kv/)
