---
title: Usługa Azure Key Vault zarządzanego konta magazynu — wersja programu PowerShell
description: Funkcja konta magazynu zarządzanego zapewnia seemless integrację, usługę Azure Key Vault i konto magazynu platformy Azure.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 9b6089aa828b5667f100c1a8cbff3e69345e4512
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66150425"
---
# <a name="azure-key-vault-managed-storage-account---powershell"></a>Usługa Azure Key Vault zarządzanego konta magazynu — PowerShell

> [!NOTE]
> [Integracja usługi Azure storage z usługą Azure Active Directory (Azure AD) jest teraz dostępna w wersji zapoznawczej](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Firma Microsoft zaleca używanie programu Azure AD do uwierzytelniania i autoryzacji, co zapewnia OAuth2 opartego na tokenach dostępu do usługi Azure storage, podobnie jak usługi Azure Key Vault. Dzięki temu można:
> - Uwierzytelniania aplikacji klienckiej przy użyciu tożsamości aplikacji lub użytkownika, a nie poświadczenia konta magazynu. 
> - Użyj [tożsamości zarządzanej usługi Azure AD](/azure/active-directory/managed-identities-azure-resources/) podczas uruchamiania na platformie Azure. Zarządzane, Usuń tożsamości potrzeby uwierzytelniania klienta, które razem i zapisywanie poświadczeń na lub z aplikacją.
> - Użyj kontroli dostępu na podstawie ról (RBAC) do zarządzania autoryzacji, co jest również obsługiwana przez usługi Key Vault.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Konta usługi Azure storage](/azure/storage/storage-create-storage-account) używa poświadczeń, który składa się z nazwy konta i klucz. Klucz jest generowana automatycznie i służy tylko jako "password" w przeciwieństwie do klucza kryptograficznego. Usługa Key Vault można zarządzać te klucze konta magazynu, przechowując je jako [wpisy tajne usługi Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

## <a name="overview"></a>Przegląd

Magazynu kluczy zarządzanego konta magazynu, że funkcja wykonuje kilka funkcji zarządzania w Twoim imieniu:

- Klucze listy (synchronizacje) za pomocą konta usługi Azure storage.
- Ponownie generuje (rotuje) okresowo kluczy.
- Zarządza klucze kont magazynu i klasycznego konta magazynu.
- Wartości kluczy nigdy nie są zwracane w odpowiedzi na obiekt wywołujący.

Kiedy używasz funkcji klucza konta magazynu zarządzanego:

- **Zezwalaj tylko na usłudze Key Vault do zarządzania kluczami konta magazynu.** Nie podejmuj próby zarządzać samodzielnie, jak będzie zakłócać procesów usługi Key Vault.
- **Nie zezwalaj na klucze konta magazynu, które będą zarządzane przez więcej niż jeden obiekt usługi Key Vault**.
- **Ręcznie nie Generuj ponownie klucze konta magazynu**. Firma Microsoft zaleca regenerować je za pośrednictwem usługi Key Vault.

Poniższy przykład pokazuje, jak umożliwić usługi Key Vault do zarządzania kluczami konta magazynu.

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>Autoryzuj Key Vault w celu uzyskania dostępu do konta magazynu

> [!IMPORTANT]
> Dzierżawa usługi Azure AD zapewnia każdej aplikacji zarejestrowanej za pomocą  **[nazwy głównej usługi](/azure/active-directory/develop/developer-glossary#service-principal-object)**, który służy jako tożsamości aplikacji. Identyfikator aplikacji nazwy głównej usługi jest używany w przypadku nadania jej zezwolenie na dostęp do innych zasobów platformy Azure za pośrednictwem kontroli dostępu opartej na rolach (RBAC). Ponieważ usługa Key Vault jest aplikacją firmy Microsoft, jest wstępnie zarejestrowane w wszystkich dzierżaw usługi Azure AD, w tym samym Identyfikatorem aplikacji w ramach każdej chmury platformy Azure:
> - Usługa Azure dzierżaw usługi AD w chmurze platformy Azure dla instytucji rządowych użyć Identyfikatora aplikacji `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - Azure dzierżaw usługi AD w chmurze publicznej Azure oraz wszystkich innych użyć Identyfikatora aplikacji `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

Zanim usługi Key Vault można uzyskać dostęp i zarządzanie kluczami konta magazynu, należy autoryzować dostępu konta magazynu. Aplikacja usługi Key Vault wymaga uprawnień do *listy* i *ponownie wygenerować* klucze konta magazynu. Te uprawnienia są włączone za pomocą wbudowanych ról RBAC [rola usługi Operator klucza konta magazynu](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role). 

Tę rolę można przypisać do usługi Key Vault jednostki usługi, ograniczając zakres do swojego konta magazynu wykonując następujące kroki. Pamiętaj zaktualizować `$resourceGroupName`, `$storageAccountName`, `$storageAccountKey`, i `$keyVaultName` zmiennych przed uruchomieniem skryptu:

```azurepowershell-interactive
# TODO: Update with the resource group where your storage account resides, your storage account name, the name of your active storage account key, and your Key Vault instance name
$resourceGroupName = "rgContoso"
$storageAccountName = "sacontoso"
$storageAccountKey = "key1"
$keyVaultName = "kvContoso"
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093" # See "IMPORTANT" block above for information on Key Vault Application IDs

# Authenticate your PowerShell session with Azure AD, for use with Azure Resource Manager cmdlets
$azureProfile = Connect-AzAccount

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Po przypisaniu roli pomyślne powinny zostać wyświetlone dane wyjściowe podobne do poniższego przykładu:

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

Jeśli magazyn kluczy został już dodany do roli w ramach konta magazynu, otrzymasz *"przypisanie roli już istnieje".* Wystąpił błąd. Można również sprawdzić przypisanie roli za pomocą strony "Kontrola dostępu (IAM)" konta magazynu w witrynie Azure portal.  

## <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Zezwolić konto użytkownika do kont magazynu zarządzanego

>[!TIP] 
> Podobnie jak usługa Azure AD zapewnia **nazwy głównej usługi** tożsamości aplikacji **głównej nazwy użytkownika** towarzyszy tożsamości użytkownika. Następnie można podać nazwy głównej użytkownika autoryzacji dostępu do usługi Key Vault za pomocą uprawnień zasad dostępu magazynu kluczy.

Korzystając z tej samej sesji programu PowerShell, zaktualizuj zasady dostępu magazynu kluczy dla kont magazynu zarządzanego. Ten krok dotyczy konta użytkownika, zapewniając dostęp funkcji konto magazynu zarządzanego uprawnienia dla konta magazynu: 

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $azureProfile.Context.Account.Id -PermissionsToStorage get, list, listsas, delete, set, update, regeneratekey, recover, backup, restore, purge
```

Należy pamiętać, że uprawnienia dla konta magazynu nie są dostępne na stronie "Zasady dostępu" konta magazynu w witrynie Azure portal.

## <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Dodaj konto magazynu zarządzanego wystąpienia usługi Key Vault

Korzystając z tej samej sesji programu PowerShell, Utwórz konto magazynu zarządzanego wystąpienia usługi Key Vault. `-DisableAutoRegenerateKey` Przełącznik określa nie można ponownie wygenerować kluczy konta magazynu.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Na pomyślne dodanie konta magazynu z nie ponownego wygenerowania klucza powinny zostać wyświetlone dane wyjściowe podobne do poniższego przykładu:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

### <a name="enable-key-regeneration"></a>Włącz ponowne generowanie klucza

Jeśli chcesz, aby okresowo ponownie wygenerować kluczy konta magazynu Key Vault, możesz ustawić okres ponownego generowania. W poniższym przykładzie możemy ustawić okres ponownego generowania trzech dni. Po upływie trzech dni Key Vault ponownie wygenerować "klucz1" i Zamień aktywnego klucza, z "klucz2" do "klucz1".

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Na pomyślne dodanie konta magazynu z ponownego wygenerowania klucza powinny zostać wyświetlone dane wyjściowe podobne do poniższego przykładu:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzane przykłady klucza konta magazynu](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Informacje o kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-and-certificates.md)
- [Dokumentacja programu PowerShell dla klucza magazynu](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
