---
title: Przypisywanie ról niestandardowych z zakresem zasobów przy użyciu Azure PowerShell-Azure Active Directory | Microsoft Docs
description: Zarządzaj członkami roli niestandardowej administratora usługi Azure AD przy użyciu Azure PowerShell.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/05/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ed8df92ac6a43a6cbf935bdb564f6cf0658608a
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812786"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Przypisywanie ról niestandardowych z zakresem zasobów przy użyciu programu PowerShell w programie Azure Active Directory

W tym artykule opisano sposób tworzenia przypisania roli w zakresie całej organizacji w Azure Active Directory (Azure AD). Przypisywanie roli w zakresie szerokiej organizacji daje dostęp w całej organizacji usługi Azure AD. Aby utworzyć przypisanie roli z zakresem pojedynczego zasobu usługi Azure AD, zobacz [jak utworzyć rolę niestandardową i przypisać ją do zakresu zasobów](roles-create-custom.md). W tym artykule jest wykorzystywany moduł [Azure Active Directory PowerShell w wersji 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) .

Aby uzyskać więcej informacji na temat ról administratorów usługi Azure AD, zobacz [Przypisywanie ról administratorów w Azure Active Directory](directory-assign-admin-roles.md).

## <a name="required-permissions"></a>Wymagane uprawnienia

Połącz się z dzierżawą usługi Azure AD przy użyciu konta administratora globalnego do przypisywania lub usuwania ról.

## <a name="prepare-powershell"></a>Przygotowywanie programu PowerShell

Zainstaluj moduł Azure AD PowerShell z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Następnie zaimportuj moduł programu Azure AD PowerShell w wersji zapoznawczej przy użyciu następującego polecenia:

``` PowerShell
import-module azureadpreview
```

Aby sprawdzić, czy moduł jest gotowy do użycia, Dopasuj wersję zwróconą przez następujące polecenie do wymienionego poniżej:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

Teraz możesz zacząć korzystać z poleceń cmdlet w module. Aby zapoznać się z pełnymi opisami poleceń cmdlet w module usługi Azure AD, zobacz dokumentację referencyjną online [modułu usługi Azure AD w wersji](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)zapoznawczej.

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>Przypisywanie roli do użytkownika lub nazwy głównej usługi z zakresem zasobów

1. Otwórz moduł programu PowerShell usługi Azure AD w wersji zapoznawczej.
1. Zaloguj się, wykonując polecenie `Connect-AzureAD`.
1. Utwórz nową rolę przy użyciu poniższego skryptu programu PowerShell.

``` PowerShell
# Get the user and role definition you want to link
 $user = Get-AzureADMSUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
    "displayName eq 'f/128 Filter Photos'"
$resourceScopes = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScopes $resourceScopes -RoleDefinitionId $roleDefinition.objectId -PrincipalId $user.objectId
```

Aby przypisać rolę do jednostki usługi zamiast użytkownika, należy użyć [polecenia cmdlet Get-AzureADMSServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0).

## <a name="operations-on-roledefinition"></a>Operacje na definicji

Obiekty definicji ról zawierają definicję wbudowanej lub niestandardowej roli wraz z uprawnieniami przyznanymi przez to przypisanie roli. Ten zasób wyświetla zarówno definicje ról niestandardowych, jak i wbudowane directoryRoles (które są wyświetlane w postaci równoważnej definicji). Obecnie organizacja usługi Azure AD może mieć maksymalnie 30 unikatowych niestandardowych RoleDefinitions zdefiniowanych przez użytkownika.

### <a name="create-operations-on-roledefinition"></a>Tworzenie operacji na definicji

``` PowerShell
# Basic information

$description = "Application Registration Credential Administrator"
$displayName = "Custom Demo Admin"
$resourceScopes = @('/')
$templateId = "355aed8a-864b-4e2b-b225-ea95482e7570"

# Set of actions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/default/read",
    "microsoft.directory/applications/credentials/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinitions -RolePermissions $rolePermissions -ResourceScopes $resourceScopes -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-operations-on-roledefinition"></a>Operacje odczytu na definicji

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
$customAdmin = Get-AzureADMSRoleDefinitions -ObjectId '86593cfc-114b-4a15-9954-97c3494ef49b'

# Get single role definition by templateId
$customAdmin = Get-AzureADMSRoleDefinitions -Filter "templateId eq '355aed8a-864b-4e2b-b225-ea95482e757not
```

### <a name="update-operations-on-roledefinition"></a>Aktualizowanie operacji na definicji

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinitions -ObjectId $customAdmin.ObjectId -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>Operacje usuwania na definicji

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -ObjectId $customAdmin.ObjectId
```

## <a name="operations-on-roleassignment"></a>Operacje na RoleAssignment

Przypisania ról zawierają informacje łączące dany podmiot zabezpieczeń (nazwę główną użytkownika lub usługi aplikacji) z definicją roli. W razie potrzeby można dodać zakres pojedynczego zasobu usługi Azure AD dla przypisanych uprawnień.  Ograniczanie zakresu uprawnień jest obsługiwane w przypadku ról wbudowanych i niestandardowych.

### <a name="create-operations-on-roleassignment"></a>Tworzenie operacji na RoleAssignment

``` PowerShell
# Scopes to scope granted permissions to
$resourceScopes = @('/')

# IDs of principal and role definition you want to link
$principalId = "27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac"
$roleDefinitionId = $customKeyCredAdmin.ObjectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignments -ResourceScopes $resourceScopes -RoleDefinitionId -PrincipalId $principalId
```

### <a name="read-operations-on-roleassignment"></a>Operacje odczytu na RoleAssignment

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignments -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignments -Filter "principalId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>Operacje usuwania na RoleAssignment

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignments -ObjectId $roleAssignment.ObjectId
```

## <a name="next-steps"></a>Następne kroki

- Podziel się z nami na [forum ról administracyjnych usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Aby uzyskać więcej informacji o rolach i przypisaniach ról administratora usługi Azure AD, zobacz [Przypisywanie ról administratorów](directory-assign-admin-roles.md).
- Domyślne uprawnienia użytkownika można znaleźć w [porównaniu z domyślnymi uprawnieniami gościa i użytkownika](../fundamentals/users-default-permissions.md).
