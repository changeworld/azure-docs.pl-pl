---
title: Tworzenie ról niestandardowych dla zasobów platformy Azure przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć niestandardowe role przy użyciu kontroli dostępu opartej na rolach (RBAC) dla zasobów platformy Azure przy użyciu wiersza polecenia platformy Azure. W tym jak listy, tworzenia, aktualizacji i usuwania ról niestandardowych.
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
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ebced83346a7b130598e4a5f49a72d51ffd18e4f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118777"
---
# <a name="create-custom-roles-for-azure-resources-using-azure-cli"></a>Tworzenie ról niestandardowych dla zasobów platformy Azure przy użyciu wiersza polecenia platformy Azure

Jeśli [wbudowane role dla zasobów platformy Azure](built-in-roles.md) nie spełniają potrzeb Twojej organizacji, możesz tworzyć własne role niestandardowe. W tym artykule opisano, jak tworzyć i zarządzać nimi role niestandardowe przy użyciu wiersza polecenia platformy Azure.

Aby uzyskać samouczek krok po kroku dotyczące sposobu tworzenia roli niestandardowej, zobacz [samouczka: Tworzenie roli niestandardowej na potrzeby zasobów platformy Azure przy użyciu wiersza polecenia platformy Azure](tutorial-custom-role-cli.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć niestandardowe role, potrzebne są:

- Uprawnienia do tworzenia ról niestandardowych, takie jak [Właściciel](built-in-roles.md#owner) lub [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) lub [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Wyświetlanie ról niestandardowych

Aby wyświetlić listę ról niestandardowych, które są dostępne do przypisania, należy użyć [Lista definicji roli az](/cli/azure/role/definition#az-role-definition-list). Poniższe przykłady listę wszystkich ról niestandardowych w bieżącej subskrypcji.

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}
{
  "roleName": "Virtual Machine Operator",
  "type": "CustomRole"
}

...
```

## <a name="list-a-custom-role-definition"></a>Listy niestandardową definicję roli

Aby wyświetlić listę niestandardową definicję roli, należy użyć [Lista definicji roli az](/cli/azure/role/definition#az-role-definition-list). Jest to samo polecenie, które są używane dla wbudowanej roli.

```azurecli
az role definition list --name <role_name>
```

Na poniższych listach przykład *Operator maszyny wirtualnej* definicji roli:

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```Output
[
  {
    "assignableScopes": [
      "/subscriptions/11111111-1111-1111-1111-111111111111"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "permissions": [
      {
        "actions": [
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
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Poniższy przykład wyświetla listę tylko akcje *Operator maszyny wirtualnej* roli:

```azurecli
az role definition list --name "Virtual Machine Operator" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
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
]
```

## <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Aby utworzyć rolę niestandardową, należy użyć [utworzenia definicji roli az](/cli/azure/role/definition#az-role-definition-create). Definicja roli może być opisu JSON lub ścieżkę do pliku zawierającego opis JSON.

```azurecli
az role definition create --role-definition <role_definition>
```

Poniższy przykład obejmuje tworzenie roli niestandardowej o nazwie *Operator maszyny wirtualnej*. Tę rolę niestandardową przydziela dostęp do wszystkich operacji odczytu z *Microsoft.Compute*, *Microsoft.Storage*, i *Microsoft.Network* dostęp do dostawcy i przypisuje zasobów Aby rozpocząć, uruchom ponownie i monitorować maszyny wirtualne. Tę rolę niestandardową, może służyć do dwóch subskrypcji. W tym przykładzie użyto pliku JSON jako dane wejściowe.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
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
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>Aktualizacja roli niestandardowej

Aby zaktualizować rolę niestandardową, należy najpierw użyć [Lista definicji roli az](/cli/azure/role/definition#az-role-definition-list) można pobrać definicji roli. Po drugie wprowadź żądane zmiany do definicji roli. Na koniec użyj [aktualizacji definicji roli az](/cli/azure/role/definition#az-role-definition-update) można zapisać definicji roli zaktualizowany.

```azurecli
az role definition update --role-definition <role_definition>
```

W poniższym przykładzie dodano *Microsoft.Insights/diagnosticSettings/* operacji *akcje* z *Operator maszyny wirtualnej* roli niestandardowej.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
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
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Usuwanie roli niestandardowej

Aby usunąć niestandardową rolę, użyj [usunąć definicji roli az](/cli/azure/role/definition#az-role-definition-delete). Aby określić roli do usunięcia, użyj nazwy roli lub identyfikator roli. Aby określić identyfikator roli, należy użyć [Lista definicji roli az](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

Poniższy przykład usuwa *Operator maszyny wirtualnej* roli niestandardowej.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Kolejne kroki

- [Samouczek: Tworzenie roli niestandardowej na potrzeby zasobów platformy Azure przy użyciu wiersza polecenia platformy Azure](tutorial-custom-role-cli.md)
- [Niestandardowe role dla zasobów platformy Azure](custom-roles.md)
- [Operacje dostawcy zasobów w usłudze Azure Resource Manager](resource-provider-operations.md)