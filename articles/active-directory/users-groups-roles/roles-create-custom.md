---
title: Utwórz niestandardową definicję roli w kontroli dostępu opartej na rolach usługi Azure AD — Azure Active Directory | Microsoft Docs
description: Utwórz niestandardowe role usługi Azure AD z zakresem zasobów dla zasobów Azure Active Directory.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae773bc1a4e1831dbe462149bb827c26b7e74b96
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722300"
---
# <a name="create-a-custom-role-and-assign-a-resource-scope-in-azure-active-directory"></a>Utwórz rolę niestandardową i przypisz zakres zasobów w Azure Active Directory

W tym artykule opisano sposób tworzenia nowych ról niestandardowych w usłudze Azure Active Directory (Azure AD). Role niestandardowe można tworzyć na karcie **role i Administratorzy** na stronie Przegląd usługi Azure AD lub na [stronie rejestracji aplikacji](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview). Rolę można przypisać do zakresu poziomu katalogu lub do zakresu tylko rejestracje aplikacji.

Aby uzyskać więcej informacji, zobacz [Omówienie ról niestandardowych](roles-custom-overview.md) dla podstaw ról niestandardowych.

## <a name="using-the-azure-ad-portal"></a>Korzystanie z portalu usługi Azure AD

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Utwórz nową rolę niestandardową, aby udzielić dostępu do zarządzania rejestracjami aplikacji

1. Zaloguj się do  [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com)przy użyciu uprawnień administrator ról uprzywilejowanych lub Administrator globalny w organizacji usługi Azure AD.
1. Wybierz pozycję **Azure Active Directory** > **role i Administratorzy** > **Nowa rola**niestandardowa.

   ![Tworzenie lub edytowanie ról na stronie role i Administratorzy](./media/roles-create-custom/new-custom-role.png)

1. Na karcie **podstawowe** Podaj nazwę i opis roli.

   ![Podaj nazwę i opis roli niestandardowej na karcie podstawowe](./media/roles-create-custom/basics-tab.png)

1. Aby wybrać uprawnienia wymagane do zarządzania poświadczeniami rejestracji aplikacji i właściwościami podstawowymi, takimi jak nazwa:
   1. Wprowadź ciąg "poświadczenia" na pasku wyszukiwania i wybierz `microsoft.directory/applications/credentials/update` uprawnienie.

      ![Wybierz uprawnienia roli niestandardowej na karcie uprawnienia](./media/roles-create-custom/permissions-tab.png)

   1. Wprowadź wartość "podstawowa" na pasku wyszukiwania, wybierz `microsoft.directory/applications/basic/update` uprawnienie, a następnie kliknij przycisk **dalej**.
1. Na karcie **Recenzja + tworzenie** Przejrzyj uprawnienia i wybierz pozycję **Utwórz**.

Rola niestandardowa zostanie wyświetlona na liście dostępnych ról do przypisania.

## <a name="assign-a-role-scoped-to-a-resource"></a>Przypisywanie roli do zasobu

Podobnie jak wbudowane role, role niestandardowe można przypisywać w domyślnym zakresie organizacji w celu udzielania dostępu przez wszystkie rejestracje aplikacji. Role niestandardowe można także przypisywać w zakresie obiektu. Dzięki temu można przyznać przydzielone uprawnienia do aktualizowania poświadczeń i podstawowych właściwości pojedynczej aplikacji bez konieczności tworzenia drugiej roli niestandardowej.

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu uprawnień dewelopera aplikacji w organizacji usługi Azure AD.
1. Wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz rejestrację aplikacji, do której chcesz udzielić dostępu do zarządzania. Może być konieczne wybranie opcji **wszystkie aplikacje** , aby wyświetlić pełną listę rejestracji aplikacji w organizacji usługi Azure AD.

    ![Wybierz rejestrację aplikacji jako zakres zasobów dla przypisania roli](./media/roles-create-custom/appreg-all-apps.png)

1. W obszarze Rejestracja aplikacji wybierz pozycję **role i Administratorzy**. Jeśli jeszcze tego nie zrobiono, instrukcje znajdują się w [poprzedniej procedurze](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

    Jeśli przypiszesz tę rolę z kontekstu otwartej rejestracji aplikacji, osoba przydzielona będzie mieć uprawnienia do rejestracji w ramach jednej aplikacji. Przypisana rola jest wyświetlana na liście w każdej rejestracji aplikacji. Ten model dostępu, w którym właściciel może być przypisany do określonych zasobów usługi Azure AD według ról, jest podobny do modelu używanego w [usłudze Azure RBAC](../../role-based-access-control/overview.md) na potrzeby kontroli dostępu do zasobów platformy Azure.

1. Wybierz rolę, aby otworzyć stronę **przypisania** .
1. Wybierz pozycję **Dodaj przypisanie** , aby dodać użytkownika. Użytkownikowi nie przyznano żadnych uprawnień do rejestracji aplikacji innej niż wybrana.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Tworzenie roli niestandardowej przy użyciu programu Azure AD PowerShell

### <a name="prepare-powershell"></a>Przygotowywanie programu PowerShell

Najpierw należy [pobrać moduł Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

Aby zainstalować moduł Azure AD PowerShell, użyj następujących poleceń:

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

### <a name="create-the-custom-role"></a>Utwórz rolę niestandardową

Utwórz nową rolę przy użyciu następującego skryptu programu PowerShell:

``` PowerShell
# Basic role information
$description = "Application Support Administrator"
$displayName = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/allProperties/read",
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customAdmin = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

## <a name="create-a-custom-role-using-microsoft-graph-api"></a>Tworzenie roli niestandardowej przy użyciu interfejsu API Microsoft Graph

1. Utwórz definicję roli.

    Żądanie HTTP, aby utworzyć niestandardową definicję roli.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Treść

    ``` HTTP
    {
    "description":"Can manage basic aspects of application registrations.",
    "displayName":"Application Support Administrator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "allowedResourceActions": 
            [
                "microsoft.directory/applications/basic/read",
                "microsoft.directory/applications/credentials/update"
            ]
        }
    ]
    }
    ```

1. Utwórz przypisanie roli.

    Żądanie HTTP, aby utworzyć niestandardową definicję roli.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions/roleAssignments
    ```

    Treść

    ``` HTTP
    {
    "principalId":"<GUID OF USER>",
    "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
    "resourceScope":["/<GUID OF APPLICATION REGISTRATION>"]
    }
    ```

## <a name="next-steps"></a>Następne kroki

- Podziel się z nami na [forum ról administracyjnych usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Aby uzyskać więcej informacji o rolach i przypisaniu roli administratora, zobacz [Przypisywanie ról administratorów](directory-assign-admin-roles.md).
- Domyślne uprawnienia użytkownika można znaleźć w [porównaniu z domyślnymi uprawnieniami gościa i użytkownika](../fundamentals/users-default-permissions.md).
