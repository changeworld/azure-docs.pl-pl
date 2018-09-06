---
title: Samouczek — udzielanie dostępu użytkownika za pomocą kontroli dostępu opartej na rolach i programu Azure PowerShell | Microsoft Docs
description: Aby przyznać użytkownikowi dostęp z możliwością wyświetlania wszystkiego w ramach subskrypcji i zarządzania wszystkim w grupie zasobów przy użyciu programu Azure PowerShell, należy użyć kontroli dostępu opartej na rolach (RBAC, Role Based Access Control).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/11/2018
ms.author: rolyon
ms.openlocfilehash: cac585b36c3b5969a18c941215b623443850cd4c
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301732"
---
# <a name="tutorial-grant-access-for-a-user-using-rbac-and-azure-powershell"></a>Samouczek: udzielanie dostępu użytkownikowi za pomocą kontroli dostępu opartej na rolach i programu Azure PowerShell

[Kontrola dostępu oparta na rolach (RBAC, Role Based Access Control)](overview.md) to sposób zarządzania dostępem do zasobów na platformie Azure. W tym samouczku przyznasz użytkownikowi dostęp z możliwością wyświetlania wszystkiego w ramach subskrypcji i zarządzania wszystkim w grupie zasobów przy użyciu programu Azure PowerShell.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Udzielanie dostępu użytkownikowi w innych zakresach
> * Tworzenie listy dostępu
> * Usuwanie dostępu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Uprawnienia do tworzenia użytkowników w usłudze Azure Active Directory (lub istniejący użytkownik)
- [Azure Cloud Shell](/azure/cloud-shell/quickstart-powershell)

## <a name="role-assignments"></a>Przypisania ról

Aby udzielić dostępu za pomocą kontroli dostępu opartej na rolach, tworzy się przypisanie roli. Przypisanie roli składa się z trzech elementów: podmiotu zabezpieczeń, definicji roli i zakresu. W tym samouczku utworzysz dwa przypisania ról:

| Podmiot zabezpieczeń | Definicja roli | Zakres |
| --- | --- | --- |
| Użytkownik<br>(Użytkownik samouczka kontroli dostępu opartej na rolach) | [Czytelnik](built-in-roles.md#reader) | Subskrypcja |
| Użytkownik<br>(Użytkownik samouczka kontroli dostępu opartej na rolach)| [Współautor](built-in-roles.md#contributor) | Grupa zasobów<br>(rbac-tutorial-resource-group) |

   ![Przypisania ról dla użytkownika](./media/tutorial-role-assignments-user-powershell/rbac-role-assignments-user.png)

## <a name="create-a-user"></a>Tworzenie użytkownika

Do przypisania roli potrzebny jest użytkownik, grupa lub jednostka usługi. Jeśli nie masz jeszcze użytkownika, możesz go utworzyć.

1. W usłudze Azure Cloud Shell utwórz hasło zgodne z wymaganiami dotyczącymi złożoności hasła.

    ```azurepowershell
    $PasswordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
    $PasswordProfile.Password = "Password"
    ```

1. Utwórz nowego użytkownika dla domeny za pomocą polecenia [New-AzureADUser](/powershell/module/azuread/new-azureaduser).

    ```azurepowershell
    New-AzureADUser -DisplayName "RBAC Tutorial User" -PasswordProfile $PasswordProfile `
      -UserPrincipalName "rbacuser@example.com" -AccountEnabled $true -MailNickName "rbacuser"
    ```
    
    ```Example
    ObjectId                             DisplayName        UserPrincipalName    UserType
    --------                             -----------        -----------------    --------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial User rbacuser@example.com Member
    ```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Za pomocą grupy zasobów można zademonstrować przypisywanie roli w zakresie grupy zasobów.

1. Pobierz listę lokalizacji regionów za pomocą polecenia [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation).

   ```azurepowershell
   Get-AzureRmLocation | select Location
   ```

1. Wybierz lokalizację w pobliżu i przypisz ją do zmiennej.

   ```azurepowershell
   $location = "westus"
   ```

1. Utwórz nową grupę zasobów za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).

   ```azurepowershell
   New-AzureRmResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>Udzielanie dostępu

Aby udzielić dostępu użytkownikowi, przypisz rolę za pomocą polecenia [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment). Należy określić podmiot zabezpieczeń, definicję roli i zakres.

1. Pobierz identyfikator subskrypcji za pomocą polecenia [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

    ```azurepowershell
    Get-AzureRmSubscription
    ```

    ```Example
    Name     : Pay-As-You-Go
    Id       : 00000000-0000-0000-0000-000000000000
    TenantId : 22222222-2222-2222-2222-222222222222
    State    : Enabled
    ```

1. Zapisz zakres subskrypcji w zmiennej.

    ```azurepowershell
    $subScope = "/subscriptions/00000000-0000-0000-0000-000000000000"
    ```

1. Przypisz użytkownikowi rolę [Czytelnik](built-in-roles.md#reader) w zakresie subskrypcji.

    ```azurepowershell
    New-AzureRmRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

1. Przypisz użytkownikowi rolę [Współautor](built-in-roles.md#contributor) w zakresie grupy zasobów.

    ```azurepowershell
    New-AzureRmRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

## <a name="list-access"></a>Tworzenie listy dostępu

1. Aby zweryfikować dostęp do subskrypcji, wyświetl listę przypisań ról za pomocą polecenia [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

    ```azurepowershell
    Get-AzureRmRoleAssignment -SignInName rbacuser@example.com -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

    W danych wyjściowych widać, że do użytkownika samouczka kontroli dostępu opartej na rolach przypisano rolę Czytelnik w zakresie subskrypcji.

1. Aby zweryfikować dostęp do grupy zasobów, wyświetl listę przypisań ról za pomocą polecenia [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

    ```azurepowershell
    Get-AzureRmRoleAssignment -SignInName rbacuser@example.com -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

    W danych wyjściowych widać, że do użytkownika samouczka kontroli dostępu opartej na rolach przypisano zarówno rolę Współautor, jak i rolę Czytelnik. Rola Współautor jest przypisana w zakresie rbac-tutorial-resource-group, a rola Czytelnik jest dziedziczona w zakresie subskrypcji.

## <a name="optional-list-access-using-the-azure-portal"></a>Opcjonalnie: tworzenie listy dostępu przy użyciu witryny Azure Portal

1. Aby sprawdzić, jak wyglądają przypisania ról w witrynie Azure Portal, należy wyświetlić blok **Kontrola dostępu (IAM)** dla subskrypcji.

    ![Przypisania ról dla użytkownika w zakresie subskrypcji](./media/tutorial-role-assignments-user-powershell/role-assignments-subscription-user.png)

1. Wyświetl blok **Kontrola dostępu (IAM)** dla grupy zasobów.

    ![Przypisania ról dla użytkownika w zakresie grupy zasobów](./media/tutorial-role-assignments-user-powershell/role-assignments-resource-group-user.png)

## <a name="remove-access"></a>Usuwanie dostępu

Aby usunąć dostęp dla użytkowników, grup i aplikacji, usuń przypisanie roli za pomocą polecenia [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment).

1. Za pomocą następującego polecenia usuń przypisanie roli Współautor dla użytkownika w zakresie grupy zasobów.

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. Za pomocą następującego polecenia usuń przypisanie roli Czytelnik dla użytkownika w zakresie subskrypcji.

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby wyczyścić zasoby utworzone w tym samouczku, usuń grupę zasobów i użytkownika.

1. Usuń grupę zasobów za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

    ```azurepowershell
    Remove-AzureRmResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. Gdy zostanie wyświetlony monit o potwierdzenie, wpisz **Y** (T). Usunięcie zajmie kilka sekund.

1. Usuń użytkownika za pomocą polecenia [Remove-AzureADUser](/powershell/module/azuread/remove-azureaduser).

    ```azurepowershell
    Remove-AzureADUser -ObjectId "rbacuser@example.com"
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zarządzanie dostępem przy użyciu kontroli RBAC i programu PowerShell](role-assignments-powershell.md)
