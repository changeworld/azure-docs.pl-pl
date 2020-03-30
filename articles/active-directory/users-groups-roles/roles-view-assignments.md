---
title: Wyświetlanie przypisania ról niestandardowych w portalu usługi Azure AD | Dokumenty firmy Microsoft
description: Teraz możesz wyświetlać i zarządzać członkami roli administratora usługi Azure AD w centrum administracyjnym usługi Azure AD.
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
ms.openlocfilehash: f04bd7788a9cc9657e14aedfb153182d6e53eb95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259710"
---
# <a name="view-custom-role-assignments-in-azure-active-directory"></a>Wyświetlanie przypisania ról niestandardowych w usłudze Azure Active Directory

W tym artykule opisano sposób wyświetlania ról niestandardowych przypisanych w usłudze Azure Active Directory (Azure AD). W usłudze Azure Active Directory (Azure AD) role można przypisać w zakresie dla całej organizacji lub w zakresie pojedynczej aplikacji.

- Przypisania ról w zakresie całej organizacji są dodawane i można je zobaczyć na liście przypisania ról pojedynczej aplikacji.
- Przypisania ról w zakresie pojedynczej aplikacji nie są dodawane do i nie mogą być widoczne na liście przydziałów o zakresie dla całej organizacji.

## <a name="view-role-assignments-in-the-azure-portal"></a>Wyświetlanie przypisań ról w witrynie Azure portal

W tej procedurze opisano wyświetlanie przypisań roli z zakresem całej organizacji.

1. Zaloguj się do [centrum](https://aad.portal.azure.com) administracyjnego usługi Azure AD przy za pomocą uprawnień administratora ról uprzywilejowanych lub administratora globalnego w organizacji usługi Azure AD.
1. Wybierz **pozycję Azure Active Directory**, wybierz pozycję Role i **administratorzy**, a następnie wybierz rolę, aby ją otworzyć i wyświetlić jej właściwości.
1. Wybierz **przydziały,** aby wyświetlić przydziały dla roli.

    ![Wyświetlanie przypisań i uprawnień ról podczas otwierania roli z listy](./media/roles-view-assignments/role-assignments.png)

## <a name="view-role-assignments-using-azure-ad-powershell"></a>Wyświetlanie przydziałów ról przy użyciu programu Azure AD PowerShell

W tej sekcji opisano wyświetlanie przypisań roli z zakresem całej organizacji. W tym artykule użyto modułu [programu PowerShell w usłudze Azure Active Directory w wersji 2.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) Aby wyświetlić przypisania zakresu pojedynczej aplikacji za pomocą programu PowerShell, można użyć poleceń cmdlet w [programie Przypisywanie ról niestandardowych za pomocą programu PowerShell](roles-assign-powershell.md).

### <a name="prepare-powershell"></a>Przygotowanie programu PowerShell

Najpierw należy [pobrać moduł programu PowerShell w wersji zapoznawczej usługi Azure AD](https://www.powershellgallery.com/packages/AzureAD/).

Aby zainstalować moduł programu Azure AD PowerShell, należy użyć następujących poleceń:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Aby sprawdzić, czy moduł jest gotowy do użycia, użyj następującego polecenia:

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="view-the-assignments-of-a-role"></a>Wyświetlanie przypisań roli

Przykład wyświetlania przypisań roli.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-role-assignments-using-microsoft-graph-api"></a>Wyświetlanie przypisań ról przy użyciu interfejsu API programu Microsoft Graph

W tej sekcji opisano wyświetlanie przypisań roli z zakresem całej organizacji.  Aby wyświetlić przypisania zakresu pojedynczej aplikacji przy użyciu interfejsu Graph API, można użyć operacji w [przypisywaniu ról niestandardowych za pomocą interfejsu Graph API](roles-assign-graph.md).

Żądanie HTTP, aby uzyskać przypisanie roli dla danej definicji roli.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Odpowiedź

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="view-assignments-of-single-application-scope"></a>Wyświetlanie przypisań zakresu pojedynczej aplikacji

W tej sekcji opisano wyświetlanie przypisań roli z zakresem pojedynczej aplikacji. Ta funkcja jest obecnie w publicznej wersji zapoznawczej.

1. Zaloguj się do [centrum](https://aad.portal.azure.com) administracyjnego usługi Azure AD przy za pomocą uprawnień administratora ról uprzywilejowanych lub administratora globalnego w organizacji usługi Azure AD.
1. Wybierz **pozycję Rejestracje aplikacji**, a następnie wybierz rejestrację aplikacji, aby wyświetlić jej właściwości. Może być trzeba wybrać **wszystkie aplikacje,** aby wyświetlić pełną listę rejestracji aplikacji w organizacji usługi Azure AD.

    ![Tworzenie lub edytowanie rejestracji aplikacji na stronie Rejestracje aplikacji](./media/roles-create-custom/appreg-all-apps.png)

1. W rejestracji aplikacji wybierz pozycję **Role i administratorzy**, a następnie wybierz rolę, aby wyświetlić jej właściwości.

    ![Wyświetlanie przypisań ról rejestracji aplikacji na stronie Rejestracje aplikacji](./media/roles-view-assignments/appreg-assignments.png)

1. Wybierz **przydziały,** aby wyświetlić przydziały dla roli. Otwarcie widoku przydziałów z poziomu rejestracji aplikacji pokazuje przydziały, które są ograniczone do tego zasobu usługi Azure AD.

    ![Wyświetlanie przypisań ról rejestracji aplikacji z właściwości rejestracji aplikacji](./media/roles-view-assignments/appreg-assignments-2.png)

## <a name="next-steps"></a>Następne kroki

* Zapraszam do dzielenia się z nami na [forum ról administracyjnych usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Aby uzyskać więcej informacji o rolach i przypisywaniu ról administratora, zobacz [Przypisywanie ról administratora](directory-assign-admin-roles.md).
* Aby uzyskać domyślne uprawnienia użytkownika, zobacz [porównanie domyślnych uprawnień gościa i użytkownika.](../fundamentals/users-default-permissions.md)
