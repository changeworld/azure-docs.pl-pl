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
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b768f6e240c354369246a6d978ed3e8dd2f58f92
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338142"
---
# <a name="create-custom-roles-for-azure-resources-using-azure-cli"></a>Tworzenie ról niestandardowych dla zasobów platformy Azure przy użyciu wiersza polecenia platformy Azure

Jeśli [wbudowane role zasobów platformy Azure](built-in-roles.md) nie spełnienia specyficznych potrzeb swojej organizacji, możesz utworzyć własne niestandardowe role. W tym artykule opisano, jak tworzyć i zarządzać nimi role niestandardowe przy użyciu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć niestandardowe role, potrzebne są:

- Uprawnienia do tworzenia ról niestandardowych, takie jak [Właściciel](built-in-roles.md#owner) lub [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) zainstalowany lokalnie

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
- [Role niestandardowe dla zasobów platformy Azure](custom-roles.md)
- [Operacje dostawcy zasobów w usłudze Azure Resource Manager](resource-provider-operations.md)