---
title: Przypisz i Usuń przypisania ról administratora przy użyciu programu Azure PowerShell — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dla tych, którzy często zarządzać przypisaniami ról można teraz zarządzać członkowie roli administratora usługi Azure AD przy użyciu programu Azure PowerShell.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6877c3e547d625cf58129a546dae798b37a24ae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60469098"
---
# <a name="assign-azure-active-directory-admin-roles-using-powershell"></a>Przypisywanie ról administratora usługi Azure Active Directory przy użyciu programu PowerShell

Można zautomatyzować, jak przypisać role do kont użytkowników przy użyciu programu Azure PowerShell. W tym artykule wykorzystano [usługi Azure Active Directory PowerShell w wersji 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) modułu.

## <a name="prepare-powershell"></a>Przygotowanie środowiska PowerShell

Najpierw musisz [pobieranie modułu programu PowerShell usługi Azure AD](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Instalowanie modułu Azure AD PowerShell

Aby zainstalować moduł programu Azure AD PowerShell, użyj następujących poleceń:

```powershell
install-module azuread
import-module azuread
```

Aby sprawdzić, czy moduł jest gotowy do użycia, użyj następującego polecenia:

```powershell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

Teraz można uruchomić przy użyciu poleceń cmdlet w module. Pełny opis poleceń cmdlet w module usługi Azure AD, można znaleźć w dokumentacji online dla [usługi Azure Active Directory PowerShell w wersji 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles).

## <a name="permissions-required"></a>Wymagane uprawnienia

Połącz z dzierżawą usługi Azure AD umożliwia przypisywanie lub usuwanie ról przy użyciu konta administratora globalnego.

## <a name="assign-a-single-role"></a>Przypisz pojedynczej roli

Aby przypisać rolę, należy najpierw uzyskać jego nazwę wyświetlaną i nazwę roli, do którego są przypisywane. Gdy nazwa wyświetlana konta i nazwa roli, należy użyć następujących poleceń cmdlet można przypisać rolę użytkownikowi.

``` PowerShell
# Fetch user to assign to role
$roleMember = Get-AzureADUser -ObjectId "username@contoso.com"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add user to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="assign-a-role-to-a-service-principal"></a>Przypisywanie roli do jednostki usługi

Przykład przypisanie jednostki usługi do roli.

```powershell
# Fetch a service principal to assign to role
$roleMember = Get-AzureADServicePrincipal -ObjectId "00221b6f-4387-4f3f-aa85-34316ad7f956"
 
#Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole
 
# Fetch a directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"
 
# Add user to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId
 
# Fetch the assignment for the role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADServicePrincipal
```

## <a name="multiple-role-assignments"></a>Przypisania wielu ról

Przykłady przypisywania i usuwania jednocześnie wiele ról.

```powershell
#File name
$fileName="<file path>" 
 
$input_Excel = New-Object -ComObject Excel.Application
$input_Workbook = $input_Excel.Workbooks.Open($fileName)
$input_Worksheet = $input_Workbook.Sheets.Item(1)
 
        #Count number of users who have to be assigned to role
$count = $input_Worksheet.UsedRange.Rows.Count
 
#Loop through each line of the csv file starting from line 2 (assuming first line is title)
for ($i=2; $i -le $count; $i++)
{
       #Fetch user display name
       $displayName = $input_Worksheet.Cells.Item($i,1).Text
       
       #Fetch role name
       $roleName = $input_Worksheet.Cells.Item($i,2).Text
 
       #Assign role
       Add-AzureADDirectoryRoleMember -ObjectId (Get-AzureADDirectoryRole | Where-Object DisplayName -eq $roleName).ObjectId -RefObjectId (Get-AzureADUser | Where-Object DisplayName -eq $displayName).ObjectId
}
 
#Remove multiple role assignments
for ($i=2; $i -le $count; $i++)
{
       $displayName = $input_Worksheet.Cells.Item($i,1).Text
       $roleName = $input_Worksheet.Cells.Item($i,2).Text
 
       Remove-AzureADDirectoryRoleMember -ObjectId (Get-AzureADDirectoryRole | Where-Object DisplayName -eq $roleName).ObjectId -MemberId (Get-AzureADUser | Where-Object DisplayName -eq $displayName).ObjectId
}
```

## <a name="remove-a-role-assignment"></a>Usuwanie przypisania roli

W tym przykładzie usuwa przypisanie roli dla określonego użytkownika.

```powershell
# Fetch user to assign to role
$roleMember = Get-AzureADUser -ObjectId "username@contoso.com"

#Fetch list of all directory roles with object id
Get-AzureADDirectoryRole
 
# Fetch a directory role by id
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"
 
# Remove user from role
Remove-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -MemberId $roleMember.ObjectId 

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
 
```

## <a name="next-steps"></a>Kolejne kroki

* Możesz udostępnić w [forum ról administracyjnych usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Więcej informacji na temat ról i przypisywania ról administratora, zobacz [przypisywania ról administratora](directory-assign-admin-roles.md).
* Aby uzyskać domyślne uprawnienia użytkowników, zobacz [porównanie domyślne uprawnienia użytkownika gościa i element członkowski](../fundamentals/users-default-permissions.md).
