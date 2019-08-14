---
title: Azure Key Vault zarządzane konto magazynu — wersja programu PowerShell
description: Funkcja zarządzanego konta magazynu zapewnia niezawodną integrację między Azure Key Vault i kontem usługi Azure Storage.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 708c34347966eee7817ca04e0552dcba233765cb
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934510"
---
# <a name="azure-key-vault-managed-storage-account---powershell"></a>Azure Key Vault zarządzane konto magazynu — PowerShell

> [!NOTE]
> [Integracja usługi Azure Storage z usługą Azure Active Directory (Azure AD) jest teraz w wersji](../storage/common/storage-auth-aad.md)zapoznawczej. Zalecamy używanie usługi Azure AD do uwierzytelniania i autoryzacji, która zapewnia dostęp oparty na tokenach OAuth2 do usługi Azure Storage, podobnie jak Azure Key Vault. Dzięki temu można:
> - Uwierzytelnianie aplikacji klienckiej przy użyciu tożsamości aplikacji lub użytkownika zamiast poświadczeń konta magazynu. 
> - Użyj [tożsamości zarządzanej usługi Azure AD](/azure/active-directory/managed-identities-azure-resources/) w przypadku uruchamiania na platformie Azure. Tożsamości zarządzane usuwają potrzebę wszystkich jednocześnie uwierzytelniania klienta i przechowywania poświadczeń w aplikacji lub z aplikacją.
> - Użyj Access Control opartej na rolach (RBAC) do zarządzania autoryzacją, która jest również obsługiwana przez Key Vault.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Konto usługi Azure Storage](/azure/storage/storage-create-storage-account) używa poświadczeń, które składają się z nazwy konta i klucza. Klucz jest generowany automatycznie i służy jako "hasło" zamiast klucza kryptograficznego. Key Vault mogą zarządzać tymi kluczami konta magazynu, przechowując je jako wpisy [tajne Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

## <a name="overview"></a>Przegląd

Funkcja zarządzanego konta magazynu Key Vault wykonuje kilka funkcji zarządzania w Twoim imieniu:

- Wyświetla listę (synchronizuje) klucze z kontem usługi Azure Storage.
- Ponownie generuje (obraca) klucze okresowo.
- Zarządza kluczami dla kont magazynu i klasycznych kont magazynu.
- Wartości klucza nigdy nie są zwracane w odpowiedzi na obiekt wywołujący.

W przypadku korzystania z funkcji klucza zarządzanego konta magazynu:

- **Zezwalaj Key Vault na zarządzanie kluczami konta magazynu.** Nie próbuj zarządzać nimi samodzielnie, ponieważ przeszkadzasz w procesach Key Vault.
- **Nie Zezwalaj na zarządzanie kluczami konta magazynu przez więcej niż jeden obiekt Key Vault**.
- **Nie należy ręcznie generować ponownie kluczy konta magazynu**. Zalecamy ponowne wygenerowanie ich za pośrednictwem Key Vault.

W poniższym przykładzie pokazano, jak zezwolić Key Vault na zarządzanie kluczami konta magazynu.

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>Autoryzuj Key Vault, aby uzyskać dostęp do konta magazynu

> [!IMPORTANT]
> Dzierżawa usługi Azure AD zapewnia każdej zarejestrowanej aplikacji za pomocą **[nazwy głównej usługi](/azure/active-directory/develop/developer-glossary#service-principal-object)** , która służy jako tożsamość aplikacji. Identyfikator aplikacji nazwy głównej usługi jest używany podczas nadawania autoryzacji IT dostępu do innych zasobów platformy Azure za pośrednictwem kontroli dostępu opartej na rolach (RBAC). Ponieważ Key Vault jest aplikacją firmy Microsoft, jest wstępnie zarejestrowana we wszystkich dzierżawach usługi Azure AD w ramach tego samego identyfikatora aplikacji w ramach każdej chmury platformy Azure:
> - Dzierżawy usługi Azure AD w chmurze platformy Azure dla instytucji `7e7c393b-45d0-48b1-a35e-2905ddf8183c`rządowych używają identyfikatora aplikacji.
> - Dzierżawy usługi Azure AD w chmurze publicznej platformy Azure i wszystkie inne korzystają `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`z identyfikatora aplikacji.

Aby Key Vault mógł uzyskać dostęp do kluczy konta magazynu i zarządzać nimi, musisz autoryzować swój dostęp do konta magazynu. Aplikacja Key Vault wymaga uprawnień do *wyświetlania* i ponownego *generowania* kluczy dla konta magazynu. Te uprawnienia są włączane za pomocą wbudowanej [roli usługi operatora kluczy konta magazynu](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)roli RBAC. 

Przypisz tę rolę do jednostki usługi Key Vault, ograniczając zakres do konta magazynu, wykonując poniższe kroki. Pamiętaj `$resourceGroupName`, aby zaktualizować zmienne, `$storageAccountName`, `$storageAccountKey`, i `$keyVaultName` przed uruchomieniem skryptu:

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

Po pomyślnym przypisaniu roli powinny zostać wyświetlone dane wyjściowe podobne do następującego przykładu:

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

Jeśli Key Vault został już dodany do roli na koncie magazynu, zostanie wyświetlony komunikat *"przypisanie roli już istnieje".* Porn. Możesz również zweryfikować przypisanie roli przy użyciu strony "kontrola dostępu (IAM)" konta magazynu w Azure Portal.  

## <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Nadaj kontu użytkownika uprawnienia do zarządzanych kont magazynu

>[!TIP] 
> Podobnie jak usługa Azure AD udostępnia nazwę **główną usługi** dla tożsamości aplikacji, **podmiot użytkownika** jest dostarczany dla tożsamości użytkownika. Następnie można uzyskać autoryzację dostępu do Key Vault przy użyciu uprawnień zasad dostępu Key Vault.

Korzystając z tej samej sesji programu PowerShell, zaktualizuj zasady dostępu Key Vault dla zarządzanych kont magazynu. Ten krok dotyczy uprawnień konta magazynu na koncie użytkownika, dzięki czemu można uzyskać dostęp do funkcji zarządzanego konta magazynu: 

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $azureProfile.Context.Account.Id -PermissionsToStorage get, list, listsas, delete, set, update, regeneratekey, recover, backup, restore, purge
```

Należy pamiętać, że uprawnienia dla kont magazynu nie są dostępne na stronie "zasady dostępu" konta magazynu w Azure Portal.

## <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Dodawanie zarządzanego konta magazynu do wystąpienia Key Vault

Korzystając z tej samej sesji programu PowerShell, Utwórz konto magazynu zarządzanego w wystąpieniu Key Vault. `-DisableAutoRegenerateKey` Przełącznik określa, aby nie generować ponownie kluczy konta magazynu.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Po pomyślnym dodaniu konta magazynu bez ponownego wygenerowania klucza powinny zostać wyświetlone dane wyjściowe podobne do następującego przykładu:

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

Jeśli chcesz, aby Key Vault okresowo generować ponownie klucze konta magazynu, możesz ustawić okres regeneracji. W poniższym przykładzie ustawimy okres regeneracji o trzy dni. Po upływie trzech dni Key Vault ponownie wygeneruje element "Klucz1" i zamianę aktywnego klucza z "klucz2" na "Klucz1".

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Po pomyślnym dodaniu konta magazynu z ponownym generowaniem kluczy powinny zostać wyświetlone dane wyjściowe podobne do następującego przykładu:

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

## <a name="next-steps"></a>Następne kroki

- [Przykłady kluczy zarządzanego konta magazynu](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Informacje o kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-and-certificates.md)
- [Dokumentacja programu Key Vault PowerShell](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
