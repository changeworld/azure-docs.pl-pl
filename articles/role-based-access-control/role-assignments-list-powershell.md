---
title: Lista przypisań ról przy użyciu usługi Azure RBAC i azure powershell
description: Dowiedz się, jak określić, do jakich zasobów użytkownicy, grupy, jednostki usług lub tożsamości zarządzane mają dostęp do przy użyciu kontroli dostępu opartej na rolach platformy Azure (RBAC) i programu Azure PowerShell.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0ec3153e5b1bfbe04a079d1cfc44e8e8709784d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931153"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-powershell"></a>Lista przypisań ról przy użyciu usługi Azure RBAC i azure powershell

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]W tym artykule opisano sposób listy przypisań ról przy użyciu programu Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

> [!NOTE]
> Jeśli twoja organizacja zleciła funkcje zarządzania na zewnątrz dostawcy usług, który korzysta z [usługi Azure zarządzanie zasobami delegowanymi,](../lighthouse/concepts/azure-delegated-resource-management.md)przypisania ról autoryzowane przez tego dostawcę usług nie będą wyświetlane w tym miejscu.

## <a name="prerequisites"></a>Wymagania wstępne

- [Program PowerShell w usłudze Azure Cloud Shell](/azure/cloud-shell/overview) lub [usłudze Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-role-assignments-for-the-current-subscription"></a>Lista przypisań ról dla bieżącej subskrypcji

Najprostszym sposobem uzyskania listy wszystkich przypisań ról w bieżącej subskrypcji (w tym dziedziczonych przypisań ról z grup głównych i zarządzania) jest użycie [funkcji Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) bez żadnych parametrów.

```azurepowershell
Get-AzRoleAssignment
```

```Example
PS C:\> Get-AzRoleAssignment

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Alain
SignInName         : alain@example.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Marketing
SignInName         :
RoleDefinitionName : Contributor
RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : Group
CanDelegate        : False

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Tworzenie listy przypisań ról dla subskrypcji

Aby wyświetlić listę wszystkich przypisań ról w zakresie subskrypcji, należy użyć funkcji [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). Aby uzyskać identyfikator subskrypcji, można go znaleźć w bloku **Subskrypcje** w witrynie Azure portal lub można użyć [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="list-role-assignments-for-a-user"></a>Tworzenie listy przypisań ról dla użytkownika

Aby wyświetlić listę wszystkich ról przypisanych do określonego użytkownika, należy użyć funkcji [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

Aby wyświetlić listę wszystkich ról przypisanych do określonego użytkownika oraz ról przypisanych do grup, do których należy użytkownik, należy użyć funkcji [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

## <a name="list-role-assignments-for-a-resource-group"></a>Tworzenie listy przypisań ról dla grupy zasobów

Aby wyświetlić listę wszystkich przypisań ról w zakresie grupy zasobów, należy użyć funkcji [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

## <a name="list-role-assignments-for-a-management-group"></a>Wyświetlanie przypisań ról dla grupy zarządzania

Aby wyświetlić listę wszystkich przypisań ról w zakresie grupy zarządzania, należy użyć funkcji [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). Aby uzyskać identyfikator grupy zarządzania, można go znaleźć w bloku **Grupy zarządzania** w witrynie Azure portal lub można użyć [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>Wyświetlanie przypisań ról dla administratora usług klasycznych i współadministratorów

Aby wyświetlić listę przypisań ról dla administratora subskrypcji klasycznej i współadministratorów, użyj [funkcji Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Wyświetlanie przypisań ról dla tożsamości zarządzanej

1. Pobierz identyfikator obiektu tożsamości zarządzanej przypisanej do systemu lub przypisanej przez użytkownika. 

    Aby uzyskać identyfikator obiektu tożsamości zarządzanej przypisanej przez użytkownika, można użyć [programu Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).

    ```azurepowershell
    Get-AzADServicePrincipal -DisplayNameBeginsWith "<name> or <vmname>"
    ```

1. Aby wyświetlić listę przypisań ról, należy użyć funkcji [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId <objectid>
    ```

## <a name="next-steps"></a>Następne kroki

- [Dodawanie lub usuwanie przypisań ról przy użyciu usługi Azure RBAC i azure powershell](role-assignments-powershell.md)
