---
title: Lista definicji ról w usłudze Azure RBAC przy użyciu witryny Azure portal, azure powershell, interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure lub interfejsu API REST | Dokumenty firmy Microsoft
description: Dowiedz się, jak wyświetlić listę ról wbudowanych i niestandardowych w usłudze Azure RBAC przy użyciu witryny Azure portal, azure powershell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: aa888eedc81ceb3188f801e273c70722207bf512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062986"
---
# <a name="list-role-definitions-in-azure-rbac"></a>Wyświetlanie definicji ról w usłudze Azure RBAC

Definicja roli jest zbiorem uprawnień, które mogą być wykonywane, takich jak odczyt, zapis i usuwanie. Zazwyczaj jest po prostu nazywany rolą. [Kontrola dostępu oparta na rolach platformy Azure (RBAC)](overview.md) ma ponad 120 [wbudowanych ról](built-in-roles.md) lub można utworzyć własne role niestandardowe. W tym artykule opisano sposób listy ról wbudowanych i niestandardowych, których można użyć do udzielenia dostępu do zasobów platformy Azure.

Aby wyświetlić listę ról administratora usługi Azure Active Directory, zobacz [Uprawnienia roli administratora w usłudze Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="azure-portal"></a>Portal Azure

### <a name="list-all-roles"></a>Lista wszystkich ról

Wykonaj następujące kroki, aby wyświetlić listę wszystkich ról w witrynie Azure portal.

1. W witrynie Azure portal kliknij pozycję **Wszystkie usługi,** a następnie wybierz dowolny zakres. Na przykład można wybrać **grupy zarządzania,** subskrypcje, **grupy zasobów**lub **zasób.**

1. Kliknij określony zasób.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **Role,** aby wyświetlić listę wszystkich ról wbudowanych i niestandardowych.

   Można zobaczyć liczbę użytkowników i grup, które są przypisane do każdej roli w bieżącym zakresie.

   ![Lista ról](./media/role-definitions-list/roles-list.png)

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-all-roles"></a>Lista wszystkich ról

Aby wyświetlić listę wszystkich ról w programie Azure PowerShell, użyj [get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
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

### <a name="list-a-role-definition"></a>Wyświetlanie listy definicji roli

Aby wyświetlić listę szczegółów określonej roli, należy użyć pliku [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

### <a name="list-a-role-definition-in-json-format"></a>Wyświetlanie definicji roli w formacie JSON

Aby wyświetlić listę roli w formacie JSON, należy użyć pliku [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-permissions-of-a-role-definition"></a>Lista uprawnień definicji roli

Aby wyświetlić listę uprawnień dla określonej roli, należy użyć pliku [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

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

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

### <a name="list-all-roles"></a>Lista wszystkich ról

Aby wyświetlić listę wszystkich ról w platformie Azure CLI, użyj [listy definicji roli az](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition list
```

Poniższy przykład zawiera nazwę i opis wszystkich dostępnych definicji ról:

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

Poniższy przykład zawiera listę wszystkich wbudowanych ról.

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

### <a name="list-a-role-definition"></a>Wyświetlanie listy definicji roli

Aby wyświetlić szczegóły roli, użyj [listy definicji roli az](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition list --name <role_name>
```

W poniższym przykładzie wymieniono definicję roli *współautora:*

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-permissions-of-a-role-definition"></a>Lista uprawnień definicji roli

Poniższy przykład zawiera listę tylko *akcje* i *nieAkcje* roli *współautora.*

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

W poniższym przykładzie wymieniono tylko akcje roli *współautora maszyny wirtualnej.*

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="rest-api"></a>Interfejs API REST

### <a name="list-role-definitions"></a>Lista definicji ról

Aby wyświetlić listę definicji ról, użyj interfejsu API [definicje ról — lista](/rest/api/authorization/roledefinitions/list) REST. Aby zawęzić wyniki, należy określić zakres i filtr opcjonalny.

1. Zacznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?$filter={$filter}&api-version=2015-07-01
    ```

1. W ramach identyfikatora URI zastąp *{scope}* zakresem, dla którego chcesz wyświetlić listę definicji ról.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Zasób |

    W poprzednim przykładzie microsoft.web jest dostawcą zasobów, który odwołuje się do wystąpienia usługi App Service. Podobnie można użyć innych dostawców zasobów i określić zakres. Aby uzyskać więcej informacji, zobacz [Dostawców i typy zasobów platformy Azure](../azure-resource-manager/management/resource-providers-and-types.md) oraz obsługiwane operacje dostawcy zasobów usługi Azure Resource [Manager](resource-provider-operations.md).  
     
1. Zastąp *{filter}* warunkiem, który chcesz zastosować do filtrowania listy definicji roli.

    > [!div class="mx-tableFixed"]
    > | Filtr | Opis |
    > | --- | --- |
    > | `$filter=atScopeAndBelow()` | Wyświetla listę definicji ról dla określonego zakresu i wszelkich podzakresów. |
    > | `$filter=type+eq+'{type}'` | Wyświetla listę definicji ról określonego typu. Typ roli może `CustomRole` `BuiltInRole`być lub . |

### <a name="list-a-role-definition"></a>Wyświetlanie listy definicji roli

Aby wyświetlić szczegółowe informacje o określonej roli, należy użyć [definicji ról — Pobierz](/rest/api/authorization/roledefinitions/get) lub [Definicje ról — Pobierz przez identyfikator](/rest/api/authorization/roledefinitions/getbyid) REST API.

1. Zacznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    W przypadku definicji roli na poziomie katalogu można użyć tego żądania:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. W ramach identyfikatora URI zastąp *{scope}* zakresem, dla którego chcesz wyświetlić listę definicji roli.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Zasób |
     
1. Zastąp *{roleDefinitionId}* identyfikatorem definicji roli.

## <a name="next-steps"></a>Następne kroki

- [Wbudowane role dla zasobów platformy Azure](built-in-roles.md)
- [Niestandardowe role dla zasobów platformy Azure](custom-roles.md)
- [Lista przypisań ról przy użyciu usługi Azure RBAC i witryny Azure portal](role-assignments-list-portal.md)
- [Dodawanie lub usuwanie przypisań ról przy użyciu usługi Azure RBAC i witryny Azure Portal](role-assignments-portal.md)
