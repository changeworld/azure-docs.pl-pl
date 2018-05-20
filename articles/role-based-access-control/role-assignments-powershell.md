---
title: Zarządzanie kontrolą dostępu opartej na rolach (RBAC) przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Jak zarządzać RBAC z programem Azure PowerShell, w tym role, przypisywanie ról i usuwanie przypisań ról.
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
ms.date: 04/17/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 9c8f5bf8036874213338b646b642407ab65e2293
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="manage-role-based-access-control-with-azure-powershell"></a>Zarządzanie kontrolą dostępu opartej na rolach przy użyciu programu Azure PowerShell
> [!div class="op_single_selector"]
> * [Program PowerShell](role-assignments-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](role-assignments-cli.md)
> * [Interfejs API REST](role-assignments-rest.md)

Przy użyciu kontroli dostępu opartej na rolach (RBAC) należy zdefiniować dostęp dla użytkowników, grup i nazwy główne usług poprzez przypisywanie ról w określonym zakresie. W tym artykule opisano, jak zarządzać dostępem przy użyciu programu Azure PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem programu PowerShell do zarządzania RBAC, należy jedną z następujących czynności:

* [PowerShell w chmurze Azure powłoki](/azure/cloud-shell/overview)
* [Program Azure PowerShell 5.1.0 lub nowszy](/powershell/azure/install-azurerm-ps)

## <a name="list-roles"></a>Lista ról

### <a name="list-all-available-roles"></a>Wyświetl listę wszystkich dostępnych ról

Aby role RBAC listy, które są dostępne do przypisania i przeprowadzać inspekcję operacji, którym przyznano dostęp, użyj [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

```azurepowershell
Get-AzureRmRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-specific-role"></a>Lista konkretnej roli

Aby wyświetlić listę określoną rolę, użyj [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

```azurepowershell
Get-AzureRmRoleDefinition <role name>
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
AssignableScopes : {/}
```

### <a name="list-a-specific-role-in-json-format"></a>Lista konkretnej roli w formacie JSON

Aby wyświetlić listę konkretnej roli w formacie JSON, użyj [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

```azurepowershell
Get-AzureRmRoleDefinition <role name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Contributor" | ConvertTo-Json

{
    "Name":  "Contributor",
    "Id":  "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "IsCustom":  false,
    "Description":  "Lets you manage everything except access to resources.",
    "Actions":  [
                    "*"
                ],
    "NotActions":  [
                       "Microsoft.Authorization/*/Delete",
                       "Microsoft.Authorization/*/Write",
                       "Microsoft.Authorization/elevateAccess/Action"
                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

### <a name="list-actions-of-a-role"></a>Akcje listy roli

Aby wyświetlić listę akcji dla konkretnej roli, należy użyć [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

```azurepowershell
Get-AzureRmRoleDefinition <role name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action}
```

```azurepowershell
(Get-AzureRmRoleDefinition <role name>).Actions
```

```Example
PS C:\> (Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="see-who-has-access"></a>Zobacz, kto ma dostęp

Aby wyświetlić listę przypisań dostępu RBAC, użyj [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

### <a name="list-role-assignments-at-a-specific-scope"></a>Lista przypisań ról w określonym zakresie

Znajduje wszystkie przypisania roli dla określonej subskrypcji, grupy zasobów lub zasobów. Na przykład, aby wyświetlić wszystkie aktywne przydziałów dla grupy zasobów, użyj [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

```azurepowershell
Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>
```

```Example
PS C:\> Get-AzureRmRoleAssignment -ResourceGroupName pharma-sales-projectforecast | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
```

### <a name="list-roles-assigned-to-a-user"></a>Lista ról przypisana do użytkownika

Aby wyświetlić listę wszystkich ról, które są przypisane do określonego użytkownika, należy użyć [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

```azurepowershell
Get-AzureRmRoleAssignment -SignInName <user email>
```

```Example
PS C:\> Get-AzureRmRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
```

Aby wyświetlić listę wszystkich ról, które są przypisane do określonego użytkownika i role, które są przypisane do grup, do których należy użytkownik, należy użyć [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

```azurepowershell
Get-AzureRmRoleAssignment -SignInName <user email> -ExpandPrincipalGroups
```

```Example
Get-AzureRmRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>Wyświetl listę przypisań ról administratora i coadmin klasycznym usługi

Aby wyświetlić listę przypisań dostępu dla subskrypcji klasycznego administratora i współadministratorzy, użyj [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment):

```azurepowershell
Get-AzureRmRoleAssignment -IncludeClassicAdministrators
```

## <a name="grant-access"></a>Udzielanie dostępu

### <a name="search-for-object-ids"></a>Wyszukaj identyfikatory obiektów

Aby przypisać rolę, trzeba będzie zidentyfikować obiekt (użytkownika, grupy lub aplikacji) i zakresu.

Jeśli nie znasz identyfikator subskrypcji można znaleźć w **subskrypcje** bloku w portalu Azure lub można użyć [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

Aby uzyskać identyfikator obiektu dla grupy usługi Azure AD, należy użyć [Get-AzureRmADGroup](/powershell/module/azurerm.resources/get-azurermadgroup):

```azurepowershell
Get-AzureRmADGroup -SearchString <group name in quotes>
```

Aby uzyskać identyfikator obiektu dla nazwy głównej usługi Azure AD lub aplikacji, należy użyć [Get-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal):

```azurepowershell
Get-AzureRmADServicePrincipal -SearchString <service name in quotes>
```

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Przypisywanie roli do aplikacji w zakresie subskrypcji

Aby udzielić dostępu do aplikacji w zakresie subskrypcji, należy użyć [AzureRmRoleAssignment nowy](/powershell/module/azurerm.resources/new-azurermroleassignment):

```azurepowershell
New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>
```

```Example
PS C:\> New-AzureRmRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Przypisywanie roli użytkownika w zakresie grupy zasobów

Aby udzielić dostępu do użytkownika w zakresie grupy zasobów, należy użyć [AzureRmRoleAssignment nowy](/powershell/module/azurerm.resources/new-azurermroleassignment):

```azurepowershell
New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>
```

```Example
PS C:\> New-AzureRmRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales-projectforecast


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Przypisywanie roli do grupy w zakresie zasobów

Aby udzielić dostępu do grupy w zakresie zasobów, użyj [AzureRmRoleAssignment nowy](/powershell/module/azurerm.resources/new-azurermroleassignment):

```azurepowershell
New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>
```

```Example
PS C:\> Get-AzureRmADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzureRmRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

## <a name="remove-access"></a>Usuń dostęp

Aby usunąć dostęp dla użytkowników, grup i aplikacji, należy użyć [AzureRmRoleAssignment Usuń](/powershell/module/azurerm.resources/remove-azurermroleassignment):

```azurepowershell
Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>
```

```Example
PS C:\> Remove-AzureRmRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales-projectforecast
```

## <a name="list-custom-roles"></a>Lista ról niestandardowych

Aby wyświetlić listę ról, które są dostępne do przypisania w zakresie, należy użyć [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) polecenia.

Poniższy przykład zawiera listę wszystkich ról, które są dostępne do przypisania w wybranej subskrypcji.

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

### <a name="create-a-role-with-psroledefinition-object"></a>Tworzenie roli z obiektem PSRoleDefinition

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

W poniższym przykładzie przedstawiono innym sposobem tworzenia *Operator maszyny wirtualnej* niestandardowej roli zabezpieczeń. Tworząc nowy obiekt PSRoleDefinition uruchamiania. Operacje akcji są określone w `perms` zmiennej i należy ustawić `Actions` właściwości. `NotActions` Właściwość jest ustawiona, odczytując `NotActions` z [Współautor·maszyny·wirtualnej](built-in-roles.md#virtual-machine-contributor) wbudowanej roli. Ponieważ [Współautor·maszyny·wirtualnej](built-in-roles.md#virtual-machine-contributor) nie ma żadnych `NotActions`, ten wiersz nie jest wymagana, ale pokazuje, jak można pobrać informacji z inną rolą.

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

### <a name="create-role-with-json-template"></a>Tworzenie roli przy użyciu szablonu JSON

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

## <a name="modify-a-custom-role"></a>Modyfikowanie niestandardowej roli zabezpieczeń

Podobnie jak tworzenie niestandardowej roli zabezpieczeń, można zmodyfikować istniejącą rolę niestandardowych za pomocą `PSRoleDefinition` obiektu lub szablonu JSON.

### <a name="modify-role-with-psroledefinition-object"></a>Modyfikuj rolę z obiektem PSRoleDefinition

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

### <a name="modify-role-with-json-template"></a>Modyfikuj rolę przy użyciu szablonu JSON

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

## <a name="see-also"></a>Zobacz także

* [Używanie programu Azure PowerShell z usługą Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
