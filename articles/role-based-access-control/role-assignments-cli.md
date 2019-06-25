---
title: Zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać dostępem do zasobów platformy Azure dla użytkowników, grup i aplikacji przy użyciu kontroli dostępu opartej na rolach (RBAC) oraz wiersza polecenia platformy Azure. Obejmuje to wyświetlanie dostępu, jego przyznawanie i usuwanie.
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
ms.date: 04/17/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: bc5deb614e2ac6e47ff3bf241943df92d97699b2
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295178"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>Zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i wiersza polecenia platformy Azure

[Kontrola dostępu oparta na rolach (RBAC, Role Based Access Control)](overview.md) to sposób zarządzania dostępem do zasobów platformy Azure. W tym artykule opisano, jak zarządzać dostępu dla użytkowników, grup i aplikacji przy użyciu RBAC i wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Do zarządzania dostępem, potrzebujesz jednego z następujących czynności:

* [Powłoka bash w usłudze Azure Cloud Shell](/azure/cloud-shell/overview)
* [Interfejs wiersza polecenia platformy Azure](/cli/azure)

## <a name="list-roles"></a>Tworzenie listy ról

Aby wyświetlić listę wszystkich dostępnych definicji ról, należy użyć [Lista definicji roli az](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list
```

Poniższy przykład wyświetla nazwę i opis wszystkich dostępnych definicji ról:

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

Poniższy przykład wyświetla listę wszystkich definicji roli wbudowanej:

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

Aby wyświetlić listę definicji roli, należy użyć [Lista definicji roli az](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list --name <role_name>
```

Na poniższych listach przykład *Współautor* definicji roli:

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

### <a name="list-actions-of-a-role"></a>Lista akcji roli

Poniższy przykład wyświetla listę tylko *akcje* i *notActions* z *Współautor* roli:

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

Poniższy przykład wyświetla listę tylko akcje *Współautor maszyny wirtualnej* roli:

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

RBAC dostęp do listy, możesz liście przypisań ról.

### <a name="list-role-assignments-for-a-user"></a>Tworzenie listy przypisań ról dla użytkownika

Aby wyświetlić listę przypisań ról określonego użytkownika, należy użyć [Lista przypisywanie roli az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Domyślnie będą wyświetlane tylko przypisań bezpośrednich ograniczone do subskrypcji. Aby wyświetlić przypisania zakresie zasobu lub grupy, należy użyć `--all` wyświetlić asisgnments dziedziczone użyj `--include-inherited`.

Poniższy przykład wyświetla przypisania ról, które są przypisane bezpośrednio do *patlong\@contoso.com* użytkownika:

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

### <a name="list-role-assignments-for-a-resource-group"></a>Tworzenie listy przypisań ról dla grupy zasobów

Aby wyświetlić listę przypisań ról, które istnieją dla grupy zasobów, użyj [Lista przypisywanie roli az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

Poniższy przykład wyświetla przypisania ról *pharma sprzedaży* grupy zasobów:

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## <a name="grant-access"></a>Udzielanie dostępu

Aby udzielić dostępu za pomocą kontroli dostępu opartej na rolach, tworzy się przypisanie roli.

### <a name="create-a-role-assignment-for-a-user"></a>Tworzenie przypisania roli dla użytkownika

Aby utworzyć przypisania roli dla użytkownika w zakresie grupy zasobów, użyj [utworzenia przypisania roli az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

Poniższy przykład przypisuje *Współautor maszyny wirtualnej* roli *patlong\@contoso.com* użytkownika na *pharma sprzedaży* zakresie grupy zasobów:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>Utwórz przypisanie roli, przy użyciu Identyfikatora szczególnej roli

Istnieje kilka razy, gdy nazwa roli mogą ulec zmianie, na przykład:

- W przypadku korzystania z własnego rolę niestandardową i zdecydujesz się zmienić nazwę.
- W przypadku korzystania z roli (wersja zapoznawcza), która ma **(wersja zapoznawcza)** w nazwie. Po zwolnieniu rolę roli została zmieniona.

> [!IMPORTANT]
> Wersja zapoznawcza jest dostarczane bez umowy dotyczącej poziomu usług, a nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Nawet jeśli rola została zmieniona, identyfikator roli nie zmienia się. Jeśli używasz skryptów i automatyzacji do utworzenia przypisania roli jest najlepszym rozwiązaniem jest używanie Unikatowy identyfikator zamiast nazwy roli. W związku z tym Jeśli rola została zmieniona, skrypty są bardziej prawdopodobne do pracy.

Aby utworzyć przypisania roli za pomocą Identyfikatora szczególnej roli zamiast nazwy roli, należy użyć [utworzenia przypisania roli az](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

Poniższy przykład przypisuje [Współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) roli *patlong\@contoso.com* użytkownika na *pharma sprzedaży* zakresie grupy zasobów. Aby uzyskać identyfikator szczególnej roli, można użyć [Lista definicji roli az](/cli/azure/role/definition#az-role-definition-list) lub zobacz [wbudowane role zasobów platformy Azure](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>Tworzenie przypisania roli dla grupy

Aby utworzyć przypisanie roli, grupy, użyj [utworzenia przypisania roli az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Poniższy przykład przypisuje *czytnika* roli *zespołu Mack pods* grupy za pomocą Identyfikatora 22222222-2222-2222-2222-222222222222 w zakresie subskrypcji. Aby uzyskać identyfikator grupy, można użyć [listy grup usługi ad az](/cli/azure/ad/group#az-ad-group-list) lub [Pokaż grupy ad az](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

Poniższy przykład przypisuje *Współautor maszyny wirtualnej* roli *zespołu Mack pods* grupy za pomocą Identyfikatora 22222222-2222-2222-2222-222222222222 w zakresie zasobów dla sieci wirtualnej o nazwie *projektu sieci, pharma sprzedaży w-* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>Tworzenie przypisania roli aplikacji

Aby utworzyć rolę dla aplikacji, należy użyć [utworzenia przypisania roli az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Poniższy przykład przypisuje *Współautor maszyny wirtualnej* roli do aplikacji za pomocą 44444444-4444-4444-4444-444444444444 Identyfikatora obiektu w *pharma sprzedaży* zakresie grupy zasobów. Aby uzyskać identyfikator obiektu aplikacji, można użyć [az ad app list](/cli/azure/ad/app#az-ad-app-list) lub [az ad app show](/cli/azure/ad/app#az-ad-app-show).

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

## <a name="remove-access"></a>Usuwanie dostępu

RBAC, aby spowodować usunięcie dostępu, możesz usunąć przypisania roli używając [Usuń przypisanie roli az](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

Poniższy przykład usuwa *Współautor maszyny wirtualnej* przypisania roli z *patlong\@contoso.com* użytkownika na *pharma sprzedaży* Grupa zasobów:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

Poniższy przykład usuwa *czytnika* rolę z *zespołu Mack pods* grupy za pomocą Identyfikatora 22222222-2222-2222-2222-222222222222 w zakresie subskrypcji. Aby uzyskać identyfikator grupy, można użyć [listy grup usługi ad az](/cli/azure/ad/group#az-ad-group-list) lub [Pokaż grupy ad az](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="next-steps"></a>Kolejne kroki

- [Samouczek: Tworzenie roli niestandardowej na potrzeby zasobów platformy Azure przy użyciu wiersza polecenia platformy Azure](tutorial-custom-role-cli.md)
- [Używanie wiersza polecenia platformy Azure do zarządzania zasobami i grupami zasobów platformy Azure](../azure-resource-manager/cli-azure-resource-manager.md)
