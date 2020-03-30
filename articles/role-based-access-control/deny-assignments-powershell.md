---
title: Lista odmów przydziałów dla zasobów platformy Azure za pomocą programu Azure PowerShell
description: Dowiedz się, jak wyświetlić listę użytkowników, grup, podmiotów usługi i tożsamości zarządzanych, którym odmówiono dostępu do określonych akcji zasobów platformy Azure w określonych zakresach przy użyciu programu Azure PowerShell.
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
ms.openlocfilehash: 5ba18b89bd37dbd55350321c503e37ab0590ab87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137391"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>Lista odmów przydziałów dla zasobów platformy Azure przy użyciu programu Azure PowerShell

[Odmów przydziałów](deny-assignments.md) zablokować użytkownikom wykonywanie określonych akcji zasobów platformy Azure, nawet jeśli przypisanie roli udziela im dostępu. W tym artykule opisano sposób listy odmów przydziałów przy użyciu programu Azure PowerShell.

> [!NOTE]
> Nie można bezpośrednio tworzyć własnych przypisań odmowy. Aby uzyskać informacje o sposobie tworzenia przypisań odmowy, zobacz [Odrzucanie przypisań](deny-assignments.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać informacje o przypisaniu odmowy, musisz mieć:

- `Microsoft.Authorization/denyAssignments/read`uprawnienie, które jest zawarte w większości [wbudowanych ról zasobów platformy Azure](built-in-roles.md)
- [Program PowerShell w usłudze Azure Cloud Shell](/azure/cloud-shell/overview) lub [usłudze Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Lista przypisań odmowy

### <a name="list-all-deny-assignments"></a>Wyświetlanie listy wszystkich przypisań odmów

Aby wyświetlić listę wszystkich przypisań odmowy dla bieżącej subskrypcji, należy użyć programu [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

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

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Lista przypisań odmowy w zakresie grupy zasobów

Aby wyświetlić listę wszystkich przypisań odmów w zakresie grupy zasobów, należy użyć funkcji [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

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

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Lista przypisań odmowy w zakresie subskrypcji

Aby wyświetlić listę wszystkich przypisań odmowy w zakresie subskrypcji, należy użyć [programu Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment). Aby uzyskać identyfikator subskrypcji, można go znaleźć w bloku **Subskrypcje** w witrynie Azure portal lub można użyć [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Następne kroki

- [Opis przypisań odmów dla zasobów platformy Azure](deny-assignments.md)
- [Lista odmów przydziałów dla zasobów platformy Azure przy użyciu witryny Azure portal](deny-assignments-portal.md)
- [Lista odmów przydziałów dla zasobów platformy Azure przy użyciu interfejsu API REST](deny-assignments-rest.md)