---
ms.assetid: ''
title: Klucze konta magazynu usługi Azure Key Vault
description: Klucze konta magazynu przedstawić seemless integrację usługi Azure Key Vault i klucza dostępu na podstawie konta magazynu Azure.
ms.topic: article
services: key-vault
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 10/12/2017
ms.openlocfilehash: 4f42a47a6d934bf0538efccbcf7f057fd28e2c03
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-key-vault-storage-account-keys"></a>Klucze konta magazynu usługi Azure Key Vault

Przed magazynu kluczy konta Azure klucz magazynu deweloperzy mają zarządzać własne klucze konta magazynu Azure (ASA) i Obróć je ręcznie lub za pomocą narzędzie automatyzacji. Teraz, klucze konta magazynu kluczy w magazynie są zaimplementowane jako [kluczy tajnych Key Vault](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets) do uwierzytelniania przy użyciu konta magazynu Azure.

Funkcja klucza konta magazynu Azure (ASA) zarządza tajny obrotu. Usuwa również potrzebę bezpośredniego kontaktu z kluczem ASA oferując dostępu sygnatur dostępu Współdzielonego jako metody.

Aby uzyskać więcej ogólnych informacji o kontach magazynu Azure, zobacz [kont magazynu Azure o](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

## <a name="supporting-interfaces"></a>Obsługa interfejsów

Pełna lista i łącza do naszych interfejsów programowania i obsługi skryptów w znajdziesz [klucza magazynu przewodnik dewelopera](key-vault-developers-guide.md#coding-with-key-vault).


## <a name="what-key-vault-manages"></a>Zarządza Magazyn kluczy

Magazyn kluczy wykonuje wiele funkcji zarządzania wewnętrznego w Twoim imieniu używania zarządzanych kluczy konta magazynu.

- Usługa Azure Key Vault zarządza kluczy dla konta magazynu Azure (ASA).
    - Wewnętrznie usługi Azure Key Vault można wyświetlić listę kluczy (synchronizacja) z kontem magazynu platformy Azure.
    - Usługa Azure Key Vault generuje (obraca) okresowo kluczy.
    - Wartości klucza nigdy nie są zwracane w odpowiedzi do wywołującego.
    - Usługa Azure Key Vault zarządza klucze konta magazynu i klasycznych kont magazynu.
- Usługa Azure Key Vault pozwala, właściciel magazynu/obiektu, do tworzenia definicji SAS (konta lub sygnatury dostępu Współdzielonego usługi).
    - Wartość SAS, utworzone za pomocą definicji SAS, jest zwracana jako klucza tajnego za pośrednictwem ścieżka identyfikatora URI REST. Aby uzyskać więcej informacji, zobacz [operacje konta magazynu usługi Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations).

## <a name="naming-guidance"></a>Wskazówki dotyczące nazewnictwa

- Nazwy kont usługi Magazyn muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery.
- Nazwa definicji SAS musi być 1 102 znaków długości i zawierać tylko 0-9, a-z, A-Z.

## <a name="developer-experience"></a>Środowisko dewelopera

### <a name="before-azure-key-vault-storage-keys"></a>Przed usługi Azure Key Vault magazynu kluczy

Deweloperzy umożliwia należy wykonać następujące rozwiązania kluczem konta magazynu można uzyskać dostępu do magazynu Azure.
1. Parametry połączenia lub tokenu sygnatury dostępu Współdzielonego są przechowywane w ustawienia aplikacji usługi aplikacji Azure lub innej pamięci masowej.
1. Podczas rozruchu aplikacji należy pobrać parametry połączenia lub tokenu sygnatury dostępu Współdzielonego.
1. Utwórz [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) wchodzić w interakcje z magazynu.

```cs
// The Connection string is being fetched from App Service application settings
var connectionStringOrSasToken = CloudConfigurationManager.GetSetting("StorageConnectionString");
var storageAccount = CloudStorageAccount.Parse(connectionStringOrSasToken);
var blobClient = storageAccount.CreateCloudBlobClient();
 ```

### <a name="after-azure-key-vault-storage-keys"></a>Po usługi Azure Key Vault magazynu kluczy

Deweloperzy tworzyć [KeyVaultClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault.keyvaultclient) i wykorzystać, który można pobrać tokenu sygnatury dostępu Współdzielonego dla ich magazynu. Następnie utwórz [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) z tym tokenem.

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

- Zezwalaj tylko na Key Vault w celu zarządzanie kluczami ASA. Nie należy próbować Zarządzanie samodzielnie, będzie zakłócać procesów magazynu kluczy.
- Nie zezwalaj ASA klucze mają być zarządzane przez więcej niż jeden obiekt magazynu kluczy.
- Jeśli musisz ręcznie wygenerować ponownie klucze ASA, zaleca się Generuj je za pomocą usługi Key Vault.

## <a name="getting-started"></a>Wprowadzenie

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>Ustawienia dla uprawnień kontroli dostępu opartej na rolach

Tożsamość aplikacji usługi Azure Key Vault wymaga uprawnień do *listy* i *ponownie wygenerować* kluczy dla konta magazynu. Skonfiguruj te uprawnienia, wykonując następujące czynności:

```powershell
# Get the resource ID of the Azure Storage Account you want to manage.
# Below, we are fetching a storage account using Azure Resource Manager
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure Key Vault Identity
$servicePrincipal = Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093

# Assign Storage Key Operator role to Azure Key Vault Identity
New-AzureRmRoleAssignment -ObjectId $servicePrincipal.Id -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storage.Id
```

    >[!NOTE]
    > For a classic account type, set the role parameter to *"Classic Storage Account Key Operator Service Role."*

## <a name="working-example"></a>Przykład pracy

W poniższym przykładzie pokazano tworzenie Key Vault zarządzanych konto magazynu Azure i powiązane definicje dostępu sygnatury dostępu Współdzielonego.

### <a name="prerequisite"></a>Wymagania wstępne

Upewnij się, zostały ukończone [Instalator uprawnień kontroli dostępu opartej na rolach](#setup-for-role-based-access-control-rbac-permissions).

### <a name="setup"></a>Konfiguracja

```powershell
# This is the name of our Key Vault
$keyVaultName = "mykeyVault"

# Fetching all the storage account object, of the ASA we want to manage with KeyVault
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure KeyVault Identity service principal
$servicePrincipalId = $(Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

Następnie należy ustawić uprawnienia dla **konta** aby upewnić się, którymi można zarządzać wszystkie uprawnienia magazynu magazynu kluczy. W poniższym przykładzie jest nasze konto Azure _developer@contoso.com_.

```powershell
# Searching our Azure Active Directory for our account's ObjectId
$userPrincipalId = $(Get-AzureRmADUser -SearchString "developer@contoso.com").Id

# We use the ObjectId we found to setting permissions on the vault
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $userPrincipalId -PermissionsToStorage all
```

### <a name="create-a-key-vault-managed-storage-account"></a>Tworzenie magazynu kluczy konta magazynu zarządzanego

Teraz Utwórz konto magazynu zarządzane w usłudze Azure Key Vault i użyj klawisza dostępu z konta magazynu do utworzenia tokenów sygnatury dostępu Współdzielonego.
- `-ActiveKeyName` używa "key2", aby generować tokeny sygnatury dostępu Współdzielonego.
- `-AccountName` Służy do identyfikowania konta magazynu zarządzanego. Poniżej nazwa konta magazynu jest używany do Zachowaj prostotę, ale może być dowolną nazwą.
- `-DisableAutoRegenerateKey` Określa nie wygenerować ponownie klucze konta magazynu.

```powershell
# Adds your storage account to be managed by Key Vault and will use the access key, key2
Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storage.StorageAccountName -AccountResourceId $storage.Id -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="key-regeneration"></a>Ponowne generowanie klucza

Magazyn kluczy, można ponownie wygenerować klucze dostępu do Twojego magazynu okresowo, można ustawić okres ponownego wygenerowania. Poniżej konfigurujemy ustawienia okres ponownego wygenerowania 3 dni. Po 3 dni Key Vault ponownie wygenerować "key1" i wymiany klucza aktywnego z "key2" do "key1".

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
$accountName = $storage.StorageAccountName

Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $accountName -AccountResourceId $storage.Id -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>Definicje SAS zestawu

Konto sygnatury dostępu Współdzielonego zapewnia dostęp do usługi blob z innymi uprawnieniami.
Ustaw definicje sygnatury dostępu Współdzielonego w magazynie kluczy konta magazynu zarządzanego.
- `-AccountName` to nazwa konta magazynu zarządzanego w magazynie kluczy.
- `-Name` jest to identyfikator tokenu sygnatury dostępu Współdzielonego w magazynie.
- `-ValidityPeriod` Ustawia datę wygaśnięcia wygenerowanych tokenu sygnatury dostępu Współdzielonego.

```powershell
$validityPeriod = [System.Timespan]::FromDays(1)
$readSasName = "readBlobSas"
$writeSasName = "writeBlobSas"

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName $keyVaultName -AccountName $accountName -Name $readSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName $keyVaultName -AccountName $accountName -Name $writeSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List,Write
```

### <a name="get-sas-tokens"></a>Pobierz tokeny sygnatury dostępu Współdzielonego

Pobierz odpowiedni tokeny sygnatury dostępu Współdzielonego i wykonywania wywołań do magazynu. `-SecretName` jest tworzony przy użyciu danych wejściowych z `AccountName` i `Name` parametry, gdy zostanie wykonane [AzureKeyVaultManagedStorageSasDefinition zestawu](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition).

```powershell
$readSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$readSasName").SecretValueText
$writeSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$writeSasName").SecretValueText
```

### <a name="create-storage"></a>Utwórz magazyn

Zwróć uwagę, że próbujesz uzyskać dostęp z *$readSasToken* nie powiedzie się, ale ten można uzyskać dostęp z *$writeSasToken*.

```powershell
$context1 = New-AzureStorageContext -SasToken $readSasToken -StorageAccountName $storage.StorageAccountName
$context2 = New-AzureStorageContext -SasToken $writeSasToken -StorageAccountName $storage.StorageAccountName

Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt" -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt" -Context $context2
```

Jesteś stanie dostępu do obiektu blob magazynu zawartości z tokenu sygnatury dostępu Współdzielonego, który ma dostęp do zapisu.

### <a name="relevant-powershell-cmdlets"></a>Odpowiednich poleceń cmdlet programu Powershell

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="storage-account-onboarding"></a>Dołączanie konta magazynu

Przykład: jako właściciel obiektu usługi Key Vault dodawany obiekt konta magazynu do użytkownika usługa Azure Key Vault dołączeniu konto magazynu.

Podczas dołączania, Key Vault musi sprawdzić, czy tożsamość konta dołączania ma uprawnienia do *listy* i *ponownie wygenerować* magazynu kluczy. Aby można było zweryfikować te uprawnienia, Key Vault pobiera OBO (imieniu z) tokenu z usługi uwierzytelniania, odbiorców, ustaw do usługi Azure Resource Manager i sprawia, że *listy* klucza wywołanie usługi Magazyn Azure. Jeśli *listy* wywołanie zakończy się niepowodzeniem, Key Vault Tworzenie obiektu zakończy się niepowodzeniem z kodem stanu HTTP *zabroniony*. Klucze wymienione w ten sposób są buforowane z magazynem jednostek magazynu kluczy.

Magazyn kluczy należy sprawdzić, czy tożsamość *ponownie wygenerować* uprawnienia przed jego przejąć prawo własności ponowne generowanie kluczy. Aby sprawdzić, czy tożsamość, za pomocą tokenu OBO, jak również tożsamości firm pierwszej usługi Key Vault ma tych uprawnień:

- Magazyn kluczy wymieniono uprawnienia RBAC zasobu konta magazynu.
- Magazyn kluczy sprawdza poprawność odpowiedzi, używając wyrażenia regularnego dopasowanie działania i z systemem innym niż działania.

Znajdź przykłady obsługi na [magazyn kluczy - zarządzane przykłady klucze konta magazynu](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/KeyVault/dataPlane/Microsoft.Azure.KeyVault.Samples/samples/HelloKeyVault/Program.cs#L167).

Jeśli nie ma tożsamość *ponownie wygenerować* uprawnienia lub jeśli nie ma usługi Key Vault pierwszej strony tożsamości *listy* lub *ponownie wygenerować* uprawnienia, a następnie procesu dołączania żądanie nie powiedzie się, zwracając kod błędu odpowiednie i wiadomości.

OBO token działa tylko, gdy używasz firmy, aplikacje klienckie natywnego programu PowerShell lub interfejsu wiersza polecenia.

## <a name="other-applications"></a>Inne aplikacje

- Tokeny sygnatury dostępu Współdzielonego, tworzony przy użyciu kluczy konta magazynu usługi Key Vault, zapewniają bardziej kontroli dostępu do konta magazynu platformy Azure. Aby uzyskać więcej informacji, zobacz [używanie sygnatury dostępu współdzielonego](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1).

## <a name="see-also"></a>Zobacz także

- [Informacje o kluczach, wpisach tajnych i certyfikatach](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog zespołu magazyn kluczy](https://blogs.technet.microsoft.com/kv/)
