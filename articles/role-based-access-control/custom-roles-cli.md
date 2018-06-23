---
title: Tworzenie niestandardowych ról przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia niestandardowych ról dla kontroli dostępu opartej na rolach (RBAC) przy użyciu wiersza polecenia platformy Azure. W tym jak listy, tworzenie, aktualizowanie i usuwanie ról niestandardowych.
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
ms.openlocfilehash: b1df50c73497e3f5ad64a7ba7210079871b155e0
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321157"
---
# <a name="create-custom-roles-using-azure-cli"></a>Tworzenie niestandardowych ról przy użyciu wiersza polecenia platformy Azure

Jeśli [wbudowane role](built-in-roles.md) nie spełnienia specyficznych potrzeb Twojej organizacji, możesz utworzyć własne niestandardowe role. W tym artykule opisano sposób tworzenia i zarządzania nimi role niestandardowe przy użyciu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć role niestandardowe, należy:

- Uprawnienia do tworzenia ról niestandardowych, takich jak [właściciela](built-in-roles.md#owner) lub [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator)
- [Azure CLI](/cli/azure/install-azure-cli) zainstalowane lokalnie

## <a name="list-custom-roles"></a>Lista ról niestandardowych

Aby wyświetlić listę ról niestandardowych, które są dostępne do przypisania, należy użyć [listy definicji roli az](/cli/azure/role/definition#az-role-definition-list). Poniższe przykłady listę ról niestandardowych w bieżącej subskrypcji.

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

## <a name="create-a-custom-role"></a>Tworzenie niestandardowej roli zabezpieczeń

Aby utworzyć niestandardową rolę, użyj [utworzenia definicji roli az](/cli/azure/role/definition#az-role-definition-create). Definicja roli może być opisu JSON lub ścieżka do pliku zawierającego opis JSON.

```azurecli
az role definition create --role-definition <role_definition>
```

W poniższym przykładzie tworzona rola niestandardowa o nazwie *Operator maszyny wirtualnej*. Tę rolę niestandardową przypisuje dostęp do wszystkich operacji odczytu z *Microsoft.Compute*, *Microsoft.Storage*, i *Microsoft.Network* dostęp do dostawcy i przypisuje zasobów Aby rozpocząć, uruchom ponownie i monitorowania maszyn wirtualnych. Tę rolę niestandardową można w dwóch subskrypcji. W tym przykładzie używany jest plik JSON jako danych wejściowych.

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

## <a name="update-a-custom-role"></a>Aktualizacja niestandardowej roli zabezpieczeń

Aby zaktualizować niestandardowej roli zabezpieczeń, należy najpierw użyć [listy definicji roli az](/cli/azure/role/definition#az-role-definition-list) można pobrać definicji roli. Po drugie wprowadź żądane zmiany do definicji roli. Na koniec użyj [aktualizacji definicji roli az](/cli/azure/role/definition#az-role-definition-update) można zapisać definicji roli zaktualizowane.

```azurecli
az role definition update --role-definition <role_definition>
```

W poniższym przykładzie dodano *Microsoft.Insights/diagnosticSettings/* operacji *akcje* z *Operator maszyny wirtualnej* niestandardowej roli zabezpieczeń.

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

## <a name="delete-a-custom-role"></a>Usunięcia niestandardowej roli zabezpieczeń

Aby usunąć niestandardową rolę, użyj [usunąć definicji roli az](/cli/azure/role/definition#az-role-definition-delete). Aby określić roli do usunięcia, użyj nazwy roli lub identyfikator roli. Aby określić identyfikator roli, należy użyć [listy definicji roli az](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

W następującym przykładzie *Operator maszyny wirtualnej* niestandardowej roli zabezpieczeń.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Kolejne kroki

- [Samouczek: Tworzenie niestandardowej roli zabezpieczeń przy użyciu wiersza polecenia platformy Azure](tutorial-custom-role-cli.md)
- [Role niestandardowe na platformie Azure](custom-roles.md)
- [Operacje dostawcy zasobów Menedżera zasobów platformy Azure](resource-provider-operations.md)