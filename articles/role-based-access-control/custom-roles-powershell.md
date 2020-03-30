---
title: Tworzenie lub aktualizowanie ról niestandardowych zasobów platformy Azure za pomocą programu Azure PowerShell
description: Dowiedz się, jak wymieniać, tworzyć, aktualizować lub usuwać role niestandardowe za pomocą kontroli dostępu opartej na rolach (RBAC) dla zasobów platformy Azure przy użyciu programu Azure PowerShell.
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 3c72e04ff7a08fecc2ef352a5879898c4c6d41c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062276"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-azure-powershell"></a>Tworzenie lub aktualizowanie ról niestandardowych zasobów platformy Azure przy użyciu programu Azure PowerShell

> [!IMPORTANT]
> Dodawanie grupy zarządzania do `AssignableScopes` jest obecnie w wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli [wbudowane role dla zasobów platformy Azure](built-in-roles.md) nie spełniają potrzeb Twojej organizacji, możesz tworzyć własne role niestandardowe. W tym artykule opisano sposób listy, tworzenia, aktualizowania lub usuwania ról niestandardowych przy użyciu programu Azure PowerShell.

Aby uzyskać samouczek krok po kroku na temat tworzenia roli niestandardowej, zobacz [Samouczek: Tworzenie roli niestandardowej dla zasobów platformy Azure przy użyciu programu Azure PowerShell](tutorial-custom-role-powershell.md).

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć role niestandardowe, potrzebujesz:

- Uprawnienia do tworzenia ról niestandardowych, takie jak [Właściciel](built-in-roles.md#owner) lub [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator)
- Usługa [Azure Cloud Shell](../cloud-shell/overview.md) lub program [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-custom-roles"></a>Wyświetlanie ról niestandardowych

Aby wyświetlić listę ról dostępnych do przypisania w zakresie, należy użyć polecenia [Get-AzRoleDefinition.](/powershell/module/az.resources/get-azroledefinition) Poniższy przykład zawiera listę wszystkich ról, które są dostępne dla przypisania w wybranej subskrypcji.

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

Poniższy przykład zawiera listę tylko role niestandardowe, które są dostępne dla przypisania w wybranej subskrypcji.

```azurepowershell
Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Jeśli wybrana subskrypcja nie `AssignableScopes` znajduje się w roli, rola niestandardowa nie zostanie wyświetlona.

## <a name="list-a-custom-role-definition"></a>Wyświetlanie listy definicji roli niestandardowej

Aby wyświetlić listę niestandardowej definicji roli, należy użyć pliku [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition). Jest to to samo polecenie, które jest używane dla wbudowanej roli.

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | ConvertTo-Json

{
  "Name": "Virtual Machine Operator",
  "Id": "00000000-0000-0000-0000-000000000000",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Poniższy przykład zawiera tylko akcje roli:

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Operator").Actions

"Microsoft.Storage/*/read",
"Microsoft.Network/*/read",
"Microsoft.Compute/*/read",
"Microsoft.Compute/virtualMachines/start/action",
"Microsoft.Compute/virtualMachines/restart/action",
"Microsoft.Authorization/*/read",
"Microsoft.ResourceHealth/availabilityStatuses/read",
"Microsoft.Resources/subscriptions/resourceGroups/read",
"Microsoft.Insights/alertRules/*",
"Microsoft.Insights/diagnosticSettings/*",
"Microsoft.Support/*"
```

## <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Aby utworzyć rolę niestandardową, należy użyć polecenia [New-AzRoleDefinition.](/powershell/module/az.resources/new-azroledefinition) Istnieją dwie metody struktury roli, przy `PSRoleDefinition` użyciu obiektu lub szablonu JSON. 

### <a name="get-operations-for-a-resource-provider"></a>Pobierz operacje dla dostawcy zasobów

Podczas tworzenia ról niestandardowych, ważne jest, aby znać wszystkie możliwe operacje od dostawców zasobów.
Można wyświetlić listę [operacji dostawcy zasobów](resource-provider-operations.md) lub można użyć [get-AzProviderOperation polecenia,](/powershell/module/az.resources/get-azprovideroperation) aby uzyskać te informacje.
Na przykład, jeśli chcesz sprawdzić wszystkie dostępne operacje dla maszyn wirtualnych, użyj tego polecenia:

```azurepowershell
Get-AzProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>Tworzenie roli niestandardowej za pomocą obiektu PSRoleDefinition

Korzystając z programu PowerShell do utworzenia roli niestandardowej, można użyć jednej z [wbudowanych ról](built-in-roles.md) jako punktu wyjścia lub można rozpocząć od zera. Pierwszy przykład w tej sekcji rozpoczyna się od wbudowanej roli, a następnie dostosowuje ją z większą liczą uprawnienia. Edytuj atrybuty, `Actions`aby `NotActions`dodać `AssignableScopes` program , lub który chcesz, a następnie zapisz zmiany jako nową rolę.

Poniższy przykład rozpoczyna się od wbudowanej roli [współautora maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) w celu utworzenia roli niestandardowej o nazwie *Operator maszyny wirtualnej*. Nowa rola udziela dostępu do wszystkich operacji odczytu *microsoft.compute*, *Microsoft.Storage*i *Microsoft.Network* dostawców zasobów i udziela dostępu do uruchamiania, ponownego uruchamiania i monitorowania maszyn wirtualnych. Rola niestandardowa może być używana w dwóch subskrypcjach.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.ResourceHealth/availabilityStatuses/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzRoleDefinition -Role $role
```

W poniższym przykładzie pokazano inny sposób tworzenia roli niestandardowej *operatora maszyny wirtualnej.* Rozpoczyna się od utworzenia `PSRoleDefinition` nowego obiektu. Operacje akcji są określone `perms` w zmiennej `Actions` i ustawione na właściwość. Właściwość `NotActions` jest ustawiana `NotActions` przez odczyt z wbudowanej roli [współautora maszyny wirtualnej.](built-in-roles.md#virtual-machine-contributor) Ponieważ [współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) nie ma żadnych `NotActions`, ten wiersz nie jest wymagany, ale pokazuje, jak informacje mogą być pobierane z innej roli.

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read'
$perms += 'Microsoft.ResourceHealth/availabilityStatuses/read'
$perms += 'Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>Tworzenie roli niestandardowej za pomocą szablonu JSON

Szablon JSON może służyć jako definicja źródła dla roli niestandardowej. Poniższy przykład tworzy rolę niestandardową, która umożliwia dostęp do odczytu do magazynu i zasobów obliczeniowych, dostęp do pomocy technicznej i dodaje tę rolę do dwóch subskrypcji. Utwórz nowy `C:\CustomRoles\customrole1.json` plik w poniższym przykładzie. Identyfikator powinien być ustawiony `null` na początkowe tworzenie roli, ponieważ nowy identyfikator jest generowany automatycznie. 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Aby dodać rolę do subskrypcji, uruchom następujące polecenie programu PowerShell:

```azurepowershell
New-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>Aktualizacja roli niestandardowej

Podobnie jak w przypadku tworzenia roli niestandardowej, można zmodyfikować istniejącą rolę niestandardową przy użyciu `PSRoleDefinition` obiektu lub szablonu JSON.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>Aktualizowanie roli niestandardowej za pomocą obiektu PSRoleDefinition

Aby zmodyfikować rolę niestandardową, najpierw użyj polecenia [Get-AzRoleDefinition,](/powershell/module/az.resources/get-azroledefinition) aby pobrać definicję roli. Po drugie, należy wprowadzić żądane zmiany w definicji roli. Na koniec użyj polecenia [Set-AzRoleDefinition,](/powershell/module/az.resources/set-azroledefinition) aby zapisać zmodyfikowaną definicję roli.

W poniższym `Microsoft.Insights/diagnosticSettings/*` przykładzie dodano operację do roli niestandardowej *operatora maszyny wirtualnej.*

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

W poniższym przykładzie dodano subskrypcję platformy Azure do zakresów przypisywalnych roli niestandardowej *operatora maszyny wirtualnej.*

```azurepowershell
Get-AzSubscription -SubscriptionName Production3

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

W poniższym przykładzie `AssignableScopes` dodano grupę zarządzania do roli niestandardowej *operatora maszyny wirtualnej.* Dodawanie grupy zarządzania do `AssignableScopes` jest obecnie w wersji zapoznawczej.

```azurepowershell
Get-AzManagementGroup

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/{groupId1}")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzManagementGroup

Id          : /providers/Microsoft.Management/managementGroups/marketing-group
Type        : /providers/Microsoft.Management/managementGroups
Name        : marketing-group
TenantId    : 99999999-9999-9999-9999-999999999999
DisplayName : Marketing group

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/marketing-group")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222,
                   /providers/Microsoft.Management/managementGroups/marketing-group}
```

### <a name="update-a-custom-role-with-a-json-template"></a>Aktualizowanie roli niestandardowej za pomocą szablonu JSON

Za pomocą poprzedniego szablonu JSON można łatwo zmodyfikować istniejącą rolę niestandardową, aby dodać lub usunąć akcje. Zaktualizuj szablon JSON i dodaj akcję odczytu dla sieci, jak pokazano w poniższym przykładzie. Definicje wymienione w szablonie nie są kumulacyjnie stosowane do istniejącej definicji, co oznacza, że rola jest wyświetlana dokładnie tak, jak określono w szablonie. Należy również zaktualizować pole identyfikatora o identyfikator roli. Jeśli nie masz pewności, jaka jest ta wartość, możesz użyć polecenia cmdlet [Get-AzRoleDefinition,](/powershell/module/az.resources/get-azroledefinition) aby uzyskać te informacje.

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Aby zaktualizować istniejącą rolę, uruchom następujące polecenie programu PowerShell:

```azurepowershell
Set-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Usuwanie roli niestandardowej

Aby usunąć rolę niestandardową, użyj polecenia [Usuń-AzRoleDefinition.](/powershell/module/az.resources/remove-azroledefinition)

W poniższym przykładzie usuwa rolę niestandardową *operatora maszyny wirtualnej.*

```azurepowershell
Get-AzRoleDefinition "Virtual Machine Operator"
Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Tworzenie roli niestandardowej dla zasobów platformy Azure przy użyciu programu Azure PowerShell](tutorial-custom-role-powershell.md)
- [Niestandardowe role dla zasobów platformy Azure](custom-roles.md)
- [Operacje dostawcy zasobów usługi Azure Resource Manager](resource-provider-operations.md)
