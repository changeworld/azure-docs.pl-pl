---
title: Usuwanie limitów tworzenia rejestracji aplikacji — Usługa Azure AD | Dokumenty firmy Microsoft
description: Przypisywanie roli niestandardowej w celu udzielania nieograniczonych rejestracji aplikacji w usłudze Azure AD Directory Active Directory
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
ms.openlocfilehash: 7acd76ff45f783f614b2a1d3f0d5c10d800a1ea9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77559049"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Szybki start: udzielanie uprawnień do tworzenia nieograniczonej liczby rejestracji aplikacji

W tym przewodniku Szybki start utworzysz rolę niestandardową z uprawnieniami do tworzenia nieograniczonej liczby rejestracji aplikacji, a następnie przypiszesz tę rolę do użytkownika. Przypisany użytkownik może następnie użyć portalu usługi Azure AD, usługi Azure AD PowerShell lub interfejsu API programu Microsoft Graph do tworzenia rejestracji aplikacji. W przeciwieństwie do wbudowanej roli dewelopera aplikacji ta rola niestandardowa daje możliwość tworzenia nieograniczonej liczby rejestracji aplikacji. Rola Deweloper aplikacji daje możliwość, ale całkowita liczba utworzonych obiektów jest ograniczona do 250, aby zapobiec trafieniu [przydziału obiektów całego katalogu](directory-service-limits-restrictions.md).

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisite"></a>Wymagania wstępne

Najmniej uprzywilejowaną rolą wymaganą do tworzenia i przypisywania ról niestandardowych usługi Azure AD jest administrator roli uprzywilejowanej.

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Tworzenie nowej roli niestandardowej przy użyciu portalu usługi Azure AD

1. Zaloguj się do [centrum](https://aad.portal.azure.com) administracyjnego usługi Azure AD przy wsparciu administratora roli uprzywilejowanej lub administratora globalnego w organizacji usługi Azure AD.
1. Wybierz **pozycję Azure Active Directory**, wybierz pozycję Role i **administratorzy**, a następnie wybierz pozycję **Nowa rola niestandardowa**.

    ![Tworzenie lub edytowanie ról na stronie Role i administratorzy](./media/roles-create-custom/new-custom-role.png)

1. Na karcie **Podstawy** podaj "Kreator rejestracji aplikacji" dla nazwy roli i "Może utworzyć nieograniczoną liczbę rejestracji aplikacji" dla opisu roli, a następnie wybierz **pozycję Dalej**.

    ![podaj nazwę i opis roli niestandardowej na karcie Podstawy](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. Na karcie **Uprawnienia** wprowadź w polu wyszukiwania pozycję "microsoft.directory/applications/create", a następnie zaznacz pola wyboru obok żądanych uprawnień, a następnie wybierz pozycję **Dalej**.

    ![Wybieranie uprawnień do roli niestandardowej na karcie Uprawnienia](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. Na karcie **Recenzja + tworzenie** przejrzyj uprawnienia i wybierz pozycję **Utwórz**.

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Przypisywanie roli do użytkownika przy użyciu portalu usługi Azure AD

1. Zaloguj się do [centrum](https://aad.portal.azure.com) administracyjnego usługi Azure AD przy za pomocą uprawnień administratora ról uprzywilejowanych lub administratora globalnego w organizacji usługi Azure AD.
1. Wybierz **pozycję Usługa Azure Active Directory,** a następnie wybierz pozycję Role i **administratorzy**.
1. Wybierz rolę Twórca rejestracji aplikacji i wybierz pozycję **Dodaj przypisanie**.
1. Wybierz żądanego użytkownika i kliknij przycisk **Wybierz,** aby dodać go do roli.

Gotowe! W tym przewodniku Szybki start pomyślnie utworzono rolę niestandardową z uprawnieniami do tworzenia nieograniczonej liczby rejestracji aplikacji, a następnie przypisać tę rolę do użytkownika.

> [!TIP]
> Aby przypisać rolę do aplikacji za pomocą portalu usługi Azure AD, wprowadź nazwę aplikacji w polu wyszukiwania strony przypisania. Aplikacje nie są domyślnie wyświetlane na liście, ale są zwracane w wynikach wyszukiwania.

### <a name="app-registration-permissions"></a>Uprawnienia do rejestracji aplikacji

Istnieją dwa uprawnienia dostępne do udzielania możliwości tworzenia rejestracji aplikacji, każdy z różnych zachowań.

- microsoft.directory/applications/createAsOwner: Przypisanie tego uprawnienia powoduje, że twórca jest dodawany jako pierwszy właściciel utworzonej rejestracji aplikacji, a utworzona rejestracja aplikacji będzie wliczane do przydziału 250 utworzonych obiektów twórcy.
- microsoft.directory/applicationPolicies/create: Przypisanie tego uprawnienia powoduje, że twórca nie jest dodawany jako pierwszy właściciel utworzonej rejestracji aplikacji, a utworzona rejestracja aplikacji nie będzie wliczać się do przydziału 250 utworzonych obiektów twórcy. Użyj tego uprawnienia ostrożnie, ponieważ nic nie stoi na przeszkodzie, aby cesjonariusz tworzył rejestracje aplikacji, dopóki przydział na poziomie katalogu nie zostanie trafiony. Jeśli oba uprawnienia są przypisane, to uprawnienie ma pierwszeństwo.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Tworzenie roli niestandardowej przy użyciu programu Azure AD PowerShell

Utwórz nową rolę przy użyciu następującego skryptu programu PowerShell:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/createAsOwner"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Przypisywanie roli niestandardowej przy użyciu programu Azure AD PowerShell

#### <a name="prepare-powershell"></a>Przygotowanie programu PowerShell

Najpierw zainstaluj moduł programu Azure AD PowerShell z [Galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Następnie zaimportuj moduł w wersji zapoznawczej programu Azure AD PowerShell, używając następującego polecenia:

```powershell
import-module azureadpreview
```

Aby sprawdzić, czy moduł jest gotowy do użycia, dopasuj wersję zwróconą przez następujące polecenie do tej wymienionej w tym miejscu:

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>Przypisywanie roli niestandardowej

Przypisz rolę przy użyciu poniższego skryptu programu PowerShell:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>Tworzenie roli niestandardowej przy użyciu interfejsu API programu Microsoft Graph

Żądanie HTTP, aby utworzyć rolę niestandardową.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

Treść

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Przypisywanie roli niestandardowej przy użyciu interfejsu API programu Microsoft Graph

Przypisanie roli łączy identyfikator jednostki zabezpieczeń (który może być użytkownikiem lub jednostką usługi), identyfikator definicji roli (roli) i zakres zasobów usługi Azure AD.

Żądanie HTTP, aby przypisać rolę niestandardową.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Treść

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Następne kroki

- Zapraszam do dzielenia się z nami na [forum ról administracyjnych usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Aby uzyskać więcej informacji o rolach i przypisywaniu ról administratora, zobacz [Przypisywanie ról administratora](directory-assign-admin-roles.md).
- Aby uzyskać domyślne uprawnienia użytkownika, zobacz [porównanie domyślnych uprawnień gościa i użytkownika.](../fundamentals/users-default-permissions.md)
