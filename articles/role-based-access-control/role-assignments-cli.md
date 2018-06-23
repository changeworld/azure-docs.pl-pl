---
title: Zarządzanie dostępem przy użyciu RBAC i interfejsu wiersza polecenia Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać dostępem dla użytkowników, grup i aplikacji, przy użyciu kontroli dostępu opartej na rolach (RBAC) i wiersza polecenia platformy Azure. W tym jak lista dostępu, udzielić dostępu i spowodować usunięcie dostępu.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 16577339f1aa33fbd1a8b90f4beaef1ee4ce806c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316400"
---
# <a name="manage-access-using-rbac-and-azure-cli"></a>Zarządzanie dostępem przy użyciu RBAC i wiersza polecenia platformy Azure

[Kontrola dostępu oparta na rolach (RBAC)](overview.md) jest sposób zarządzania dostępem do zasobów na platformie Azure. W tym artykule opisano, jak zarządzać dostępu dla użytkowników, grup i aplikacji przy użyciu RBAC oraz wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby zarządzać dostępem, jedną z następujących należy:

* [Bash w chmurze Azure powłoki](/azure/cloud-shell/overview)
* [Interfejs wiersza polecenia platformy Azure](/cli/azure)

## <a name="list-roles"></a>Lista ról

Aby wyświetlić listę wszystkich dostępnych definicji ról, należy użyć [listy definicji roli az](/cli/azure/role/definition#az-role-definition-list):

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

Poniższy przykład zawiera listę wszystkich definicji wbudowanej roli:

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

### <a name="list-actions-of-a-role"></a>Akcje listy roli

Aby wyświetlić listę akcji definicji roli, należy użyć [listy definicji roli az](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list --name <role_name>
```

Na poniższych listach przykład *współautora* definicji roli:

```azurecli
az role definition list --name "Contributor"
```

```Output
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
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

Na poniższych listach przykład *akcje* i *notActions* z *współautora* roli:

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

Poniższy przykład zawiera listę działań *Współautor·maszyny·wirtualnej* roli:

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

## <a name="list-access"></a>Dostęp do listy

W RBAC na liście dostępu, możesz listę przypisań ról.

### <a name="list-role-assignments-for-a-user"></a>Lista przypisań ról dla użytkownika

Aby wyświetlić listę przypisań ról określonego użytkownika, należy użyć [listy przypisania roli az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Domyślnie będą wyświetlane tylko przypisania ograniczone do subskrypcji. Aby wyświetlić przypisania ograniczone w zależności od zasobów w grupie, należy użyć `--all`.

Poniższy przykład zawiera listę przypisań ról, które są przypisane bezpośrednio do *patlong@contoso.com* użytkownika:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
```

### <a name="list-role-assignments-for-a-resource-group"></a>Lista przypisań ról dla grupy zasobów.

Aby wyświetlić listę przypisań ról, które istnieją dla grupy zasobów, należy użyć [listy przypisania roli az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

Poniższy przykład zawiera listę przypisań ról *pharma sprzedaży projectforecast* grupy zasobów:

```azurecli
az role assignment list --resource-group pharma-sales-projectforecast --output json | jq '.[] | {"roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}

...
```

## <a name="grant-access"></a>Udzielanie dostępu

W RBAC Aby udzielić dostępu, należy utworzyć przypisania roli.

### <a name="create-a-role-assignment-for-a-user"></a>Tworzy przypisanie roli użytkownika

Aby utworzyć przypisanie roli użytkownika w zakresie grupy zasobów, użyj [utworzenia przypisania roli az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee <assignee> --resource-group <resource_group>
```

W poniższym przykładzie przypisano *współautora maszyny wirtualnej* rolę *patlong@contoso.com* użytkownika na *pharma sprzedaży projectforecast* zakres grupy zasobów:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales-projectforecast
```

### <a name="create-a-role-assignment-for-a-group"></a>Tworzy przypisanie roli w grupie

Aby utworzyć przypisanie roli, grupy, użyj [utworzenia przypisania roli az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

W poniższym przykładzie przypisano *czytnika* rolę *zespołu Mack pods* z Identyfikatorem 22222222-2222-2222-2222-222222222222 w zakresie subskrypcji. Aby uzyskać identyfikator grupy, można użyć [listy grup ad az](/cli/azure/ad/group#az-ad-group-list) lub [Pokaż grupy ad az](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

W poniższym przykładzie przypisano *współautora maszyny wirtualnej* rolę *zespołu Mack pods* z Identyfikatorem 22222222-2222-2222-2222-222222222222 w zakresie zasobów dla sieci wirtualnej o nazwie *projektu sieci, pharma sprzedaży w-*:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111/resourcegroups/pharma-sales-projectforecast/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>Tworzenie przypisania roli dla aplikacji

Aby utworzyć rolę dla aplikacji, należy użyć [utworzenia przypisania roli az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

W poniższym przykładzie przypisano *Współautor·maszyny·wirtualnej* roli do aplikacji z 44444444-4444-4444-4444-444444444444 Identyfikatora obiektu w *pharma sprzedaży projectforecast* grupy zasobów zakres. Aby uzyskać identyfikator aplikacji, można użyć [listy aplikacji usługi ad az](/cli/azure/ad/app#az-ad-app-list) lub [Pokaż aplikacji ad az](/cli/azure/ad/app#az-ad-app-show).

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales-projectforecast
```

## <a name="remove-access"></a>Usuń dostęp

RBAC, aby usunąć dostęp, można usunąć przypisania roli używając [usunąć przypisanie roli az](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role> --resource-group <resource_group>
```

Poniższy przykład umożliwia usunięcie *Współautor·maszyny·wirtualnej* przypisania roli z *patlong@contoso.com* użytkownika na *pharma sprzedaży projectforecast* zasobów Grupa:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales-projectforecast
```

Poniższy przykład umożliwia usunięcie *czytnika* rolę z *zespołu Mack pods* z Identyfikatorem 22222222-2222-2222-2222-222222222222 w zakresie subskrypcji. Aby uzyskać identyfikator grupy, można użyć [listy grup ad az](/cli/azure/ad/group#az-ad-group-list) lub [Pokaż grupy ad az](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Kolejne kroki

- [Samouczek: Tworzenie niestandardowej roli zabezpieczeń przy użyciu wiersza polecenia platformy Azure](tutorial-custom-role-cli.md)
- [Użyj wiersza polecenia platformy Azure do zarządzania zasobami Azure i grup zasobów](../azure-resource-manager/xplat-cli-azure-resource-manager.md)