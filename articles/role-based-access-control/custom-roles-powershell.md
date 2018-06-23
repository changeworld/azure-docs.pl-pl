---
title: Tworzenie niestandardowych ról przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia niestandardowych ról dla kontroli dostępu opartej na rolach (RBAC) przy użyciu programu Azure PowerShell. W tym jak listy, tworzenie, aktualizowanie i usuwanie ról niestandardowych.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6205198d3f9c4ec5bfa2311014cf9b90dcade6dc
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320520"
---
# <a name="create-custom-roles-using-azure-powershell"></a>Tworzenie niestandardowych ról przy użyciu programu Azure PowerShell

Jeśli [wbudowane role](built-in-roles.md) nie spełnienia specyficznych potrzeb Twojej organizacji, możesz utworzyć własne niestandardowe role. W tym artykule opisano sposób tworzenia i zarządzania nimi przy użyciu programu Azure PowerShell role niestandardowe.

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć role niestandardowe, należy:

- Uprawnienia do tworzenia ról niestandardowych, takich jak [właściciela](built-in-roles.md#owner) lub [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator)
- [Program Azure PowerShell](/powershell/azure/install-azurerm-ps) zainstalowane lokalnie

## <a name="list-custom-roles"></a>Lista ról niestandardowych

Aby wyświetlić listę ról, które są dostępne do przypisania w zakresie, należy użyć [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) polecenia. Poniższy przykład zawiera listę wszystkich ról, które są dostępne do przypisania w wybranej subskrypcji.

```azurepowershell
Get-AzureRmRoleDefinition | FT Name, IsCustom
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

Poniższy przykład zawiera tylko role niestandardowe, które są dostępne do przypisania w wybranej subskrypcji.

```azurepowershell
Get-AzureRmRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Jeśli wybrana subskrypcja nie ma `AssignableScopes` roli, rola niestandardowa nie będą wyświetlane.

## <a name="create-a-custom-role"></a>Tworzenie niestandardowej roli zabezpieczeń

Aby utworzyć niestandardową rolę, użyj [AzureRmRoleDefinition nowy](/powershell/module/azurerm.resources/new-azurermroledefinition) polecenia. Istnieją dwie metody struktury roli, przy użyciu `PSRoleDefinition` obiektu lub szablonu JSON. 

### <a name="get-operations-for-a-resource-provider"></a>Pobierz operacje dla dostawcy zasobów

Utworzenie ról niestandardowych jest musisz znać wszystkie możliwe operacje z dostawców zasobów.
Można wyświetlić listę [operacji dostawcy zasobów](resource-provider-operations.md) lub użyć [Get AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) polecenie, aby uzyskać te informacje.
Na przykład jeśli chcesz sprawdzić wszystkie operacje dostępne dla maszyn wirtualnych, należy użyć tego polecenia:

```azurepowershell
Get-AzureRMProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>Tworzenie niestandardowej roli zabezpieczeń z obiektem PSRoleDefinition

Przy użyciu programu PowerShell do utworzenia niestandardowej roli zabezpieczeń, można użyć jednej z [wbudowane role](built-in-roles.md) jako punkt początkowy lub można utworzyć od podstaw. Pierwszym przykładzie w tej sekcji rozpoczyna się od wbudowanej roli i dostosowuje go z większe uprawnienia. Edycja atrybutów do dodania `Actions`, `NotActions`, lub `AssignableScopes` , a następnie zapisz zmiany jako nową rolę.

Poniższy przykład rozpoczyna się od [Współautor·maszyny·wirtualnej](built-in-roles.md#virtual-machine-contributor) wbudowana Rola do utworzenia niestandardowej roli zabezpieczeń o nazwie *Operator maszyny wirtualnej*. Nowa rola nieograniczony dostęp do wszystkich operacji odczytu z *Microsoft.Compute*, *Microsoft.Storage*, i *Microsoft.Network* zasobów dostawców i udziela dostępu do uruchomienia , uruchom ponownie, a monitorowania maszyn wirtualnych. Tworzona rola niestandardowa może służyć w dwóch subskrypcji.

```azurepowershell
$role = Get-AzureRmRoleDefinition "Virtual Machine Contributor"
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
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzureRmRoleDefinition -Role $role
```

W poniższym przykładzie przedstawiono innym sposobem tworzenia *Operator maszyny wirtualnej* niestandardowej roli zabezpieczeń. Rozpocznie się tworzenie nowej `PSRoleDefinition` obiektu. Operacje akcji są określone w `perms` zmiennej i należy ustawić `Actions` właściwości. `NotActions` Właściwość jest ustawiona, odczytując `NotActions` z [Współautor·maszyny·wirtualnej](built-in-roles.md#virtual-machine-contributor) wbudowanej roli. Ponieważ [Współautor·maszyny·wirtualnej](built-in-roles.md#virtual-machine-contributor) nie ma żadnych `NotActions`, ten wiersz nie jest wymagana, ale pokazuje, jak można pobrać informacji z inną rolą.

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read','Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzureRmRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzureRmRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>Utworzyć niestandardową rolę za pomocą szablonu JSON

Szablonu JSON może służyć jako definicji źródła niestandardowego roli. Poniższy przykład tworzy niestandardową rolę, która umożliwia dostęp do odczytu do zasobów magazynu i zasobów obliczeniowych, dostęp do pomocy technicznej, i dodaje do dwóch subskrypcji tej roli. Utwórz nowy plik `C:\CustomRoles\customrole1.json` z następującym przykładzie. Identyfikator powinien być ustawiony na `null` na tworzenie początkowej roli jako nowy identyfikator jest generowany automatycznie. 

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
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Aby dodać rolę do subskrypcji, uruchom następujące polecenie programu PowerShell:

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>Aktualizacja niestandardowej roli zabezpieczeń

Podobnie jak tworzenie niestandardowej roli zabezpieczeń, można zmodyfikować istniejącą rolę niestandardowych za pomocą `PSRoleDefinition` obiektu lub szablonu JSON.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>Aktualizacja niestandardowej roli zabezpieczeń z obiektem PSRoleDefinition

Aby zmodyfikować niestandardową rolę, najpierw użyj [Get AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) polecenie, aby pobrać definicji roli. Po drugie wprowadź żądane zmiany do definicji roli. Na koniec użyj [AzureRmRoleDefinition zestaw](/powershell/module/azurerm.resources/set-azurermroledefinition) polecenie, aby zapisać definicji roli zmodyfikowane.

W poniższym przykładzie dodano `Microsoft.Insights/diagnosticSettings/*` operacji *Operator maszyny wirtualnej* niestandardowej roli zabezpieczeń.

```azurepowershell
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzureRmRoleDefinition -Role $role

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

W poniższym przykładzie dodano subskrypcji platformy Azure do zakresów można przypisać z *Operator maszyny wirtualnej* niestandardowej roli zabezpieczeń.

```azurepowershell
Get-AzureRmSubscription -SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzureRmRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzureRmSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzureRmRoleDefinition -Role $role

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

### <a name="update-a-custom-role-with-a-json-template"></a>Aktualizacja niestandardowej roli zabezpieczeń przy użyciu szablonu JSON

Przy użyciu poprzednich szablonu JSON, można łatwo zmodyfikować istniejącą rolę niestandardowych można dodać ani usunąć działań. Zaktualizuj szablonu JSON, a następnie dodaj akcję odczytu dotyczące sieci, jak pokazano w poniższym przykładzie. Łącznie definicje wymienione w szablonie nie są stosowane do istniejącej definicji, co oznacza dokładnie, jak określone w szablonie pojawia się roli. Należy również zaktualizować w polu identyfikatora identyfikator roli. Jeśli nie masz pewności, ta wartość jest, możesz użyć [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) polecenia cmdlet, aby uzyskać te informacje.

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
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Aby uaktualnić istniejącą rolę, uruchom następujące polecenie programu PowerShell:

```azurepowershell
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Usunięcia niestandardowej roli zabezpieczeń

Aby usunąć niestandardową rolę, użyj [AzureRmRoleDefinition Usuń](/powershell/module/azurerm.resources/remove-azurermroledefinition) polecenia.

Poniższy przykład umożliwia usunięcie *Operator maszyny wirtualnej* niestandardowej roli zabezpieczeń.

```azurepowershell
Get-AzureRmRoleDefinition "Virtual Machine Operator"
Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>Kolejne kroki

- [Samouczek: Tworzenie niestandardowej roli zabezpieczeń przy użyciu programu Azure PowerShell](tutorial-custom-role-powershell.md)
- [Role niestandardowe na platformie Azure](custom-roles.md)
- [Operacje dostawcy zasobów Menedżera zasobów platformy Azure](resource-provider-operations.md)
