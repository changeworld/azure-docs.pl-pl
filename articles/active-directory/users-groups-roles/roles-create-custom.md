---
title: Tworzenie ról niestandardowych w kontroli dostępu opartej na rolach usługi Azure AD | Dokumenty firmy Microsoft
description: Tworzenie i przypisywanie niestandardowych ról usługi Azure AD z zakresem zasobów usługi Azure w zasobach usługi Azure Active Directory.
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
ms.openlocfilehash: c2cb19c82f8c19bf87eeef755adb5756b2452512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025272"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Tworzenie i przypisywanie roli niestandardowej w usłudze Azure Active Directory

W tym artykule opisano sposób tworzenia nowych ról niestandardowych w usłudze Azure Active Directory (Azure AD). Aby zapoznać się z podstawami ról niestandardowych, zobacz [omówienie ról niestandardowych](roles-custom-overview.md). Rolę można przypisać tylko w zakresie na poziomie katalogu lub w zakresie zasobu rejestracji aplikacji.

Role niestandardowe można tworzyć na karcie [Role i administratorzy](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) na stronie przegląd usługi Azure AD.

## <a name="create-a-role-in-the-azure-portal"></a>Tworzenie roli w witrynie Azure portal

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Tworzenie nowej roli niestandardowej w celu przyznania dostępu do zarządzania rejestracjami aplikacji

1. Zaloguj się do [centrum](https://aad.portal.azure.com) administracyjnego usługi Azure AD przy za pomocą uprawnień administratora ról uprzywilejowanych lub administratora globalnego w organizacji usługi Azure AD.
1. Wybierz **role usługi Azure Active Directory** > i**administratorów** > **Nowa rola niestandardowa**.

   ![Tworzenie lub edytowanie ról na stronie Role i administratorzy](./media/roles-create-custom/new-custom-role.png)

1. Na karcie **Podstawy** podaj nazwę i opis roli, a następnie kliknij przycisk **Dalej**.

   ![podaj nazwę i opis roli niestandardowej na karcie Podstawy](./media/roles-create-custom/basics-tab.png)

1. Na karcie **Uprawnienia** wybierz uprawnienia niezbędne do zarządzania podstawowymi właściwościami i właściwościami poświadczeń rejestracji aplikacji. Aby uzyskać szczegółowy opis każdego uprawnienia, zobacz [Podtypy i uprawnienia rejestracji aplikacji w usłudze Azure Active Directory](./roles-custom-available-permissions.md).
   1. Najpierw wprowadź "poświadczenia" na pasku wyszukiwania `microsoft.directory/applications/credentials/update` i wybierz uprawnienie.

      ![Wybieranie uprawnień do roli niestandardowej na karcie Uprawnienia](./media/roles-create-custom/permissions-tab.png)

   1. Następnie wprowadź "podstawowe" na pasku `microsoft.directory/applications/basic/update` wyszukiwania, wybierz uprawnienie, a następnie kliknij przycisk **Dalej**.
1. Na karcie **Recenzja + tworzenie** przejrzyj uprawnienia i wybierz pozycję **Utwórz**.

Twoja rola niestandardowa pojawi się na liście dostępnych ról do przypisania.

## <a name="create-a-role-using-powershell"></a>Tworzenie roli przy użyciu programu PowerShell

### <a name="prepare-powershell"></a>Przygotowanie programu PowerShell

Najpierw należy [pobrać moduł programu PowerShell usługi Azure AD Preview](https://www.powershellgallery.com/packages/AzureADPreview).

Aby zainstalować moduł programu Azure AD PowerShell, należy użyć następujących poleceń:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Aby sprawdzić, czy moduł jest gotowy do użycia, użyj następującego polecenia:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.2.31     azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>Tworzenie roli niestandardowej

Utwórz nową rolę przy użyciu następującego skryptu programu PowerShell:

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Przypisywanie roli niestandardowej przy użyciu programu Azure AD PowerShell

Przypisz rolę przy użyciu poniższego skryptu programu PowerShell:

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

## <a name="create-a-role-with-graph-api"></a>Tworzenie roli za pomocą interfejsu API wykresu

1. Utwórz definicję roli.

    Żądanie HTTP, aby utworzyć niestandardową definicję roli.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Treść

    ``` HTTP
   {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
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
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Treść

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>Przypisywanie roli niestandardowej o zakresie do zasobu

Podobnie jak role wbudowane, role niestandardowe są domyślnie przypisywane w domyślnym zakresie dla całej organizacji w celu przyznania uprawnień dostępu do wszystkich rejestracji aplikacji w organizacji. Jednak w przeciwieństwie do ról wbudowanych role niestandardowe można również przypisać w zakresie pojedynczego zasobu usługi Azure AD. Dzięki temu można udzielić użytkownikowi uprawnienia do aktualizowania poświadczeń i podstawowych właściwości pojedynczej aplikacji bez konieczności tworzenia drugiej roli niestandardowej.

1. Zaloguj się do [centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy przy tym przyuczuje uprawnienia dewelopera aplikacji w organizacji usługi Azure AD.
1. Wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz rejestrację aplikacji, do której udzielasz dostępu do zarządzania. Może być trzeba wybrać **wszystkie aplikacje,** aby wyświetlić pełną listę rejestracji aplikacji w organizacji usługi Azure AD.

    ![Wybieranie rejestracji aplikacji jako zakresu zasobu dla przypisania roli](./media/roles-create-custom/appreg-all-apps.png)

1. W rejestracji aplikacji wybierz pozycję **Role i administratorzy**. Jeśli nie został jeszcze utworzony, instrukcje znajdują się w [poprzedniej procedurze](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Wybierz rolę, aby otworzyć stronę **Przydziały.**
1. Wybierz **pozycję Dodaj przypisanie,** aby dodać użytkownika. Użytkownik otrzyma wszelkie uprawnienia tylko do wybranej rejestracji aplikacji.

## <a name="next-steps"></a>Następne kroki

- Zapraszam do dzielenia się z nami na [forum ról administracyjnych usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Aby uzyskać więcej informacji o rolach i przypisywaniu ról administratora, zobacz [Przypisywanie ról administratora](directory-assign-admin-roles.md).
- Aby uzyskać domyślne uprawnienia użytkownika, zobacz [porównanie domyślnych uprawnień gościa i użytkownika.](../fundamentals/users-default-permissions.md)
