---
title: "Zarządzanie kontrolą dostępu opartej na rolach (RBAC) przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft"
description: "Jak zarządzać RBAC z programem Azure PowerShell, w tym role, przypisywanie ról i usuwanie przypisań ról."
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 5e7e01502a173cb93216c77dd43f61094fd88148
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2018
---
# <a name="manage-role-based-access-control-with-azure-powershell"></a>Zarządzanie kontrolą dostępu opartą na rolach za pomocą programu Azure PowerShell
> [!div class="op_single_selector"]
> * [Program PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](role-based-access-control-manage-access-azure-cli.md)
> * [Interfejs API REST](role-based-access-control-manage-access-rest.md)

Z opartej na rolach kontroli dostępu (RBAC), należy zdefiniować dostęp dla użytkowników, grup i nazwy główne usług poprzez przypisywanie ról w określonym zakresie. W tym artykule opisano, jak zarządzać dostępem przy użyciu programu Azure PowerShell.

Przed użyciem programu PowerShell do zarządzania RBAC, potrzebne są następujące wymagania wstępne:

* Program Azure PowerShell w wersji 0.8.8 lub nowszej. Aby zainstalować najnowszą wersję i skojarzyć go z subskrypcją platformy Azure, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).
* Polecenia cmdlet Menedżera zasobów systemu Azure. Zainstaluj [poleceń cmdlet usługi Azure Resource Manager](/powershell/azure/overview) w programie PowerShell.

## <a name="list-roles"></a>Lista ról
### <a name="list-all-available-roles"></a>Wyświetl listę wszystkich dostępnych ról
Aby role RBAC listy, które są dostępne do przypisania i przeprowadzać inspekcję operacji, którym przyznano dostęp, użyj `Get-AzureRmRoleDefinition`.

```
Get-AzureRmRoleDefinition | FT Name, Description
```

![Zrzut ekranu PowerShell-Get AzureRmRoleDefinition - RBAC](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### <a name="list-actions-of-a-role"></a>Akcje listy roli
Aby wyświetlić listę akcji dla konkretnej roli, należy użyć `Get-AzureRmRoleDefinition <role name>`.

```
Get-AzureRmRoleDefinition Contributor | FL Actions, NotActions

(Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions
```

![Zrzut ekranu PowerShell-Get AzureRmRoleDefinition dla konkretnej roli - RBAC](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## <a name="see-who-has-access"></a>Zobacz, kto ma dostęp
Aby wyświetlić listę przypisań dostępu RBAC, użyj `Get-AzureRmRoleAssignment`.

### <a name="list-role-assignments-at-a-specific-scope"></a>Lista przypisań ról w określonym zakresie
Znajduje wszystkie przypisania dostępu dla określonej subskrypcji, grupy zasobów lub zasobów. Na przykład, aby wyświetlić wszystkie aktywne przydziałów dla grupy zasobów, użyj `Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>`.

```
Get-AzureRmRoleAssignment -ResourceGroupName Pharma-Sales-ProjectForcast | FL DisplayName, RoleDefinitionName, Scope
```

![Zrzut ekranu PowerShell-Get AzureRmRoleAssignment dla grupy zasobów - RBAC](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### <a name="list-roles-assigned-to-a-user"></a>Lista ról przypisana do użytkownika
Aby wyświetlić listę wszystkich ról, które są przypisane do określonego użytkownika i role, które są przypisane do grup, do których należy użytkownik, należy użyć `Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups`.

```
Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com | FL DisplayName, RoleDefinitionName, Scope

Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

![Zrzut ekranu PowerShell-Get AzureRmRoleAssignment dla użytkownika — RBAC](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>Wyświetl listę przypisań ról administratora i coadmin klasycznym usługi
Do listy dostępu do przypisania dla subskrypcji klasycznego administratora i coadministrators, użyj:

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## <a name="grant-access"></a>Udzielanie dostępu
### <a name="search-for-object-ids"></a>Wyszukaj identyfikatory obiektów
Aby przypisać rolę, trzeba będzie zidentyfikować obiekt (użytkownika, grupy lub aplikacji) i zakresu.

Jeśli nie znasz identyfikator subskrypcji można znaleźć w **subskrypcje** bloku w portalu Azure. Aby dowiedzieć się, jak wykonać zapytanie dla Identyfikatora subskrypcji, zobacz [Get-AzureSubscription](/powershell/module/azure/get-azuresubscription?view=azuresmps-3.7.0) w witrynie MSDN.

Aby uzyskać identyfikator obiektu dla grupy usługi Azure AD, należy użyć:

    Get-AzureRmADGroup -SearchString <group name in quotes>

Aby uzyskać identyfikator obiektu dla nazwy głównej usługi Azure AD lub aplikacji, należy użyć:

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Przypisywanie roli do aplikacji w zakresie subskrypcji
Aby udzielić dostępu do aplikacji w zakresie subskrypcji, należy użyć:

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>

![Zrzut ekranu PowerShell — nowy AzureRmRoleAssignment - RBAC](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Przypisywanie roli użytkownika w zakresie grupy zasobów
Aby udzielić dostępu do użytkownika w zakresie grupy zasobów, należy użyć:

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![Zrzut ekranu PowerShell — nowy AzureRmRoleAssignment - RBAC](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Przypisywanie roli do grupy w zakresie zasobów
Aby udzielić dostępu do grupy w zakresie zasobów, należy użyć:

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![Zrzut ekranu PowerShell — nowy AzureRmRoleAssignment - RBAC](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## <a name="remove-access"></a>Usuń dostęp
Aby usunąć dostęp dla użytkowników, grup i aplikacji, należy użyć:

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![Zrzut ekranu PowerShell-Remove AzureRmRoleAssignment - RBAC](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## <a name="create-a-custom-role"></a>Tworzenie niestandardowej roli zabezpieczeń
Aby utworzyć niestandardową rolę, użyj ```New-AzureRmRoleDefinition``` polecenia. Istnieją dwie metody struktury roli przy użyciu PSRoleDefinitionObject lub szablonu JSON. 

## <a name="get-actions-for-a-resource-provider"></a>Pobierz akcji dla dostawcy zasobów
Tworząc role niestandardowe od początku, należy znać wszystkie możliwe operacje z dostawców zasobów.
Użyj ```Get-AzureRMProviderOperation``` polecenie, aby uzyskać te informacje.
Na przykład jeśli chcesz sprawdzić wszystkie operacje dostępne dla maszyny wirtualnej, użyj tego polecenia:

```
Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation , Description -AutoSize
```

### <a name="create-role-with-psroledefinitionobject"></a>Tworzenie roli z PSRoleDefinitionObject
Przy użyciu programu PowerShell do utworzenia niestandardowej roli zabezpieczeń, możesz rozpocząć od początku lub użyj jednej z [wbudowane role](role-based-access-built-in-roles.md) jako punktu wyjścia. W przykładzie w tej sekcji rozpoczyna się od wbudowanej roli i dostosowuje go z wyższego poziomu uprawnień. Edycja atrybutów do dodania *akcje*, *notActions*, lub *zakresy* , a następnie zapisz zmiany jako nową rolę.

Poniższy przykład rozpoczyna się od *Współautor·maszyny·wirtualnej* roli i używa do utworzenia niestandardowej roli zabezpieczeń o nazwie *Operator maszyny wirtualnej*. Nowa rola nieograniczony dostęp do wszystkich operacji odczytu z *Microsoft.Compute*, *Microsoft.Storage*, i *Microsoft.Network* zasobów dostawców i udziela dostępu do uruchomienia , uruchom ponownie, a monitorowania maszyn wirtualnych. Tworzona rola niestandardowa może służyć w dwóch subskrypcji.

```
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
$role.AssignableScopes.Add("/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e")
$role.AssignableScopes.Add("/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624")
New-AzureRmRoleDefinition -Role $role
```

![Zrzut ekranu PowerShell-Get AzureRmRoleDefinition - RBAC](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

### <a name="create-role-with-json-template"></a>Tworzenie roli przy użyciu szablonu JSON
Szablonu JSON może służyć jako definicji źródła niestandardowego roli. Poniższy przykład tworzy niestandardową rolę, która umożliwia dostęp do odczytu do zasobów magazynu i zasobów obliczeniowych, dostęp do pomocy technicznej, i dodaje do dwóch subskrypcji tej roli. Utwórz nowy plik `C:\CustomRoles\customrole1.json` z następującym przykładzie. Identyfikator powinien być ustawiony na `null` na tworzenie początkowej roli jako nowy identyfikator jest generowany automatycznie. 

```
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
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```
Aby dodać rolę do subskrypcji, uruchom następujące polecenie programu PowerShell:
```
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="modify-a-custom-role"></a>Modyfikowanie niestandardowej roli zabezpieczeń
Podobnie do utworzenia niestandardowej roli zabezpieczeń, można zmodyfikować istniejącą rolę niestandardowych za pomocą PSRoleDefinitionObject lub szablonu JSON.

### <a name="modify-role-with-psroledefinitionobject"></a>Modyfikuj rolę z PSRoleDefinitionObject
Aby zmodyfikować niestandardową rolę, najpierw użyj `Get-AzureRmRoleDefinition` polecenie, aby pobrać definicji roli. Po drugie wprowadź żądane zmiany do definicji roli. Na koniec użyj `Set-AzureRmRoleDefinition` polecenie, aby zapisać definicji roli zmodyfikowane.

W poniższym przykładzie dodano `Microsoft.Insights/diagnosticSettings/*` operacji *Operator maszyny wirtualnej* niestandardowej roli zabezpieczeń.

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

![Zrzut ekranu PowerShell-Set AzureRmRoleDefinition - RBAC](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

W poniższym przykładzie dodano subskrypcji platformy Azure do zakresów można przypisać z *Operator maszyny wirtualnej* niestandardowej roli zabezpieczeń.

```
Get-AzureRmSubscription - SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/34370e90-ac4a-4bf9-821f-85eeedead1a2")
Set-AzureRmRoleDefinition -Role $role
```

![Zrzut ekranu PowerShell-Set AzureRmRoleDefinition - RBAC](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

### <a name="modify-role-with-json-template"></a>Modyfikuj rolę przy użyciu szablonu JSON
Przy użyciu poprzednich szablonu JSON, można łatwo zmodyfikować istniejącą rolę niestandardowych można dodać ani usunąć działań. Zaktualizuj szablonu JSON, a następnie dodaj akcję odczytu dotyczące sieci, jak pokazano w poniższym przykładzie. Łącznie definicje wymienione w szablonie nie są stosowane do istniejącej definicji, co oznacza dokładnie, jak określone w szablonie pojawia się roli. Należy również zaktualizować w polu identyfikatora identyfikator roli. Jeśli nie masz pewności, ta wartość jest, możesz użyć `Get-AzureRmRoleDefinition` polecenia cmdlet, aby uzyskać te informacje.

```
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
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

Aby uaktualnić istniejącą rolę, uruchom następujące polecenie programu PowerShell:
```
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Usunięcia niestandardowej roli zabezpieczeń
Aby usunąć niestandardową rolę, użyj `Remove-AzureRmRoleDefinition` polecenia.

Poniższy przykład umożliwia usunięcie *Operator maszyny wirtualnej* niestandardowej roli zabezpieczeń.

```
Get-AzureRmRoleDefinition "Virtual Machine Operator"

Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

![Zrzut ekranu PowerShell-Remove AzureRmRoleDefinition - RBAC](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## <a name="list-custom-roles"></a>Lista ról niestandardowych
Aby wyświetlić listę ról, które są dostępne do przypisania w zakresie, należy użyć `Get-AzureRmRoleDefinition` polecenia.

Poniższy przykład zawiera listę wszystkich ról, które są dostępne do przypisania w wybranej subskrypcji.

```
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

![Zrzut ekranu PowerShell-Get AzureRmRoleDefinition - RBAC](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

W poniższym przykładzie *Operator maszyny wirtualnej* rola niestandardowa nie jest dostępna w *Production4* subskrypcji, ponieważ nie ma subskrypcji **AssignableScopes** roli.

![Zrzut ekranu PowerShell-Get AzureRmRoleDefinition - RBAC](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## <a name="see-also"></a>Zobacz także
* [Używanie programu Azure PowerShell z usługą Azure Resource Manager](../powershell-azure-resource-manager.md)
  [!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

