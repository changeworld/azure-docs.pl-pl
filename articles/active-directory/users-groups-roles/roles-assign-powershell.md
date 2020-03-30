---
title: Przypisywanie ról niestandardowych przy użyciu programu Azure PowerShell — Azure AD | Dokumenty firmy Microsoft
description: Zarządzanie członkami roli niestandardowej administratora usługi Azure AD za pomocą programu Azure PowerShell.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a2096b7899039e7a9d3455bc0c6fb3ec84ebd1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025319"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Przypisywanie ról niestandardowych z zakresem zasobów przy użyciu programu PowerShell w usłudze Azure Active Directory

W tym artykule opisano sposób tworzenia przypisania roli w zakresie całej organizacji w usłudze Azure Active Directory (Azure AD). Przypisywanie roli w zakresie obejmującym całą organizację zapewnia dostęp w całej organizacji usługi Azure AD. Aby utworzyć przypisanie roli z zakresem pojedynczego zasobu usługi Azure AD, zobacz [Jak utworzyć rolę niestandardową i przypisać ją do zakresu zasobu](roles-create-custom.md). W tym artykule użyto modułu [programu PowerShell w usłudze Azure Active Directory w wersji 2.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles)

Aby uzyskać więcej informacji na temat ról administratora usługi Azure AD, zobacz [Przypisywanie ról administratora w usłudze Azure Active Directory](directory-assign-admin-roles.md).

## <a name="required-permissions"></a>Wymagane uprawnienia

Połącz się z dzierżawą usługi Azure AD przy użyciu konta administratora globalnego, aby przypisać lub usunąć role.

## <a name="prepare-powershell"></a>Przygotowanie programu PowerShell

Zainstaluj moduł programu Azure AD PowerShell z [Galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Następnie zaimportuj moduł w wersji zapoznawczej programu Azure AD PowerShell, używając następującego polecenia:

``` PowerShell
import-module azureadpreview
```

Aby sprawdzić, czy moduł jest gotowy do użycia, dopasuj wersję zwróconą przez następujące polecenie do tej wymienionej w tym miejscu:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

Teraz możesz zacząć używać poleceń cmdlet w module. Aby uzyskać pełny opis poleceń cmdlet w module usługi Azure AD, zobacz dokumentację odwołania online dla [modułu usługi Azure AD w wersji zapoznawczej.](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>Przypisywanie roli do jednostki użytkownika lub jednostki usługi z zakresem zasobu

1. Otwórz moduł programu PowerShell w wersji Zapoznawczej usługi Azure AD.
1. Zaloguj się, wykonując `Connect-AzureAD`polecenie .
1. Utwórz nową rolę przy użyciu następującego skryptu programu PowerShell.

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

Aby przypisać rolę do jednostki usługi zamiast użytkownika, należy użyć [polecenia cmdlet Get-AzureADMSServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0).

## <a name="operations-on-roledefinition"></a>Operacje na funkcji Definicja roli

Obiekty definicji roli zawierają definicję wbudowanej lub niestandardowej roli wraz z uprawnieniami przyznanymi przez to przypisanie roli. Ten zasób wyświetla zarówno niestandardowe definicje ról, jak i wbudowane role katalogów (które są wyświetlane w postaci równoważnej funkcjiDefiniowanie). Obecnie organizacja usługi Azure AD może mieć maksymalnie 30 unikatowych niestandardowych niestandardowych funkcji RoleDefinitions zdefiniowanych.

### <a name="create-operations-on-roledefinition"></a>Tworzenie operacji na definicji roli

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/standard/read",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-operations-on-roledefinition"></a>Odczyt operacji w funkcji Definicja roli

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-operations-on-roledefinition"></a>Aktualizuj operacje w celu zdefiniowania funkcji RoleDefinition

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>Usuwanie operacji na funkcji Definicja roli

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="operations-on-roleassignment"></a>Operacje na znakowanie roleassignment

Przypisania ról zawierają informacje łączące dany podmiot zabezpieczeń (podmiotu usługi użytkownika lub usługi aplikacji) z definicją roli. W razie potrzeby można dodać zakres pojedynczego zasobu usługi Azure AD dla przypisanych uprawnień.  Ograniczanie zakresu uprawnień jest obsługiwane dla ról wbudowanych i niestandardowych.

### <a name="create-operations-on-roleassignment"></a>Tworzenie operacji na znakowanie roli

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

### <a name="read-operations-on-roleassignment"></a>Odczyt operacji na znakowanie roleAssignment

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>Usuwanie operacji na znakowanie roli

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Następne kroki

- Podziel się z nami na [forum ról administracyjnych usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Aby uzyskać więcej informacji o rolach i przypisaniach ról administratora usługi Azure AD, zobacz [Przypisywanie ról administratora](directory-assign-admin-roles.md).
- Aby uzyskać domyślne uprawnienia użytkownika, zobacz [porównanie domyślnych uprawnień gościa i użytkownika.](../fundamentals/users-default-permissions.md)
