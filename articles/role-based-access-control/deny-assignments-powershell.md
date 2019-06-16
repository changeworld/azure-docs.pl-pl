---
title: Lista Odmów przydziały dla zasobów platformy Azure przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyświetlić listę użytkowników, grup, nazw głównych usług i zarządzanych tożsamości, na których nastąpiła odmowa dostępu do akcji określony zasób platformy Azure, w szczególności zakresów przy użyciu programu Azure PowerShell.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: c1ea26fdb4d60262f89ea6ab0f87220a08c01e68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110487"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>Lista Odmów przydziały dla zasobów platformy Azure przy użyciu programu Azure PowerShell

[Odmów przypisania](deny-assignments.md) Zablokuj użytkownikom możliwość wykonywania akcji na określony zasób platformy Azure, nawet wtedy, gdy przypisanie roli przyznaje im dostęp. W tym artykule opisano sposób wyświetlenia listy Odmów przypisania przy użyciu programu Azure PowerShell.

> [!NOTE]
> Nie można bezpośrednio utworzyć własne Odmów przypisania. Aby uzyskać informacje na temat nie zezwoli na przydziały są tworzone, zobacz [Odmów przypisania](deny-assignments.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać informacje o przypisaniu Odmów, musisz mieć:

- `Microsoft.Authorization/denyAssignments/read` uprawnienie, który jest dostępny w większości [wbudowane role zasobów platformy Azure](built-in-roles.md)
- [Program PowerShell w usłudze Azure Cloud Shell](/azure/cloud-shell/overview) lub [programu Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Lista Odmów przypisania

### <a name="list-all-deny-assignments"></a>Lista wszystkich Odmów przypisania

Aby wyświetlić listę wszystkich Odmów przypisania w ramach bieżącej subskrypcji, użyj [Get AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Lista Odmów przypisania w zakresie grupy zasobów

Aby wyświetlić listę wszystkich Odmów przypisania w zakresie grupy zasobów, użyj [Get AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Lista Odmów przypisania w zakresie subskrypcji

Aby wyświetlić listę wszystkich Odmów przypisania w zakresie subskrypcji, użyj [Get AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment). Aby uzyskać identyfikator subskrypcji, możesz go znaleźć na **subskrypcje** bloku w witrynie Azure portal albo można używać [Get AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Kolejne kroki

- [Zrozumienie Odmów przydziały dla zasobów platformy Azure](deny-assignments.md)
- [Lista Odmów przydziały dla zasobów platformy Azure przy użyciu witryny Azure portal](deny-assignments-portal.md)
- [Lista Odmów przydziały dla zasobów platformy Azure przy użyciu interfejsu API REST](deny-assignments-rest.md)