---
title: Tworzenie lub aktualizowanie ról niestandardowych zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak wymieniać, tworzyć, aktualizować lub usuwać role niestandardowe za pomocą kontroli dostępu opartej na rolach (RBAC) dla zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 44676f7b92c2bcd30612295840054ab2f0c0cf12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062231"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-azure-cli"></a>Tworzenie lub aktualizowanie ról niestandardowych zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Dodawanie grupy zarządzania do `AssignableScopes` jest obecnie w wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli [wbudowane role dla zasobów platformy Azure](built-in-roles.md) nie spełniają potrzeb Twojej organizacji, możesz tworzyć własne role niestandardowe. W tym artykule opisano sposób listy, tworzenia, aktualizowania lub usuwania ról niestandardowych przy użyciu interfejsu wiersza polecenia platformy Azure.

Aby uzyskać samouczek krok po kroku na temat tworzenia roli niestandardowej, zobacz [Samouczek: Tworzenie roli niestandardowej dla zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](tutorial-custom-role-cli.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć role niestandardowe, potrzebujesz:

- Uprawnienia do tworzenia ról niestandardowych, takie jak [Właściciel](built-in-roles.md#owner) lub [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) lub [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Wyświetlanie ról niestandardowych

Aby wyświetlić listę ról niestandardowych dostępnych dla przypisania, użyj [listy definicji roli az](/cli/azure/role/definition#az-role-definition-list). Poniższe przykłady lista wszystkich ról niestandardowych w bieżącej subskrypcji.

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

## <a name="list-a-custom-role-definition"></a>Wyświetlanie listy definicji roli niestandardowej

Aby wyświetlić listę niestandardowej definicji roli, użyj [listy definicji roli az](/cli/azure/role/definition#az-role-definition-list). Jest to to samo polecenie, które można użyć dla wbudowanej roli.

```azurecli
az role definition list --name <role_name>
```

W poniższym przykładzie wymieniono definicję roli *operatora maszyny wirtualnej:*

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

Poniższy przykład zawiera tylko akcje roli *operatora maszyny wirtualnej:*

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

Aby utworzyć rolę niestandardową, użyj [funkcji tworzenia definicji roli az](/cli/azure/role/definition#az-role-definition-create). Definicja roli może być opis JSON lub ścieżka do pliku zawierającego opis JSON.

```azurecli
az role definition create --role-definition <role_definition>
```

W poniższym przykładzie utworzy się rolę niestandardową o nazwie *Operator maszyny wirtualnej*. Ta rola niestandardowa przypisuje dostęp do wszystkich operacji odczytu dostawców zasobów *Microsoft.Compute*, *Microsoft.Storage*i *Microsoft.Network* i przypisuje dostęp do uruchamiania, ponownego uruchamiania i monitorowania maszyn wirtualnych. Tej roli niestandardowej można używać w dwóch subskrypcjach. W tym przykładzie użyto pliku JSON jako danych wejściowych.

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

Aby zaktualizować rolę niestandardową, należy najpierw użyć [listy definicji roli az,](/cli/azure/role/definition#az-role-definition-list) aby pobrać definicję roli. Po drugie, należy wprowadzić żądane zmiany w definicji roli. Na koniec użyj [aktualizacji definicji roli az,](/cli/azure/role/definition#az-role-definition-update) aby zapisać zaktualizowaną definicję roli.

```azurecli
az role definition update --role-definition <role_definition>
```

W poniższym przykładzie dodano *microsoft.Insights/diagnosticSettings/* operation `Actions` `AssignableScopes` to and adds a management group to for the *Virtual Machine Operator* custom role. Dodawanie grupy zarządzania do `AssignableScopes` jest obecnie w wersji zapoznawczej.

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
    "/subscriptions/33333333-3333-3333-3333-333333333333",
    "/providers/Microsoft.Management/managementGroups/marketing-group"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Usuwanie roli niestandardowej

Aby usunąć rolę niestandardową, użyj usuń [definicji roli az](/cli/azure/role/definition#az-role-definition-delete). Aby określić rolę do usunięcia, użyj nazwy roli lub identyfikatora roli. Aby określić identyfikator roli, użyj [listy definicji roli az](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

W poniższym przykładzie usuwa rolę niestandardową *operatora maszyny wirtualnej.*

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Tworzenie roli niestandardowej dla zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](tutorial-custom-role-cli.md)
- [Niestandardowe role dla zasobów platformy Azure](custom-roles.md)
- [Operacje dostawcy zasobów usługi Azure Resource Manager](resource-provider-operations.md)