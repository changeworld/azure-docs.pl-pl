---
title: Zarządzanie dostępem do zasobów platformy Azure przy użyciu funkcji RBAC i interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Dowiedz się, jak zarządzać dostępem do zasobów platformy Azure dla użytkowników, grup i aplikacji przy użyciu kontroli dostępu opartej na rolach (RBAC) i interfejsu wiersza polecenia platformy Azure. Obejmuje to wyświetlanie dostępu, jego przyznawanie i usuwanie.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 3420374e90790bd1ffe4c845c19de1bfed317302
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173738"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>Zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i interfejsu wiersza polecenia platformy Azure

[Kontrola dostępu oparta na rolach (RBAC, Role Based Access Control)](overview.md) to sposób zarządzania dostępem do zasobów platformy Azure. W tym artykule opisano sposób zarządzania dostępem użytkowników, grup i aplikacji przy użyciu funkcji RBAC i interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby zarządzać dostępem, należy wykonać jedną z następujących czynności:

* [Bash w Azure Cloud Shell](/azure/cloud-shell/overview)
* [Interfejs wiersza polecenia platformy Azure](/cli/azure)

## <a name="list-roles"></a>Tworzenie listy ról

Aby wyświetlić listę wszystkich dostępnych definicji ról, użyj [AZ role Definition list](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list
```

Poniższy przykład zawiera listę nazw i opis wszystkich dostępnych definicji ról:

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

W poniższym przykładzie wymieniono wszystkie wbudowane definicje ról:

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

## <a name="list-a-role-definition"></a>Lista definicji roli

Aby wyświetlić listę definicji roli, użyj [AZ role Definition list](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list --name <role_name>
```

Poniższy przykład zawiera listę definicji roli *współautor* :

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

### <a name="list-actions-of-a-role"></a>Wyświetlanie listy akcji roli

W poniższym przykładzie wymieniono tylko *Akcje* i nienaruszone roli *współautor* :

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

Poniższy przykład zawiera listę tylko działań roli *współautor maszyny wirtualnej* :

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

## <a name="list-access"></a>Tworzenie listy dostępu

W polu RBAC, aby wyświetlić listę uprawnień dostępu, należy wyświetlić listę przypisań ról.

### <a name="list-role-assignments-for-a-user"></a>Tworzenie listy przypisań ról dla użytkownika

Aby wyświetlić listę przypisań ról dla określonego użytkownika, użyj [AZ role przypisanie list](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Domyślnie będą wyświetlane tylko przypisania bezpośrednie mające zakres do subskrypcji. Aby wyświetlić przydziały należące do zakresu zasobów lub grup `--all` , użyj i, aby wyświetlić dziedziczone `--include-inherited`przydziały, użyj.

Poniższy przykład zawiera listę przypisań ról przypisanych bezpośrednio do użytkownika *patlong\@contoso.com* :

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

### <a name="list-role-assignments-at-a-resource-group-scope"></a>Wyświetlanie listy przypisań ról w zakresie grupy zasobów

Aby wyświetlić listę przypisań ról istniejących w zakresie grupy zasobów, użyj [AZ role przypisanie list](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

Poniższy przykład zawiera listę przypisań ról dla grupy zasobów *Pharma-Sales* :

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

### <a name="list-role-assignments-at-a-subscription-scope"></a>Wyświetlanie listy przypisań ról w zakresie subskrypcji

Aby wyświetlić listę wszystkich przypisań ról w zakresie subskrypcji, użyj [AZ role przypisanie list](/cli/azure/role/assignment#az-role-assignment-list). Aby uzyskać identyfikator subskrypcji, możesz go znaleźć w bloku **subskrypcje** w Azure Portal lub użyć [AZ Account List](/cli/azure/account#az-account-list).

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

### <a name="list-role-assignments-at-a-management-group-scope"></a>Wyświetlanie listy przypisań ról w zakresie grupy zarządzania

Aby wyświetlić listę wszystkich przypisań ról w zakresie grupy zarządzania, użyj [AZ role przypisanie list](/cli/azure/role/assignment#az-role-assignment-list). Aby uzyskać identyfikator grupy zarządzania, można go znaleźć w bloku **grupy zarządzania** w Azure Portal lub użyć [AZ Account Management-Group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="grant-access"></a>Przyznaj dostęp

Aby udzielić dostępu za pomocą kontroli dostępu opartej na rolach, tworzy się przypisanie roli.

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Tworzenie przypisania roli dla użytkownika w zakresie grupy zasobów

Aby udzielić dostępu użytkownikowi w zakresie grupy zasobów, użyj [AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

Poniższy przykład przypisuje rolę *współautor maszyny wirtualnej* do *patlong\@contoso.com* użytkownika w zakresie grupy zasobów *Pharma-Sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>Tworzenie przypisania roli przy użyciu unikatowego identyfikatora roli

Istnieje kilka razy więcej czasu, gdy nazwa roli może ulec zmianie, na przykład:

- Używasz własnej roli niestandardowej i zdecydujesz się zmienić nazwę.
- W nazwie jest używana rola w wersji zapoznawczej mająca **(wersja zapoznawcza)** . Po wydaniu roli zostanie zmieniona nazwa roli.

> [!IMPORTANT]
> Wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Nawet w przypadku zmiany nazwy roli identyfikator roli nie zmienia się. Jeśli używasz skryptów lub automatyzacji do tworzenia przypisań ról, najlepszym rozwiązaniem jest użycie unikatowego identyfikatora roli zamiast nazwy roli. W związku z tym, jeśli zostanie zmieniona nazwa roli, skrypty mogą pracować.

Aby utworzyć przypisanie roli przy użyciu unikatowego identyfikatora roli zamiast nazwy roli, użyj polecenia [AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

Poniższy przykład przypisuje rolę [współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) do użytkownika *patlong\@contoso.com* w zakresie grupy zasobów *Pharma-Sales* . Aby uzyskać unikatowy identyfikator roli, można użyć polecenie [AZ role Definition list](/cli/azure/role/definition#az-role-definition-list) lub zobaczyć [wbudowane role dla zasobów platformy Azure](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>Tworzenie przypisania roli dla grupy

Aby udzielić dostępu do grupy, użyj [AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create). Aby uzyskać identyfikator grupy, możesz użyć [AZ AD Group list](/cli/azure/ad/group#az-ad-group-list) lub [AZ AD Group Show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Poniższy przykład przypisuje rolę *czytnika* do grupy *zespołu Ann Mack* o identyfikatorze 22222222-2222-2222-2222-222222222222 w zakresie subskrypcji.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

Poniższy przykład przypisuje rolę *współautor maszyny wirtualnej* do grupy *zespołu Ann Mack* o identyfikatorze 22222222-2222-2222-2222-222222222222 w zakresie zasobów dla sieci wirtualnej o nazwie *Pharma-Sales-Project-Network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application-at-a-resource-group-scope"></a>Tworzenie przypisania roli dla aplikacji w zakresie grupy zasobów

Aby udzielić dostępu do aplikacji, użyj [AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create). Aby uzyskać identyfikator obiektu aplikacji, można użyć [AZ AD App list](/cli/azure/ad/app#az-ad-app-list) lub [AZ AD App Show](/cli/azure/ad/app#az-ad-app-show).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

Poniższy przykład przypisuje rolę *współautor maszyny wirtualnej* do aplikacji z identyfikatorem obiektu 44444444-4444-4444-4444-444444444444 w zakresie grupy zasobów *Pharma-Sales* .

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-user-at-a-subscription-scope"></a>Utwórz przypisanie roli dla użytkownika w zakresie subskrypcji

Aby udzielić dostępu użytkownikowi z zakresem subskrypcji, użyj [AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create). Aby uzyskać identyfikator subskrypcji, możesz go znaleźć w bloku **subskrypcje** w Azure Portal lub użyć [AZ Account List](/cli/azure/account#az-account-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

Poniższy przykład przypisuje rolę *czytnika* do użytkownika *annm\@example.com* w zakresie subskrypcji.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="create-a-role-assignment-for-a-user-at-a-management-group-scope"></a>Tworzenie przypisania roli dla użytkownika w zakresie grupy zarządzania

Aby udzielić dostępu użytkownikowi w zakresie grupy zarządzania, użyj [AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create). Aby uzyskać identyfikator grupy zarządzania, można go znaleźć w bloku **grupy zarządzania** w Azure Portal lub użyć [AZ Account Management-Group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Poniższy przykład przypisuje rolę *czytnika rozliczeń* do użytkownika *Alain\@example.com* w zakresie grupy zarządzania.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="create-a-role-assignment-for-a-new-service-principal"></a>Tworzenie przypisania roli dla nowej nazwy głównej usługi

Jeśli utworzysz nową nazwę główną usługi i natychmiast spróbujesz przypisać rolę do tej jednostki usługi, w niektórych przypadkach przypisanie roli może zakończyć się niepowodzeniem. Na przykład, jeśli używasz skryptu do utworzenia nowej tożsamości zarządzanej, a następnie spróbujesz przypisać rolę do tej jednostki usługi, przypisanie roli może zakończyć się niepowodzeniem. Przyczyną tego błędu jest prawdopodobnie opóźnienie replikacji. Nazwa główna usługi jest tworzona w jednym regionie; jednak przypisanie roli może wystąpić w innym regionie, który jeszcze nie replikuje jednostki usługi. Aby rozwiązać ten scenariusz, należy określić typ podmiotu podczas tworzenia przypisania roli.

Aby utworzyć przypisanie roli, użyj [AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create), określ wartość dla `--assignee-object-id`, a następnie ustaw `--assignee-principal-type` jako `ServicePrincipal`.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Poniższy przykład przypisuje rolę *współautor maszyny wirtualnej* do tożsamości zarządzanej przez *test MSI* w zakresie grupy zasobów *Pharma-Sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-access"></a>Usuń dostęp

Aby usunąć dostęp w ramach RBAC, usuń przypisanie roli za pomocą polecenia [AZ role przypisanie Delete](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

Poniższy przykład usuwa przypisanie roli *współautor maszyny wirtualnej* z użytkownika *patlong\@contoso.com* w grupie zasobów *Pharma-Sales* :

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

Poniższy przykład usuwa rolę *czytnika* z grupy *zespołu Ann Mack* o identyfikatorze 22222222-2222-2222-2222-222222222222 w zakresie subskrypcji. Aby uzyskać identyfikator grupy, możesz użyć [AZ AD Group list](/cli/azure/ad/group#az-ad-group-list) lub [AZ AD Group Show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

Poniższy przykład usuwa rolę *czytnika rozliczeń* z użytkownika *Alain\@example.com* w zakresie grupy zarządzania. Aby uzyskać identyfikator grupy zarządzania, można użyć [AZ Account Management-Group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Tworzenie roli niestandardowej dla zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](tutorial-custom-role-cli.md)
- [Zarządzanie zasobami i grupami zasobów platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/cli-azure-resource-manager.md)
