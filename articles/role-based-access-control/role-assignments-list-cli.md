---
title: Lista przypisań ról przy użyciu narzędzia Azure RBAC i interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak określić, do jakich zasobów użytkownicy, grupy, jednostki usług lub tożsamości zarządzane mają dostęp do przy użyciu kontroli dostępu opartej na rolach platformy Azure (RBAC) i interfejsu wiersza polecenia platformy Azure.
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
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5716e7bb89d017866bd1575256e2d119bb7acbe5
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385065"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>Lista przypisań ról przy użyciu narzędzia Azure RBAC i interfejsu wiersza polecenia platformy Azure

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]W tym artykule opisano sposób listy przypisań ról przy użyciu interfejsu wiersza polecenia platformy Azure.

> [!NOTE]
> Jeśli twoja organizacja zleciła funkcje zarządzania na zewnątrz dostawcy usług, który korzysta z [usługi Azure zarządzanie zasobami delegowanymi,](../lighthouse/concepts/azure-delegated-resource-management.md)przypisania ról autoryzowane przez tego dostawcę usług nie będą wyświetlane w tym miejscu.

## <a name="prerequisites"></a>Wymagania wstępne

- [Bash w usłudze Azure Cloud Shell](/azure/cloud-shell/overview) lub [azure cli](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Tworzenie listy przypisań ról dla użytkownika

Aby wyświetlić listę przypisań ról dla określonego użytkownika, użyj [listy przypisań ról az:](/cli/azure/role/assignment#az-role-assignment-list)

```azurecli-interactive
az role assignment list --assignee <assignee>
```

Domyślnie będą wyświetlane tylko przypisania ról dla bieżącej subskrypcji. Aby wyświetlić przypisania ról dla bieżącej `--all` subskrypcji i poniżej, dodaj parametr. Aby wyświetlić przypisania odziedziczonych `--include-inherited` ról, dodaj parametr.

W poniższym przykładzie wymieniono przypisania ról, które są przypisane bezpośrednio do *użytkownika\@patlong contoso.com:*

```azurecli-interactive
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
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

## <a name="list-role-assignments-for-a-resource-group"></a>Tworzenie listy przypisań ról dla grupy zasobów

Aby wyświetlić listę przypisań ról istniejących w zakresie grupy zasobów, użyj [listy przypisań ról az:](/cli/azure/role/assignment#az-role-assignment-list)

```azurecli-interactive
az role assignment list --resource-group <resource_group>
```

W poniższym przykładzie wymieniono przypisania ról dla grupy zasobów *sprzedaży farmacji:*

```azurecli-interactive
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
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

## <a name="list-role-assignments-for-a-subscription"></a>Tworzenie listy przypisań ról dla subskrypcji

Aby wyświetlić listę wszystkich przypisań ról w zakresie subskrypcji, użyj [listy przypisania ról az](/cli/azure/role/assignment#az-role-assignment-list). Aby uzyskać identyfikator subskrypcji, można go znaleźć w bloku **Subskrypcje** w witrynie Azure portal lub można użyć [listy kont AZ](/cli/azure/account#az-account-list).

```azurecli-interactive
az role assignment list --subscription <subscription_name_or_id>
```

Przykład:

```azurecli-interactive
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>Wyświetlanie przypisań ról dla grupy zarządzania

Aby wyświetlić listę wszystkich przypisań ról w zakresie grupy zarządzania, należy użyć [listy przypisań ról az](/cli/azure/role/assignment#az-role-assignment-list). Aby uzyskać identyfikator grupy zarządzania, można go znaleźć w bloku **Grupy zarządzania** w witrynie Azure portal lub można użyć [listy grup zarządzania kontem AZ](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Przykład:

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Wyświetlanie przypisań ról dla tożsamości zarządzanej

1. Pobierz identyfikator obiektu tożsamości zarządzanej przypisanej do systemu lub przypisanej przez użytkownika.

    Aby uzyskać identyfikator obiektu tożsamości zarządzanej przypisanej przez użytkownika, możesz użyć [listy az ad sp](/cli/azure/ad/sp#az-ad-sp-list) lub listy tożsamości [az](/cli/azure/identity#az-identity-list).

    ```azurecli-interactive
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    Aby uzyskać identyfikator obiektu tożsamości zarządzanej przypisanej do systemu, można użyć [listy az ad sp](/cli/azure/ad/sp#az-ad-sp-list).

    ```azurecli-interactive
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. Aby wyświetlić listę przypisań ról, użyj [listy przypisań ról az](/cli/azure/role/assignment#az-role-assignment-list).

    Domyślnie będą wyświetlane tylko przypisania ról dla bieżącej subskrypcji. Aby wyświetlić przypisania ról dla bieżącej `--all` subskrypcji i poniżej, dodaj parametr. Aby wyświetlić przypisania odziedziczonych `--include-inherited` ról, dodaj parametr.

    ```azurecli-interactive
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>Następne kroki

- [Dodawanie lub usuwanie przypisań ról przy użyciu narzędzia Azure RBAC i interfejsu wiersza polecenia platformy Azure](role-assignments-cli.md)
